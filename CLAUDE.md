# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 목적

`눌림목매매2`는 **순수 리뷰 도구**다.
- `pwman111-debuge/nulliummok-maemae` 저장소에 쌓인 발굴 리포트를 읽고 분석한다.
- 보고서 생성(발굴)은 수행하지 않는다. 발굴은 `눌림목매매` 프로젝트에서 담당한다.

---

## 핵심 명령어

| 명령어 | 동작 |
|--------|------|
| **"제네시스 하자"** | 아래 "리뷰 에이전트 실행 절차" 따름 |

---

## 리뷰 에이전트 실행 절차

**"제네시스 하자"** 명령 시 반드시 아래를 순서대로 실행한다.

### 1. 에이전트 정의 파일 로드
```
agents/review-agent.md 파일을 Read한다.
```

### 2. 파일에 정의된 절차 실행
`review-agent.md`에 기술된 STEP 1 ~ STEP 3을 순서대로 실행한다.

---

## 에이전트 파일 구조

```
눌림목매매2/
├── CLAUDE.md
└── agents/
    └── review-agent.md    ← 리뷰 에이전트: 기존 리포트 분석 + 향후 대응방안 제시
```

향후 에이전트 추가 시 `agents/` 폴더에 파일을 추가하고, 이 파일에 명령어와 실행 절차를 등록한다.

---

## GitHub 동기화 원칙

**저장소**: `https://github.com/pwman111-debuge/mullimmokmaemae2` (remote: `origin`)

### 즉시 푸시 규칙 (예외 없음)

아래 두 경우 모두 **작업 완료 즉시** 푸시한다. 확인이나 별도 지시를 기다리지 않는다.

**① 리뷰 리포트가 생성된 경우**
**② 코드(에이전트 파일·설정 파일 포함)가 변경된 경우**

```bash
git add <수정된 파일>
git commit -m "feat/fix: 변경 내용 요약"
git push origin main
```

대상 파일:
- `CLAUDE.md`
- `agents/` 내 모든 파일
- `workflows/reviews/` 리뷰 리포트

---

## 운영 원칙

- 데이터는 `pwman111-debuge/nulliummok-maemae` GitHub 리포트에서만 읽는다 — 추정값·기억값 금지
- 최종 판단과 매수 결정권은 **황원장**에게 있다
- **프로젝트 격리 원칙**: 이 폴더는 독립적으로 유지한다. 외부 폴더의 코드를 이 폴더로 복사하거나 이동하지 않는다.
