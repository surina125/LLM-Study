# 🏦 은행 업무 문서 기반 RAG 챗봇

## 📌 프로젝트 개요

은행 업무 관련 문서를 기반으로 질의응답이 가능한  
RAG(Retrieval-Augmented Generation) 챗봇을 구현하는 프로젝트

- LangChain과 Ollama를 활용하여  
문서 검색(Retrieval)과 답변 생성(Generation)을 결합한 구조를 구현

---

## 🧠 RAG 구조

RAG는 Retrieval-Augmented Generation의 약자로,  
질문에 대해 관련 문서를 검색하여 LLM의 답변 정확도를 높이는 방식

프로젝트 흐름:

1. Indexing  
   문서를 임베딩하여 벡터 DB에 저장

2. Retrieval  
   사용자 질문과 유사한 문서 검색

3. Augmenting  
   검색된 문서를 프롬프트에 포함

4. Generation  
   LLM이 최종 답변 생성

---

## ⚙️ 기술 스택

- LLM: Ollama (`llama3:8b`)
- Framework: LangChain
- Embedding:
  - OpenAI (`text-embedding-3-large`)
  - Qwen3 Embedding (`0.6B`)
- Vector DB: Qdrant
- Document Parsing:
  - PyMuPDF
  - Docling
- Language: Python

---

## 🤖 모델 선택

초기에 `nemotron-cascade-2:30b` 모델을 사용하려 했으나,  
디스크 용량 제한으로 인해 `llama3:8b` 모델을 사용함.

---

## 📄 데이터 처리 과정

### 1. 문서 로딩
- PDF 문서를 로드하여 텍스트 추출

### 2. 문서 파싱
- PyMuPDF: 기본 텍스트 추출
- Docling: 표 및 구조화된 데이터 처리

### 3. 전처리
- 특수문자 제거
- 불필요한 공백 정리
- HTML 엔티티 제거

### 4. 청킹
- RecursiveCharacterTextSplitter 사용
- chunk_size=1000, overlap=200

---

## 🗄 벡터 데이터베이스 구축

- Qdrant를 활용하여 벡터 DB 구성
- 문서 임베딩 후 저장
- Top-K 검색 기반 Retrieval 수행

---

## 🔗 RAG 체인 구성

Question → Retriever → Context 생성 → Prompt → LLM → Answer

- LangChain의 체인을 활용하여  
Retrieval과 Generation을 하나의 파이프라인으로 구성

---

## 💬 실행 예시

- 여권으로 실명확인이 가능한가요?
- 30세 골드 VIP인 광은이가 창구에서 타행으로 500만원을 송금 시 송금수수료는 얼마인가?
- 한도거래여신 300백만원 기한연장 시 한도소진율이 27%(전전월말 이전 과거 6개월 평균고진율 기준)인 경우 영업점장전결로 기한연장 시 연장 가능금액은 최대 얼마인가?
- 차주의 총 금융부채 상환부담을 판단하기 위하여 신청하는 차주의 연간 소득 대비 연간 금융부채 원리금 상환액 비율은 몇 퍼센트인가요?

---

## 🚀 추후 개선 방향

1. 데이터 정제
- PDF 문서 내 불필요한 텍스트 제거
- 깨진 문자 및 노이즈 데이터 정리
- 문서 구조 기반 정제 필요

2. 청킹 전략 개선
- 단순 길이 기반 → 문단/조항 기반 분할
- 금융 문서 특성 반영한 구조적 청킹 필요

3. 모델 성능 개선
- 현재: llama3:8b (경량 모델)
- 향후:
  - 고성능 모델 적용
  - 응답 품질 비교 및 개선
