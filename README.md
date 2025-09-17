# SKN16-3st-4Team
SKN 16기 3차 단위프로젝트

## 산출물
 - 

## 수집된 데이터 및 데이터 전처리 문서
 - https://www.jiisonline.org/index.php?mnu=archive

## 시스템 아키텍쳐
 - Frontend/UI: Gradio
 - PDF 처리기: PDFplumber, PyMuPDF, OCR
 - 전처리: okt, komoran, keybert
 - 메타데이터 추출기: gpt (APA 인용 생성 포함)
 - RAG embedder: 'jhgan/ko-sroberta-multitask'
 - RAG reranker: 'cross-encoder/ms-marco-MiniLM-L-12-v2'
 - 벡터 DB: ChromaDB 기반 RAG 검색
 - 웹 검색: Tavily
 - LLM 연동: ‘gpt-4’ + 평가기(CustomLLMEvaluator)
 - 로깅/모니터링: LangSmith

## 개발된 소프트웨어 : RAG기반 LLM과 벡터 데이터베이스 연동 구현 및 화면
 - 로그인 및 API 키 설정

 - 단일 논문 업로드 및 분석
   
 - 다중 업로드 및 분석
   
 - 임베딩 모델 선택
   
 - 업로드 논문 DB 관리
   
 - 논문 DB 기반 AI 챗봇
 - 

## 테스트 계획 및 검증보고서
 - 업로드 시 : 업로드할 논문과 관련 있는 질문 / 관련 있는 질문 + 관련 없는 질문 / 관련 없는 질문
 - 챗봇 사용시 : 업로드 되어있는 논문들과 관련 있는 질문 / 관련 있는 질문 + 관련 없는 질문 / 관련 없는 질문

## 결과 보고서
 - 
