---
title: "팅커 보드 검은 화면 에러 해결 방법"
date: 2024-09-02
categories: TinkerEdgeR, Display
tags: TinkerEdgeR Error Display GUI
---

## 에러 메시지:

```bash
GUI may not be starting correctly issue with the display manager.
```
---

## 해결 방법:

1. Ctrl + Alt + F1을 눌러 터미널 모드로 전환합니다.
2. 로그인한 후 아래 명령어로 GUI를 수동으로 시작합니다.

   ```bash
   startx
   ```
---

## 오류 원인:  
팅커 보드의 디스플레이 매니저가 올바르게 작동하지 않거나 GUI가 자동으로 시작되지 않아 발생한 문제입니다. 수동으로 `startx` 명령어를 실행하여 GUI를 시작하면 문제를 해결할 수 있습니다.