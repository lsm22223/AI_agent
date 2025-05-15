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
