
# ITAM 서비스 담당자 도우미 - AI 시스템 개발 보고서

## 개요
본 프로젝트는 전사통합 IT 자산관리(ITAM) 시스템 내에서 서비스 담당자 정보를 사용자 요청에 따라 정확히 안내하는 **AI 기반 Q&A 어시스턴트**를 개발하는 것을 목표로 하였습니다. 
Azure OpenAI, Azure Search, Azure SQL Database 등의 서비스를 연계하여 사용자의 질문에 의미 기반 검색 및 자연어 응답이 가능한 RAG 파이프라인을 구현하였습니다.

---

## 1. 기능 구현 

  - 사용자가 입력한 문장에서 이름과 요청 정보를 인식하고, 사전 연동된 서비스 담당자 데이터를 기반으로 결과를 반환하는 기능 구현
  - 예: `"나는 배수현1입니다. ITAM 담당자 알려주세요"` → 해당 담당자 정보 응답
  - 권한 없는 사용자의 경우: `"권한이 없습니다. 권한을 획득하세요"` 로 안내

**흐름도 설명**

- 사용자가 질문을 입력하면 SQL DB USER 테이블의 `usernm`에 해당하는 권한(owner)을 조회합니다.
- 권한이 없는 경우 권한 신청을 안내합니다.
- 권한이 있을 경우, CODE 테이블에서 AP 담당자 정보를 조회합니다.
- AP 담당자가 없을 경우, PO 및 BA 담당자를 제공합니다.

---

## 2. 기술 구현 

  - `Azure SQL Database`에서 원본 데이터 관리
    <img width="1100" height="439" alt="image" src="https://github.com/user-attachments/assets/338dded2-0af0-41cf-a5ac-faa7dcbc5fc8" />

  - `AzureSearch Service`를 통한 백터 인덱스 + 의미 기반 검색 구성
    <img width="1160" height="141" alt="image" src="https://github.com/user-attachments/assets/7e9b603f-facf-4ef9-a127-0a5f8c112277" />

  - `Azure OpenAI` 모델(gpt-4o)과 연동하여 사용자 쿼리에 대해 자연어 응답 제공
  -<img width="1233" height="191" alt="image" src="https://github.com/user-attachments/assets/613315eb-4d70-421c-bf65-3860b682c210" />

  - `.env`를 통한 안전한 키 관리 및 배포 자동화 스크립트(streamlit.sh) 구현
   <img width="188" height="126" alt="image" src="https://github.com/user-attachments/assets/2a04e74f-8477-49bd-8837-9ecab3eace7c" />

   <img width="960" height="449" alt="image" src="https://github.com/user-attachments/assets/4a1f49c5-1825-4754-8f12-79299951afc8" />


- **코드 구성**:
  - `Streamlit` 기반 웹앱 UI로 사용자 입력 및 응답 확인
  - `app.py`에서 `.env`, `system_prompt.txt` 분리로 유지보수 용이
  - 구조 분리 및 모듈화 고려한 간결한 Python 코드 작성

---

## 3. 사용자 경험 (UX)

- **UI 및 흐름**:
  - 웹 페이지에 접속 후 간단한 텍스트 입력만으로 담당자 정보를 얻을 수 있음
  - 최소 입력으로 의미 있는 결과 도출 → 접근성과 사용성 고려
    <img width="737" height="259" alt="image" src="https://github.com/user-attachments/assets/7abef3a0-4adf-441e-b3a6-c321b05c0c85" />


- **절차 간편화**:
  - 사용자는 Streamlit UI에서 `"질문 입력 → 버튼 클릭"` 만으로 결과 확인 가능
  - 별도 메뉴나 복잡한 선택 없이 단일 페이지 내에서 전체 기능 수행

---

## 4. 발표 및 데모

- **문제 정의 및 솔루션 설명**:
  - 기존 IT 서비스 담당자 정보 확인의 어려움 → AI 기반 질의응답으로 해결
  - 의미 기반 검색을 통한 정확한 정보 매칭 구조 설명

- **라이브 데모**:
  - Streamlit 실행 → 사용자 질문 입력 → GPT 응답 확인까지 실시간 시연
  - 인덱스 생성, 데이터 처리, 응답 예시까지 실제 작동 확인

- **질의응답 대응**:
  - 에러 발생 시 처리 사례, 한글 인코딩 이슈 해결, Semantic 구성 등 실전 대응 경험 있음

---

## 5. 혁신성 및 확장성

- **독창성**:
  - 기존 RAG 응용을 넘어, 사용자 권한 체크까지 반영한 응답 제어 기능 구현
  - 시스템 프롬프트 기반 규칙 제어 + 실시간 검색 결합 구조

- **확장 고려**:
  - 테이블 및 인덱스 구조를 일반화하여 향후 담당자 외 자산 정보 등 확장성 고려
  - 시스템 프롬프트 기반의 사용자에게 자주 발생하는 QnA 에 대하여 다음 단계까지 고려한 응답 진행
  - 시스템 적용시 Login Session 정보를 활용하여 질문자 의 권한정보 자동파악 기능 확장 고려

---

## 6. 시연 영상

https://github.com/user-attachments/assets/dd81d493-76ec-4f80-81da-338575725223

---

##  결론

- 본 프로젝트는 **Azure 기반 생성형 AI 기능과 검색 기술을 통합**하여,  
  **실제 업무에 적용 가능한 IT 서비스 도우미 시스템**을 구현한 사례입니다.

