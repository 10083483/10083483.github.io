---
title: "OpenCV가 설치되어 있을떄 ModuleNotFoundError: No module named 'cv2' 해결 방법"
date: 2024-08-26
categories: Python, OpenCV, TinkerEdgeR
tags: Python Error OpenCV TinkerEdgeR
---

## 에러 메시지:

```bash
ModuleNotFoundError: No module named 'cv2'
```
---

## 해결 방법:

1. OpenCV 패키지 설치 확인  
   OpenCV가 설치되어 있더라도 Python 버전 불일치로 동작하지 않을 수 있습니다.  
   아래 명령어를 실행하여 Python 3.7에 맞게 OpenCV를 설치합니다.

   ```bash
   sudo python3.7 -m pip install opencv-python
   ```

2. Python3-distutils 설치  
   Python 환경에서 `pip` 관련 오류가 발생할 경우 아래 명령어로 `python3-distutils`를 설치합니다.

   ```bash
   sudo apt-get install python3-distutils
   ```
---

## 오류 원인:  
1. **Python 버전 불일치**  
   OpenCV 패키지가 Python 3.8 환경에 설치되어 있으나, 실행 중인 Python은 3.7 버전을 사용하고 있었습니다. Python은 각 버전마다 독립적인 패키지 경로를 갖기 때문에, Python 3.7 환경에서는 Python 3.8에 설치된 패키지를 인식하지 못합니다.  

   - OpenCV 설치 경로 확인: `/usr/local/lib/python3.8/site-packages`  
   - 실행 중인 Python 경로: `/usr/lib/python3.7`

2. **환경 경로 문제**  
   Python의 `sys.path`를 확인해보면, 실행 중인 Python 버전(3.7)의 경로만 포함되어 있습니다. 따라서 OpenCV가 설치된 Python 3.8 경로가 포함되지 않아 `cv2` 모듈을 찾을 수 없었습니다.

   ```python
   >>> import sys
   >>> print(sys.path)
   ['', '/usr/lib/python37.zip', '/usr/lib/python3.7', '/usr/lib/python3.7/lib-dynload', '/home/linaro/.local/lib/python3.7/site-packages', '/usr/local/lib/python3.7/dist-packages', '/usr/lib/python3/dist-packages']
   ```

3. **Python 설치 환경 구성 문제**  
   일부 Linux 배포판에서는 Python3-distutils 패키지가 기본적으로 설치되어 있지 않아 `pip` 명령이 정상적으로 동작하지 않을 수 있습니다. 이로 인해 추가적인 설치 작업이 필요합니다.
