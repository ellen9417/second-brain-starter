---
title: 온톨로지 스키마 (Schema)
type: Schema
category: 시스템
tags: [온톨로지, 스키마, 헌법, Entity, Relation, Action]
created: 2026-08-03
updated: 2026-08-03
---

# 온톨로지 스키마 — 이 위키의 헌법

> 이 문서는 위키의 **현실을 정의한다.** 모든 페이지가 따르는 타입·관계·행동의 controlled vocabulary.
> 팔란티어 3레이어(Semantic·Kinetic·Dynamic) 모델 기반.

## 1. Entity 타입 (존재하는 것) — Semantic

모든 콘텐츠 페이지는 frontmatter에 `type:` 한 개를 갖는다.

| type | 의미 | 예시 |
|------|------|------|
| `Project` | 최상위 과제 | [[포트폴리오-리뉴얼]] |
| `Component` | 프로젝트 하위 시스템·스펙·산출물 | (프로젝트의 세부 산출물 페이지) |
| `Policy` | 정책·규정 | (사내 규정 정리, 개인 원칙) |
| `Domain` | 업무 도메인·시스템 지식 | (내 도메인의 구조·용어 정리) |
| `Method` | 방법론·노하우·멘탈모델 | [[llm-회의록-정리-노하우]] |
| `Case` | 사례·평가·검증 결과 | (실험 결과, 회고) |
| `Record` | 시계열 기록(주간보고 등) | (주간보고 아카이브) |
| `Career` | 커리어 자산 | (프로젝트 이력, 포트폴리오 소재) |
| `Personal` | 개인 자료 색인 | (개인 기록 색인) |
| `Person` | 협업 인물 프로필(스타일·관점·대응법) | (자주 협업하는 사람의 커뮤니케이션 스타일) |
| `Hub` | 종합 허브 | [[overview]] |
| `Index`·`Log`·`Schema`·`Registry` | 운영 인프라 | index, log, _ontology, _entities |

신규 타입이 필요하면 **여기 먼저 추가**한 뒤 사용한다.

## 2. Relation 타입 (관계) — "일급 시민"

`[[링크]]`는 "연결만" 표현한다. 의미 있는 관계는 **타입을 붙인다.**
표기: frontmatter `relations:` 블록 또는 본문 inline `관계:: [[대상]]` (Obsidian Dataview 호환). 중앙 집계는 [[_entities]].

| relation | 방향 | 의미 |
|----------|------|------|
| `part_of` | Component → Project | ~의 구성요소 |
| `impacts` | A → B | A가 B에 영향을 줌 |
| `depends_on` | A → B | A는 B에 의존 |
| `informs` | 과거 → 현재 | 과거 경험이 현재를 떠받침 |
| `applies_to` | Method → Project | 방법론 적용 대상 |
| `evaluates` | Case → Component | ~를 평가/검증 |
| `owned_by` | Entity → Person | 담당자 |
| `derived_from` | Entity → Source | 원본 출처(raw/외부) |
| `supersedes` | new → old | ~를 대체 |
| `realizes` | Case/Component → Project | 전략·계획을 실물로 실현 |
| `implements` | Component → Policy/설계 | ~를 구현 |
| `uses` | A → B | ~를 사용·소비 |
| `feeds` | A → B | A의 산출이 B의 입력이 됨 |
| `documents` | Record → Entity | ~를 문서화·설명 |
| `references` | A → B | 참고·인용 |
| `authored_by` | Entity → Person/Career | 작성 주체 |
| `relates_to` | A ↔ B | 일반 연관 — **더 구체적 관계가 없을 때만** 사용 |

## 3. Attribute (속성) 규약

frontmatter 공통 키: `title` `type` `category` `tags` `source` `created` `updated`
선택 키: `status`(active/done/blocked/draft) · `priority`(P0~P2) · `owner` · `metrics`
Kinetic 바인딩 키(아래 §4): `src_jira` `src_confluence` `src_calendar` `src_slack` `src_git`
세분 변형: `src_<소스>_<세부>` 패턴으로 자유 확장 (예: `src_slack_saved`, `src_jira_keys`)

## 4. Kinetic — 데이터가 흘러드는 통로

`Project`·`Case`·`Record` 등 "살아있어야 하는" 개체는 데이터 소스를 **선언**한다. 질문 시 이 바인딩으로 현재 상태를 pull.

```yaml
src_jira: "project = MYPROJ AND assignee = currentUser()"  # JQL
src_confluence: "<pageId>"                                  # 문서 ID
src_calendar: "primary"                                     # 캘린더
src_slack: "#my-project"                                    # 채널
```

원칙: *lineage tracked · live.* 스냅샷이 아니라 "지금 상태"를 반영하고, 출처를 `derived_from`으로 남긴다.

**SoT(진실의 원천) 우선순위 — ★내 도구 스택에 맞게 수정:** "내 할 일·진행 상황"의 1차 원천 순서를 정한다. 기본 예시:
① 내가 편집/기여한 문서(위키·Confluence 등) ② 내가 저장해둔 메시지(액션 큐) ③ 캘린더 ④ 티켓 시스템(보조 — 남이 나에게 요청한 일 뷰라 내 의도와 불일치할 수 있음). standup·할 일 추출은 ①②를 먼저, 티켓은 보조로 본다.

**Pull 정책 = 답변 전용:** 바인딩 소스는 **질문 답변 시점에만** 실시간 조회해 답변에 반영한다. pull 결과로 **위키 본문을 자동 갱신하지 않는다**(본문 변경은 명시적 ingest·승인 시에만). 상세: 루트 `CLAUDE.md` §0.5.

## 5. Dynamic — 개체로 할 수 있는 행동 (Action)

각 타입은 실행 가능한 액션을 갖는다. 액션 = Claude가 수행하는 작업 패턴에 매핑 (스킬로 만들면 더 좋다).

| 대상 타입 | Action | 동작 |
|-----------|--------|------|
| `Project` | `.brief()` `.status()` | 현황 브리핑 (kinetic pull 포함) |
| `Decision` | `.log()` `.branch()` `.merge()` | 의사결정 버전관리 (§7) |
| `Meeting` | `.prep()` | 회의 준비 브리핑 |
| `Document`(raw) | `.ingest()` `.review()` | 위키 ingest · 문서 리뷰 |
| 위키 전체 | `.healthcheck()` | 건강검진(운영3) + §8 온톨로지 정합성 |

## 6. Trigger·Rule (IF-THEN)

- `IF raw/에 새 파일 THEN Document.ingest()` → 관련 Project에 `derived_from`/`part_of` 링크
- `IF 의사결정 확정 THEN Decision.log() + 관련 Project에 impacts 링크`
- `IF wiki+raw 페이지 > ~150 (또는 직접 읽기/검색이 느려짐) THEN RAG 레이어 검토` — 임베딩 + 벡터스토어 + **온톨로지 메타데이터(type·관계) 필터**. 건강검진(§8)에서 페이지 수 체크해 알림.

> 자동 실행(파일 감지 등)은 harness hook(settings.json)이 필요 — 기본은 "정리해줘" 한마디가 트리거.

## 7. 의사결정 버전관리 (Git-like)

Git이 코드를 버전관리하듯 의사결정을 버전관리한다.

| Git | 의사결정 온톨로지 | 여기서 |
|-----|------------------|--------|
| branch | AI 제안 | `Decision status: branch` |
| PR review | 오너 검토 | 승인/반려 |
| merge | 적용 | 위키 반영 + `status: merged` |
| log | 이력 | [[log]] = 의사결정 히스토리 |

## 8. 건강검진 확장 — 온톨로지 정합성

기존 건강검진(운영3)에 추가 검사:
1. `type:` 누락 페이지
2. 스키마에 없는 type/relation 사용
3. 끊긴 Kinetic 바인딩(소스 없어짐)
4. `Decision` 중 branch 상태로 방치된 것
5. **규모 체크** — wiki+raw 페이지 수가 ~150 접근 시 "RAG 레이어 검토" 알림

## 관련 페이지
- [[_entities]] (개체 레지스트리) · [[overview]] · 운영 규칙: 루트 `CLAUDE.md`
