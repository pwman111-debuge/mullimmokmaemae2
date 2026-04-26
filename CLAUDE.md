# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 핵심 명령어

| 명령어 | 동작 |
|--------|------|
| **"리뷰 하자"** | 아래 "리뷰 에이전트 실행 절차" 따름 |
| **"발굴 하자"** | 아래 "발굴 에이전트 실행 절차" 따름 |
| **"제네시스 하자"** | "리뷰 하자" → "발굴 하자" 순서로 연속 실행 |

---

## 리뷰 에이전트 실행 절차

**"리뷰 하자"** 명령 시 반드시 아래를 순서대로 실행한다.

### 1. 에이전트 정의 파일 로드
```
agents/review-agent.md 파일을 Read한다.
```

### 2. 파일에 정의된 절차 실행
`review-agent.md`에 기술된 STEP 1 ~ STEP 3을 순서대로 실행한다.

---

## 발굴 에이전트 실행 절차

**"발굴 하자"** 명령 시 반드시 아래를 순서대로 실행한다.

### 1. 에이전트 정의 파일 로드
```
agents/report-agent.md 파일을 Read한다.
```

### 2. 파일에 정의된 절차 실행
`report-agent.md`에 기술된 STEP 1 ~ STEP 6을 순서대로 실행한다.

---

## 에이전트 파일 구조

```
눌림목매매2/
├── CLAUDE.md
└── agents/
    ├── review-agent.md    ← 리뷰 에이전트: 기존 리포트 분석 + 향후 대응방안 제시
    └── report-agent.md    ← 발굴 에이전트: 전체 파이프라인 실행 + 신규 리포트 생성
```

향후 에이전트 추가 시 `agents/` 폴더에 파일을 추가하고, 이 파일에 명령어와 실행 절차를 등록한다.

---

## GitHub 동기화 원칙

**저장소**: `https://github.com/pwman111-debuge/mullimmokmaemae2` (remote: `origin`)

이 레포의 파일이 수정·추가된 경우, 작업 완료 즉시 아래 명령으로 푸시한다:

```bash
git add <수정된 파일>
git commit -m "feat/fix: 변경 내용 요약"
git push origin main
```

대상 파일:
- `CLAUDE.md`
- `agents/` 내 모든 파일
- `workflows/reports/` 발굴 리포트

---

## 운영 원칙

- 데이터는 네이버 증권 및 GitHub 리포트에서만 읽는다 — 추정값·기억값 금지
- 최종 판단과 매수 결정권은 **황원장**에게 있다
- **프로젝트 격리 원칙**: 이 폴더는 독립적으로 유지한다. 외부 폴더(`../눌림목매매/` 등)의 코드를 이 폴더로 복사하거나 이동하지 않는다. 필요한 스크립트는 이 프로젝트 안에서 새로 작성한다.
