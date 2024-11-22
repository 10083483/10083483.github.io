---
title: "SyntaxError: Non-ASCII character 해결 방법"
date: 2024-08-19
categories: Python, SyntaxError, YOLOv5
tags: Python Error Encoding YOLOv5
---

## 에러 메시지:

```bash
SyntaxError: Non-ASCII character '\xf0' in file ../yolov5/detect.py on line 1, but no encoding declared; see http://python.org/dev/peps/pep-0263/ for details
```
---

## 해결 방법: `detect.py` 파일의 첫 번째 줄을 삭제하여 문제를 해결했습니다.

```bash
# detect.py 파일에서 첫 번째 줄 삭제
# -*- coding: ascii -*- 줄 추가는 효과가 없었습니다.
```
---

## 오류 원인:  
YOLOv5를 사용하는 과정에서 `detect.py` 파일의 첫 번째 줄에 비 ASCII 문자가 포함되어 있었으나, Python이 해당 파일의 문자 인코딩을 인식하지 못하여 발생한 문제입니다. 파일의 첫 번째 줄을 삭제하여 비 ASCII 문자를 제거하거나 명시적인 인코딩 선언을 수정해야 합니다.
