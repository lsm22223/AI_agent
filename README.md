# AI Ethics Risk Diagnostic System
본 프로젝트는 AI 윤리성 리스크를 자동으로 진단하고 개선 권고안을 생성하는 멀티 에이전트 시스템을 설계하고 구현한 실습 프로젝트입니다.

## Overview

- Objective : AI 서비스에 존재하는 윤리적 리스크(편향성, 투명성 부족, 프라이버시 침해 등)를 진단하고 국제 가이드라인 기반 개선안을 제안
- Methods : Multi-Agent Reasoning, Rule-based Evaluation, Retrieval-Augmented Generation (RAG)
- Tools : IBM Fairness360, LegalBERT, RAG with OpenAI API

## Features

- 국제 기준(EU AI Act, UNESCO) 기반 리스크 자동 분석
- RAG 기반 설명 가능한 보고서 생성
- 서비스별 윤리 리스크 등급화 및 개선안 제시

## Tech Stack 

| Category   | Details                         |
|------------|---------------------------------|
| Framework  | LangGraph, LangChain, Python    |
| LLM        | GPT-4o-mini via OpenAI API      |
| Retrieval  | FAISS, Chroma                   |

## Agents
- Agent A: `Service Analyzer` - AI 시스템 구조와 데이터 민감도 분석
- Agent B: `Risk Evaluator` - 편향성, 투명성, 프라이버시 항목별 위험도 평가
- Agent C: `Compliance Checker` - 국제 가이드라인(UNESCO, EU AI Act) 적합성 평가
- Agent D: `Report Generator` - 진단 결과 기반 보고서 자동 생성
- Agent E: `Recommendation Engine` - 실행 가능한 개선 권고안 도출

## State 
- `service_info` : 분석 대상 AI 서비스의 기능, 데이터 민감도, 사회적 영향력
- `ethical_risks` : 리스크 유형별 정량/정성 평가 결과
- `compliance_gap` : 기준 대비 미달 조항 및 리스크 매핑 결과
- `report_data` : 보고서 작성용 구조화된 종합 진단 결과
- `recommendations` : 리스크 유형별 개선 권고안 리스트

## Architecture
![image](https://github.com/user-attachments/assets/5e739f7d-9ef4-4e15-bfae-9f1037d3c166)


## Directory Structure
├── data/                  # AI 서비스 메타정보 및 샘플 진단 데이터  
├── agents/                # 에이전트 구현 모듈  
│   ├── service_analyzer.py  
│   ├── risk_evaluator.py  
│   ├── compliance_checker.py  
│   ├── report_generator.py  
│   └── recommendation_engine.py  
├── prompts/               # 프롬프트 템플릿  
├── outputs/               # 진단 보고서 및 로그  
├── app.py                 # 실행 스크립트  
└── README.md

좋습니다! 아래에 AI 윤리성 리스크 진단 시스템에서 사용하는 **`state` 정의**와 **보고서 생성 프롬프트**를 **완성 형태**로 정리해드립니다.

---

##  1. State 정의 (`state`)

멀티 에이전트에서 State의 역할

예시 시나리오 (AI 윤리 리스크 진단 시스템 기준):
Service Analyzer 에이전트가 분석 대상 AI 시스템의 정보를 수집합니다.

예: "이 시스템은 환자 데이터를 활용하는 의료 상담 챗봇이다."

이 결과는 state["service_info"]에 저장됩니다.

Risk Evaluator 에이전트는 위 정보를 기반으로 편향성, 프라이버시 등을 진단합니다.

결과는 state["ethical_risks"]로 저장

**Compliance Checker**는 service_info와 ethical_risks를 바탕으로 국제 가이드라인(EU AI Act 등)과 얼마나 일치하는지 평가

평가 결과는 state["compliance_gap"]에 저장

마지막으로 **Report Generator**는 위의 모든 state 값을 종합해 보고서를 생성합니다.


###  전체 State 구조 예시

```python
state = {
    "service_info": {
        "name": "MediScan",
        "domain": "Healthcare",
        "data_volume": 120000,
        "algorithm": "Deep Learning",
        "description": "고령자 대상 의료상담 챗봇"
    },
    "ethical_risks": {
        "bias": {
            "score": "High",
            "evidence": "성별 기반 추천 정확도 차이 23%"
        },
        "privacy": {
            "score": "Medium",
            "evidence": "데이터 암호화 기준 미준수"
        },
        "transparency": {
            "score": "Low",
            "evidence": "모델 구조 및 결정 근거 미공개"
        }
    },
    "compliance_gap": {
        "EU_AI_Act": ["Annex III 고위험군 분류 해당", "Transparency 요구사항 미충족"],
        "UNESCO": ["책임성과 설명 가능성 항목 부족"]
    },
    "report_data": {
        "summary": "MediScan은 고위험 의료 AI로 분류되며, 프라이버시 및 투명성 영역에서 개선이 시급함",
        "risk_table": [
            {"서비스명": "MediScan", "위험유형": "편향성", "위험수준": "High"},
            {"서비스명": "MediScan", "위험유형": "프라이버시", "위험수준": "Medium"},
            {"서비스명": "MediScan", "위험유형": "설명가능성", "위험수준": "Low"}
        ]
    },
    "recommendations": [
        "데이터 수집 알고리즘에 대한 편향 재검증 필요",
        "의사결정 시각화 도구(XAI) 도입",
        "개인정보 암호화 정책 업데이트"
    ]
}
```

---

## 보고서 생성 프롬프트

`Report Generator` 에이전트가 `state`를 바탕으로 보고서를 생성할 때 사용하는 **LLM 프롬프트 템플릿**입니다.

### 프롬프트 예시

```text
당신은 AI 윤리 진단 전문가입니다. 아래 정보를 기반으로 AI 윤리 리스크 진단 보고서를 작성하세요.

[서비스 정보]
- 서비스명: MediScan
- 도메인: Healthcare
- 설명: 고령자 대상 의료상담 챗봇
- 사용 알고리즘: Deep Learning
- 데이터 규모: 120,000건

[적용 기준]
- EU AI Act Annex III
- UNESCO AI Ethics Recommendation

[리스크 분석 결과]
- 편향성(Bias): High / 성별 추천 정확도 차이 23%
- 프라이버시(Privacy): Medium / 암호화 기준 미준수
- 설명 가능성(Transparency): Low / 결정 근거 미공개

[준수 격차]
- EU AI Act: Annex III 고위험군 해당, 투명성 요건 미충족
- UNESCO: 책임성, 설명 가능성 미흡

[개선 권고안]
1. 데이터 수집 알고리즘의 편향 재검증
2. 의사결정 시각화 도구 도입 (XAI)
3. 개인정보 처리방침 강화 및 모니터링 도입

[보고서 형식]

1. SUMMARY
   - 주요 위험 항목 요약
   - 긴급 개선 항목 명시

2. METHODOLOGY
   - 사용된 윤리 기준 및 평가 도구 설명

3. FINDINGS
   - 항목별 위험 분석 표 및 요약

4. RECOMMENDATIONS
   - 구체적 개선 방안 제안

위 구조를 Markdown 보고서 형식으로 작성하세요.
```
