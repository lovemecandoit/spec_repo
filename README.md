# Spec Repository Overview

이 저장소는 **제품/서비스 스펙 문서를 단일 소스(Source of Truth)**로 관리하고,  
**AI(MCP 서버 기반)가 문서를 검색·분석·인용하여 답변**할 수 있도록 설계된 스펙 저장소입니다.

원본 문서(PDF/DOCX)는 그대로 보존하고,  
AI 검색/분석에 최적화된 **추출 텍스트(Markdown)**를 함께 관리합니다.

## Repository Structure

```text
specs/
  productA/
    raw/
      2025-11/
        Spec_ProductA_v2.3.pdf
        API_Reference_v1.4.docx
    extracted/
      2025-11/
        Spec_ProductA_v2.3.md
        API_Reference_v1.4.md
    attachments/
      2025-11/
        diagrams/
        images/

  productB/
    raw/
    extracted/
    attachments/

meta/
  glossary.md
  owners.yml
  access.yml
  indexing.yml

tools/
  extractor/
  indexer/
```

## Directory 설명

### `specs/`
제품 또는 서비스별 스펙 문서를 관리하는 최상위 디렉터리입니다.  
각 제품은 독립적인 문서 세트와 버전을 가집니다.

### `raw/`
- **원본 문서 저장소**
- PDF, DOCX 등 원본 파일을 **수정 없이 그대로 보관**
- 법적/계약적 기준 문서이자 최종 참조본(Source of Truth)

> ⚠️ `raw/` 파일은 직접 편집하지 않습니다.

### `extracted/`
- AI 검색·분석을 위해 **원본 문서에서 추출한 텍스트**
- 일반적으로 Markdown(`.md`) 형식
- 원본 파일과 **1:1 매칭되는 파일명 유지**
- MCP 서버 및 검색 인덱싱 대상

> AI 답변은 기본적으로 `extracted/` 내용을 기준으로 생성되며,  
> 인용 시 원본(`raw/`) 문서의 페이지/섹션을 함께 표시합니다.

### `attachments/`
- 문서에서 참조하는 이미지, 다이어그램 등 보조 자료
- 추출된 Markdown에서 상대 경로로 참조 가능

---

## `meta/` 디렉터리

문서 관리 및 AI 품질을 높이기 위한 메타데이터 디렉터리입니다.

- **`glossary.md`**  
  제품/도메인 공통 용어 정의  
  AI 검색 정확도 및 응답 일관성 향상 목적

- **`owners.yml`**  
  문서 또는 디렉터리 단위 담당자 정의  
  리뷰, 변경 문의, 책임자 추적용

- **`access.yml`**  
  폴더/문서 단위 접근 권한 정의  
  MCP 서버에서 권한(ACL) 체크 시 사용

- **`indexing.yml`**  
  문서 인덱싱 관련 설정  
  청크 크기, 제외 경로, 문서 타입 규칙 등 정의

---

## `tools/` 디렉터리 (선택)

문서 처리 자동화를 위한 도구 모음입니다.

- **`extractor/`**  
  PDF/DOCX → Markdown 변환 스크립트  
  섹션, 페이지 정보, 추출 신뢰도 포함

- **`indexer/`**  
  추출 문서를 검색/벡터 인덱스로 변환하는 파이프라인

> MVP 단계에서는 외부 파이프라인을 사용하고,  
> 필요 시 이 디렉터리에 스크립트를 추가할 수 있습니다.

---

## 문서 관리 원칙

1. **원본 보존**
   - 모든 변경은 새 버전 파일로 추가
   - 기존 문서 덮어쓰기 금지

2. **추출 결과 동기화**
   - `raw/` 문서 변경 시 `extracted/`도 함께 업데이트
   - PR에서 추출 결과 diff를 리뷰 가능하게 유지

3. **버전 명시**
   - 파일명 또는 상위 디렉터리로 버전/날짜 명확히 구분
   - AI 답변에는 항상 참조한 문서의 버전/커밋 정보 포함

4. **근거 기반 응답**
   - AI는 이 저장소에 존재하는 내용만 근거로 답변
   - 문서에 없는 내용은 “확인 불가”로 응답

---

## AI / MCP Server 연동 개요

- MCP 서버는 이 repo를 **읽기 전용**으로 접근
- `extracted/` 문서를 검색·분석 대상으로 사용
- AI 답변 시 다음 정보를 근거로 함께 제공:
  - 문서 경로
  - 섹션 제목
  - 페이지 범위 (PDF 기준)
  - 커밋 또는 태그 정보

---

## 권장 사용 시나리오

- 스펙 질의 응답 (Q&A)
- API / 에러 코드 설명 검색
- 문서 간 변경사항 비교
- 요구사항 영향도 분석
- 신규 인력 온보딩 지원