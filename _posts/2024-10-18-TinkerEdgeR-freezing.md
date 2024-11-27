---
title: "Rockchip SoC 기반 OpenCV와 TensorFlow Lite 환경에서 발생한 프로그램 멈춤 문제 해결기"
date: 2024-10-18
categories: OpenCV TensorFlow_Lite GUI
tags: Rockchip Pygame Tkinter GPU 하드웨어가속 SegmentationFault TinkerEdgeR Freezing
---

# Rockchip SoC 기반 OpenCV와 TensorFlow Lite 환경에서 발생한 프로그램 멈춤 문제 해결기

Rockchip SoC 기반 시스템에서 OpenCV와 TensorFlow Lite를 활용하여 비디오 처리 애플리케이션을 개발하던 중, 프로그램이 **짧게는 10분, 길게는 2시간** 내에 **화면이 멈추는 문제**가 발생했습니다. 에러 메시지가 없거나, 간혹 다음과 같은 에러가 나타났습니다:

- `Segmentation Fault`
- `double free or corruption (!prev)`

시스템 로그와 리소스 사용률을 확인한 결과, **과열**, **메모리 누수**, **CPU 과부하**, **디스크 I/O 문제** 등의 가능성은 낮았습니다. 이에 따라 다양한 원인을 추측하고 해결 방안을 모색하였습니다.

---

## 1. 시스템 상태 점검

### **1.1. 리소스 사용률 모니터링**

프로그램 실행 중 시스템의 리소스 사용률을 주기적으로 기록하였습니다:

```
CPU Usage: 39.6%, Memory Usage: 17.9%, Disk Usage: 27.0%, CPU Temperature: 41.1°C, Time: 2024-10-18 11:04:00
CPU Usage: 38.0%, Memory Usage: 18.1%, Disk Usage: 27.0%, CPU Temperature: 41.7°C, Time: 2024-10-18 11:04:01
...
```

- **CPU 온도**가 안정적으로 유지되므로 **과열로 인한 문제 가능성은 낮음**.
- **메모리 사용량**이 일정하므로 **메모리 누수 가능성도 낮음**.
- **CPU 사용률**이 높지 않아 **CPU 과부하 가능성도 낮음**.
- **디스크 사용률**이 일정하여 **디스크 I/O 문제 가능성도 낮음**.

### **1.2. 로그 분석**

프로그램이 멈추거나 종료될 때의 로그를 분석한 결과, 특정 라이브러리나 함수에서 오류가 발생한 흔적은 찾을 수 없었습니다.

---

## 2. 문제 원인 추측 및 시도한 해결 방법

### **2.1. 네이티브 라이브러리의 메모리 관리 이슈**

- **OpenCV**, **TensorFlow Lite**와 같은 네이티브 라이브러리에서 메모리 관리 문제가 있을 가능성을 고려했습니다.
- 이러한 라이브러리의 버전을 변경하거나 재설치하여 문제를 해결하려고 시도했습니다.

### **2.2. 하드웨어 가속 라이브러리와의 호환성 문제**

- 로그에서 **librga**, **MPP** 관련 메시지가 나타나 **하드웨어 가속 라이브러리**와의 충돌 가능성을 의심했습니다.
- OpenCV를 하드웨어 가속 기능 없이 빌드하거나, 관련 라이브러리의 버전을 조정해 보았습니다.

### **2.3. 라이브러리 빌드 설정 문제**

- OpenCV나 TensorFlow Lite를 빌드할 때 사용된 설정이나 옵션이 현재 시스템과 호환되지 않을 수 있다고 판단했습니다.
- 빌드 옵션을 수정하고 다시 빌드하여 문제를 해결하려고 노력했습니다.

### **2.4. 비디오 파일 코덱 문제**

- 비디오 파일을 소프트웨어 코덱으로 재인코딩하여 사용해 보았습니다:

  ```bash
  ffmpeg -i "input_video.mp4" -c:v libx264 -crf 23 output_video.mp4
  ```

- 그러나 재인코딩된 파일로 실행해도 문제는 해결되지 않았습니다.

---

## 3. 최종 해결 방법: GUI 라이브러리 변경

여러 가지 시도를 했음에도 문제가 해결되지 않아, **GUI 라이브러리**에 문제가 있을 수 있다고 판단했습니다. 기존에는 **Tkinter**를 사용하고 있었는데, 이를 **Pygame**으로 변경해 보기로 했습니다.

### **3.1. Pygame으로 변경 후 결과**

- Pygame으로 GUI를 구현한 후 프로그램을 실행한 결과, **더 이상 프로그램이 멈추지 않았습니다**.
- **Segmentation Fault**나 **double free or corruption** 오류도 발생하지 않았습니다.

---

## 4. Tkinter와 Pygame의 차이점 분석 및 문제 해결 이유

### **4.1. Tkinter 사용 시 발생 가능 문제**

1. **CPU 기반 렌더링**

   - Tkinter는 CPU 기반의 렌더링 방식을 사용하며, GPU를 활용하지 않습니다.
   - Rockchip SoC의 하드웨어 가속 라이브러리(MPP, RGA)와의 **호환성 문제가 발생**했을 가능성이 있습니다.

2. **이벤트 처리 구조의 복잡성**

   - Tkinter는 복잡한 위젯 계층과 이벤트 큐를 관리합니다.
   - OpenCV와 함께 실시간 비디오 스트림을 처리하는 과정에서 **이벤트 루프의 복잡성이 충돌**을 일으켰을 수 있습니다.

### **4.2. Pygame으로 변경 후 개선점**

1. **GPU 활용**

   - Pygame은 SDL(Simple DirectMedia Layer) 기반으로 **GPU 가속을 지원**합니다.
   - 하드웨어 가속을 사용하는 OpenCV와의 **호환성이 높아져 충돌이 감소**했습니다.

2. **단순한 이벤트 루프**

   - Pygame은 단순한 프레임 기반 업데이트 방식을 사용하여 **이벤트 처리가 간단**합니다.
   - OpenCV와 함께 실시간 비디오 스트림을 처리하는 데 적합합니다.

---

## 5. Pygame을 사용한 코드 예시

다음은 Pygame과 OpenCV를 사용하여 비디오를 표시하는 코드 예시입니다:

```python
import cv2
import pygame
import numpy as np
import sys

# Pygame 초기화
pygame.init()

# 비디오 캡처
video_path = "output_video.mp4"
cap = cv2.VideoCapture(video_path)
if not cap.isOpened():
    print("Error: Unable to open video file.")
    sys.exit()

# 비디오 프레임 크기 가져오기
frame_width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
frame_height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))

# Pygame 창 설정
screen = pygame.display.set_mode((frame_width, frame_height))
pygame.display.set_caption("Video Stream")

clock = pygame.time.Clock()
running = True
while running:
    ret, frame = cap.read()
    if not ret:
        break

    # OpenCV에서 읽은 프레임을 Pygame에서 렌더링하기 위해 변환
    frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    frame = np.rot90(frame)  # 필요에 따라 이미지 회전
    frame_surface = pygame.surfarray.make_surface(frame)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.blit(frame_surface, (0, 0))
    pygame.display.update()
    clock.tick(30)  # 초당 30프레임으로 설정

cap.release()
pygame.quit()
```

---

## 6. 결론 및 교훈

여러 가지 시도를 통해 문제를 해결하려고 노력했지만, **GUI 라이브러리를 Tkinter에서 Pygame으로 변경**한 것이 결정적인 해결책이었습니다.

**문제 해결 이유**:

- **Tkinter의 CPU 기반 렌더링이 하드웨어 가속 라이브러리와 충돌**을 일으켰을 가능성이 높습니다.
- **Pygame은 GPU 가속을 지원**하여 OpenCV와의 호환성이 향상되었습니다.
- 단순한 이벤트 루프 구조로 인해 **프로그램의 안정성이 높아졌습니다**.

**교훈**:

- 하드웨어 가속을 사용하는 시스템에서는 **GUI 라이브러리의 선택이 매우 중요**합니다.
- **GPU 가속을 지원하는 Pygame**은 OpenCV와의 통합에서 안정성과 성능 면에서 유리합니다.
- 문제가 발생할 때는 **다양한 가능성을 열어두고 접근하는 것이 중요**합니다.

---

## 7. 추가 고려 사항 및 팁

- **라이브러리 버전 관리**:

  - OpenCV, TensorFlow Lite 등 주요 라이브러리의 버전이 시스템과 호환되는지 확인합니다.

- **하드웨어 가속 라이브러리 업데이트**:

  - 최신 버전의 MPP, RGA 라이브러리를 사용하여 호환성 문제를 줄일 수 있습니다.

- **에러 로그 모니터링**:

  - 프로그램 실행 중 로그를 주기적으로 확인하여 잠재적인 문제를 조기에 발견합니다.

---

## 8. 참고 자료

- [Pygame 공식 문서](https://www.pygame.org/docs/)
- [OpenCV-Python 튜토리얼](https://docs.opencv.org/4.x/d6/d00/tutorial_py_root.html)
- [Rockchip MPP 개발자 가이드](https://opensource.rock-chips.com/wiki_Mpp)
- [Tkinter vs Pygame 비교](https://stackoverflow.com/questions/20842801/python-tkinter-vs-pygame)
