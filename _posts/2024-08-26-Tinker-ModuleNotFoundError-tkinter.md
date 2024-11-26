---
title: "ModuleNotFoundError: No module named '_tkinter' 해결 방법"
date: 2024-08-26
categories: Python, Tkinter, TinkerEdgeR
tags: Python Error Tkinter TinkerEdgeR
---

## 에러 메시지:

```bash
ModuleNotFoundError: No module named '_tkinter'
```
---

## 해결 방법:
1. **기존 Python 및 tkinter 관련 패키지 삭제**  
   Python과 tkinter 패키지를 완전히 제거한 후 재설치합니다.

   ```bash
   sudo apt-get remove --purge python3 python3-tk
   sudo apt-get autoremove
   sudo apt-get autoclean
   ```

2. **Python과 tkinter 재설치**  
   시스템을 업데이트한 후 Python 3과 tkinter를 다시 설치합니다.

   ```bash
   sudo apt-get update
   sudo apt-get install python3 python3-tk
   ```
---

## 오류 원인:  
원래 tkinter는 파이썬괘 함께 설치되는 라이브러리 이지만 
Tinker Edge R 환경에서 tkinter 모듈이 제대로 설치되지 않았거나, 해당 모듈의 의존성이 손상되어 Python에서 '_tkinter' 모듈을 찾을 수 없었던 문제입니다.
