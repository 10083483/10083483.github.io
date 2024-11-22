```markdown
---
title: "Illegal Instruction Error 해결 방법"
date: 2024-08-12
categories: Python, PyTorch
tags: PyTorch Python Error
---

에러 메시지:

```bash
"Illegal instruction" error
```
---

해결 방법: PyTorch를 ARM64 아키텍처에서 직접 빌드하여 문제를 해결할 수 있습니다.

```bash
# 시스템 업데이트 및 필수 패키지 설치
sudo apt-get update
sudo apt-get install -y build-essential cmake
sudo apt-get install -y python3-dev python3-pip
sudo apt-get install -y libopenblas-dev libblas-dev m4 cmake cython3 libatlas-base-dev

# 필요한 Python 패키지 설치
pip3 install numpy pyyaml mkl mkl-include setuptools cmake cffi typing_extensions future six requests dataclasses

# PyTorch 소스코드 클론 및 설정
git clone --recursive https://github.com/pytorch/pytorch
cd pytorch

# ARM64 아키텍처를 위한 환경 변수 설정
export BUILD_CAFFE2=0
export USE_OPENCV=1
export USE_NNPACK=0
export USE_QNNPACK=0

# PyTorch 빌드 및 설치
python3 setup.py install
```
---

오류 원인:  
ARM64 아키텍처에서 PyTorch의 사전 빌드 바이너리가 호환되지 않아 **Illegal Instruction** 오류가 발생했습니다. 소스에서 직접 빌드하면 이 문제를 해결할 수 있습니다.
```