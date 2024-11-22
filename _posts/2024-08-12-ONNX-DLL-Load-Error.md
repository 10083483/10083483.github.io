---
title: "ONNX DLL Load Error 해결 방법"
date: 2024-08-12
categories: Python, ONNX
tags: ONNX Python Error
---

에러 메시지:

```bash
ONNX: export failure  16.0s: DLL load failed while importing onnx_cpp2py_export: DLL
```
***

해결 방법:  특정 ONNX 버전을 설치하는 것이 도움이 됩니다
```bash
pip install onnx==1.12.0
```
---

오류 원인: 
ONNX 버전과 시스템 라이브러리의 불일치
