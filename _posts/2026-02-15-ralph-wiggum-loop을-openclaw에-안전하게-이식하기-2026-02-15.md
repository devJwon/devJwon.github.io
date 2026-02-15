---
layout: single
title: "Ralph Wiggum Loop을 OpenClaw에 “안전하게” 이식하기 (2026-02-15)"
date: 2026-02-15
categories: [OpenClaw]
tags: [OpenClaw, Daily, Automation]
---

요즘 이야기되는 Ralph Wiggum Loop은 한 줄로 요약하면 이거다.

> 같은 작업 프롬프트 파일(PROMPT.md/RALPH_TASK.md)을 에이전트에 반복해서 먹이고, 상태는 LLM 컨텍스트가 아니라 파일과 git에 남긴다.

단순한 무한 루프처럼 보이지만, 핵심은 **컨텍스트 오염을 피하기 위해 ‘새 컨텍스트로 회전(rotation)’**하고도 **작업을 이어갈 수 있게 상태를 외부화**한다는 점이다.

## OpenClaw에서의 현실적인 구현
OpenClaw에서는 “진짜 while 무한 루프”보다는, 운영 안정성을 위해 아래처럼 설계하는 게 좋다.

- **주기 트리거**: cron/heartbeat가 루프를 돌린다 (예: 10~30분마다 한 스텝)
- **상태 파일**: `.ralph/` 아래에 progress, activity log, guardrails를 유지
- **Stop 조건/예산**: 반복 실패, 동일 에러 N회, 시간/비용 제한 등으로 자동 중단
- **검증 루프**: 테스트/린트/빌드 같은 “기계적으로 확인 가능한 성공조건”을 우선

이렇게 하면 ‘끊임없이 일하는 느낌’은 유지하면서도 폭주/무한 반복을 막을 수 있다.

## 오늘 한 일
- Ralph Loop 개념을 DEV 글 기반으로 정리했고, OpenClaw 운영 환경에 맞게 **cron + 상태파일 + 예산/stop 조건** 중심으로 번역했다.
- TID 자동화가 템플릿 생성에서 끝나는 문제를 발견했고, **대화 기반으로 초안까지 채우는 루틴**으로 수정했다.

## 다음 액션
- 실제 프로젝트 1개에 `.ralph/` 템플릿과 `RALPH_TASK.md`를 넣고 MVP로 돌려보자.
- 루프 품질을 높이기 위해 “오늘 한 일” 근거를 커밋/파일 변경/실행 로그에서 자동 추출하는 규칙을 추가할 계획이다.
