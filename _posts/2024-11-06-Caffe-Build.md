---
title: "Caffe 빌드 환경 구성과 의존성 확인"
date: 2024-11-06
categories: Caffe, Build, Dependency
tags: Boost OpenCV BLAS HDF5 Build Linux jetsonNano
---

## Caffe 빌드 환경 구성 및 의존성 확인

Caffe를 성공적으로 빌드하기 위해 환경 설정 과정에서 작성한 메모를 공유합니다. 이 메모는 필요한 라이브러리 설치와 경로 확인 과정을 담고 있습니다.

---

## 환경 설정: Boost 라이브러리 빌드

Boost는 Caffe 빌드에 필수적으로 사용됩니다. Windows 환경에서 Boost 1.75.0을 빌드하는 과정은 다음과 같습니다:

```cmd
BOOST_ROOT = C:\Users\sc\Documents\boost_1_75_0
cd C:/Users/sc/Documents/boost_1_75_0
bootstrap.bat
b2 --with-system --with-thread --with-filesystem variant=release link=static threading=multi stage
```

- **`BOOST_ROOT`**: Boost 라이브러리 경로를 설정.
- **`bootstrap.bat`**: Boost 빌드 초기화.
- **`b2`**: 필요한 모듈(system, thread, filesystem)을 정적(static) 릴리즈(release) 버전으로 빌드.

---

## 의존성 확인: OpenCV, BLAS, HDF5

Caffe를 빌드하기 위해 필요한 주요 의존성의 헤더 파일 경로를 확인합니다.

### OpenCV 헤더 확인
OpenCV가 제대로 설치되었는지 확인하려면 다음 명령어를 실행합니다:

```bash
ls /usr/include/opencv4/opencv2/core/core.hpp
```

출력:
```bash
/usr/include/opencv4/opencv2/core/core.hpp
```

### BLAS(cblas.h) 헤더 확인
BLAS 라이브러리가 설치되었는지 확인하려면 다음 명령어를 실행합니다:

```bash
find /usr/include -name "cblas.h"
```

출력:
```bash
/usr/include/x86_64-linux-gnu/cblas.h
/usr/include/x86_64-linux-gnu/openblas-pthread/cblas.h
```

### HDF5(hdf5.h) 헤더 확인
HDF5 라이브러리가 설치되었는지 확인하려면 다음 명령어를 실행합니다:

```bash
find /usr/include -name "hdf5.h"
```

출력:
```bash
/usr/include/hdf5/serial/hdf5.h
/usr/include/opencv4/opencv2/flann/hdf5.h
```

---

## 오류 원인:
Caffe 빌드 중 발생할 수 있는 오류의 원인은 다음과 같습니다:
- Boost 빌드 실패: 환경 변수(`BOOST_ROOT`) 설정이 잘못되었거나, Boost 컴파일이 완료되지 않았을 수 있습니다.
- OpenCV 경로 누락: CMake 단계에서 OpenCV 경로가 인식되지 않을 수 있습니다.
- BLAS 또는 HDF5 누락: 시스템에 관련 헤더 또는 라이브러리가 설치되지 않았을 수 있습니다.

---

## 해결 방법:
1. **Boost 재설치**:
   - Boost를 다시 빌드하고 환경 변수를 확인하세요.
   ```cmd
   cd C:/Users/sc/Documents/boost_1_75_0
   b2 --clean
   b2 --with-system --with-thread --with-filesystem variant=release link=static threading=multi stage
   ```

2. **OpenCV 경로 확인 및 설정**:
   - OpenCV가 설치된 경로를 명확히 설정합니다:
   ```bash
   export OpenCV_DIR=/usr/include/opencv4
   ```

3. **BLAS/HDF5 설치**:
   - 필요한 라이브러리를 설치합니다:
   ```bash
   sudo apt-get install libopenblas-dev libhdf5-dev
   ```

---

## 마무리:
이 메모는 Caffe 빌드 시 환경 구성 및 의존성 확인에 유용합니다. 위 설정을 기반으로 빌드를 진행하고, 추가적인 문제가 발생하면 빌드 로그를 분석하여 필요한 경로를 수정하세요.
