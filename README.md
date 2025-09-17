# SKN16-3st-4Team
SKN 16기 3차 단위프로젝트

## 개요
- 프로젝트명: 논문 분석 챗봇
- 배경: 논문 탐색과 저장 및 관리, 정보 활용에 많은 시간 소요
- 목표: 논문 데이터를 활용해 RAG기반 + 웹 검색 질의응답 서비스 구축
- 범위: 한국지능정보연구원 학술논문 약 300건(2020~2025) (https://www.jiisonline.org/index.php?mnu=archive)
- 주요 기능:
   - 논문 업로드 (PDF)
   - 메타정보 추출 및 섹터 요약
   - 논문 분석
   - 추출 DB 업데이트 및 관리
   - 논문 기반 AI 챗봇 기능

## 수집된 데이터 및 데이터 전처리 문서
 - https://www.jiisonline.org/index.php?mnu=archive

## 시스템 아키텍쳐
![archit](./img/archit.jpg)
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

### 임베딩 모델 비교
- 비교 모델 : jhgan/ko-sroberta-multitask, BAAI/bge-m3, intfloat/multilingual-e5-base, sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
![embed eval](./img/embed eval.jpg)
![embed vs](./img/embed vs.jpg)

## 개발된 소프트웨어 : RAG기반 LLM과 벡터 데이터베이스 연동 구현 및 화면
 - 사진 업로드

## 테스트 계획 및 검증보고서
### 테스트 계획
  1. 로그인 검증 → 접속 및 API 설정 검증
  2. 논문 PDF 업로드 → 메타데이터 추출 검증, 다중 PDF 업로드 검증
  3. 논문 DB 관리 → 메타데이터 수정, 삭제 검증
  4. 논문 챗봇 → 답변/출처/평가 자체 검증 + Human 정성적 평가 검증

### 자체 검증 Prompt
 - EVALUATION_PROMPT = """###작업 설명:
   지시사항(내부에 입력이 포함될 수 있음), 평가할 응답, 5점을 받은 참조 답안, 그리고 평가 기준을 나타내는 점수 루브릭이 제공됩니다.
   1. 주어진 평가 기준에 따라 응답의 질을 엄격하게 평가하는 상세한 피드백을 작성합니다.
   2. 피드백 작성 후, 1에서 5사이의 정수 점수를 작성합니다. 점수 루브릭을 참조해야 합니다
   3. 출력 형식은 다음과 같습니다: \"Feedback: {{평가 기준에 대한 피드백 작성}} [RESULT] {{1에서 5 사이의 정수}}"
   4. 다른 서문, 결론, 설명은 생성하지 말고, 출력에 [RESULT]를 반드시 포함하세요.
   
   ###평가할 지시사항:
   {instruction}
   
   ###평가할 응답:
   {response}
   
   ###참조 답안 (점수 5):
   {reference_answer}
   
   ###점수 루브릭:
   [응답이 참조 답안에 기반하여 정확하고 사실적인가?]
   점수 1: 응답이 완전히 정확하지 않거나 사실적이지 않습니다
   점수 2: 응답이 대부분 정확하지 않거나 사실적이지 않습니다.
   점수 3: 응답이 어느 정도 정확하고 사실적입니다.
   점수 4: 응답이 대부분 정확하고 사실적입니다.
   점수 5: 응답이 완전히 정확하고 사실적입니다.
   
   ###피드백:"""
   
   from langchain.prompts.chat import (
       ChatPromptTemplate,
       HumanMessagePromptTemplate,
   )
   from langchain.schema import SystemMessage
   
   evaluation_prompt_template = ChatPromptTemplate.from_messages(
       [
           SystemMessage(content="당신은 공정한 평가자 언어 모델입니다."),
           HumanMessagePromptTemplate.from_template(EVALUATION_PROMPT),
       ]
   )

 ### 검증 결과
  1. 로그인 검증 ✅ 정상 (잘못된 계정 시 오류 처리 확인)
  2. 논문 PDF 업로드 ✅ 정상 (PDF 100건 업로드 및 분석 확인)
  3. 논문 DB 관리 ✅ 정상 (저장, 수정, 삭제 확인)
  4. 논문 챗봇 ⚠️ 양호 (관련/비관련/복합 질문 30건 테스트 결과 정확도 평균 93.0점)
     [검증 시트 첨부 참고](./verify/검증 시트.xlsx)

## 결과 보고서
본 프로젝트를 통해 논문 데이터를 활용한 RAG 기반 질의응답 시스템과 웹 검색 연동 챗봇 서비스를 성공적으로 구축할 수 있었습니다. 초기 개발 단계에서는 한국지능정보연구원(KIIS)에서 제공하는 2020년 이후 논문 데이터를 대상으로 범위를 설정하였으며, 사용성을 고려하여 다양한 형식의 PDF를 업로드하고 분석할 수 있도록 OCR 기능을 포함하여 시스템을 설계했습니다.
그러나 일부 스캔 PDF의 경우 텍스트 추출이 원활하지 않은 한계가 확인되었으며, 웹 검색 기반 fallback 시나리오에서는 Travily 응답 지연 및 환각(hallucination) 현상이 발생하는 문제가 관찰되었습니다.
따라서 향후 개선 방향은 다음과 같습니다.
 1. OCR 품질 향상: 다양한 스캔 형식 및 저해상도 PDF에 대한 텍스트 추출 성능 개선
 2. 웹 검색 안정화: 외부 검색 API 응답 지연 최소화 및 환각 답변 억제를 위한 프롬프트 및 결과 필터링 전략 강화
 3. 데이터 확장: 보다 다양한 언어와 출처의 논문 데이터 통합을 통해 시스템 범용성 확보
이를 통해 시스템의 신뢰성과 응답 정확도를 더욱 향상시킬 수 있을 것으로 기대됩니다.

