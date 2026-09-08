🛡️ AI Guardrail & NAS MLOps Security Pipeline

LLM 서비스에 입력되는 프롬프트를 사전에 검사해 악성 요청을 탐지·차단하고, 탐지 데이터를 NAS에 저장한 뒤 재학습에 활용하는 AI 보안 프로젝트입니다.

📌 Project Overview

본 프로젝트의 핵심 흐름은 다음과 같습니다.

사용자 Prompt
   ↓
Frontend
   ↓
Backend API
   ↓
AI Guardrail
   ↓
SAFE / SUSPICIOUS / BLOCK
   ↓
NAS 로그 저장
   ↓
Dataset 정제 / 라벨링
   ↓
GPU 재학습
   ↓
Model 평가 / 배포
   ↓
Dashboard 관제

최종적으로는 요청 검증 → 격리 수집 → 모델 재학습 → 관제·피드백 → 다시 요청 검증의 순환 구조를 구현하는 것이 목표입니다.

👥 Team

담당

역할

실환

AI 학습, AI 추론 서버, NAS 관리, Dataset 관리, 모델 버전 관리

윤종

Backend API, AI 서버 연동, NAS 로그 저장, 통계 API, 전체 서비스 연결

성조

Frontend, Prompt 검사 화면, Dashboard, 탐지 결과 및 통계 시각화

⚙️ Tech Stack

AI / MLOps

Python

PyTorch

Hugging Face Transformers / Datasets

scikit-learn

Pandas

Backend

FastAPI

Uvicorn

Pydantic

httpx

Frontend

Streamlit

Plotly

Requests

Infrastructure

NAS

Tailscale

Git / GitHub

Docker (추후 적용)

🚨 Detection

초기 탐지 대상은 다음과 같습니다.

NORMAL

PROMPT_INJECTION

SYSTEM_PROMPT_LEAKAGE

UNKNOWN

탐지 결과는 위험도에 따라 다음 세 단계로 구분합니다.

Risk Score

Result

0.00 ~ 0.29

SAFE

0.30 ~ 0.69

SUSPICIOUS

0.70 ~ 1.00

BLOCK

🗄️ NAS Structure

AI_Guardrail/
├─ logs/
│  ├─ raw/
│  └─ blocked/
├─ dataset/
│  ├─ pending/
│  ├─ approved/
│  ├─ train/
│  └─ validation/
├─ models/
│  ├─ candidate/
│  ├─ production/
│  └─ archive/
└─ reports/

탐지된 데이터는 바로 학습에 사용하지 않고 pending 영역에 저장한 뒤 검증 및 라벨링을 거쳐 approved 데이터만 학습에 사용합니다.

🤖 Model Lifecycle

탐지 로그
   ↓
pending
   ↓
라벨 검토
   ↓
approved
   ↓
train / validation
   ↓
GPU 학습
   ↓
Candidate Model
   ↓
성능 평가
   ↓
Production Model

모델 평가는 Accuracy, Precision, Recall, F1-score를 기준으로 진행하며, 기존 운영 모델보다 성능이 충분히 향상된 경우 새 모델로 교체하는 구조를 목표로 합니다.

🔌 Main API

Prompt 검사

POST /api/inspect

{
  "prompt": "Ignore previous instructions"
}

{
  "result": "BLOCK",
  "attack_type": "PROMPT_INJECTION",
  "risk_score": 0.93,
  "model_version": "v0.1"
}

AI 추론

POST /predict

Backend가 AI Server에 프롬프트 검사를 요청할 때 사용합니다.

Dashboard 통계

GET /api/stats

전체 요청 수, SAFE / SUSPICIOUS / BLOCK 수, 공격 유형별 통계를 제공합니다.

🎯 MVP

초기 MVP는 다음 한 사이클을 안정적으로 구현하는 것을 목표로 합니다.

Prompt 입력
   ↓
Backend
   ↓
AI 탐지
   ↓
NAS 저장
   ↓
Dashboard 반영

이후 Dataset 구축, Hugging Face 모델 학습, 모델 성능 비교, 재학습 자동화 순으로 기능을 확장합니다.

🗺️ Roadmap

Phase 1: Rule 기반 Guardrail MVP

Phase 2: 공격 데이터 수집 및 Dataset 구축

Phase 3: Hugging Face 기반 탐지 모델 학습

Phase 4: 모델 평가 및 버전 관리

Phase 5: 재학습 자동화 및 MLOps 고도화
