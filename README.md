# 도입사례(Case Study) Agent

그로스팀의 세일즈/도입 검토 **미팅 기록을 읽어 `도입사례_data` 시트로 정리**하는 에이전트.
Slack `callabo-sales-meeting` 채널·첨부 파일에서 기업별 **도입 사유 · 후킹 포인트 · 활용 방법**을
추출해, 산업군별로 조회 가능한 시트에 1행씩 적재한다.

## 구성 (Agent의 4대 요소)

| 요소 | 파일 | 역할 |
|---|---|---|
| **Persona** | [Agents.md](./Agents.md) | 역할·판단 기준·산출물 포맷 (8개 섹션) |
| **Memory** | [Memory.md](./Memory.md) | 지난 경험·실패에서 배운 판단 규칙 누적 |
| **Skills** | [skills/extract-case-study.md](./skills/extract-case-study.md) | 미팅 기록 1건 → 시트 1행 추출·적재 절차 |
| **Tools** | [Agents.md §6](./Agents.md) | Slack MCP(읽기), Google Sheets/Drive MCP(append) |

> 기획 원본: [description.md](./description.md)

## 동작 흐름

```
미팅 기록(CSV·텍스트·이미지)
      │  Slack 첨부 / callabo-sales-meeting 채널
      ▼
[1] 읽기 → [2] 시트 상태·중복 확인 → [3] 5개 열 추출
      → [4] 근거 없는 셀 공란·애매하면 확인 질문 → [5] 시트 1행 append
      → [6] Slack 요약 + 공란 사유 고지
      ▼
도입사례_data 시트 (1행 = 1기업)
```

### 시트 열 스키마 (`도입사례_data`)

| 산업군 | 플랜 | 기업명 | 부서 | 도입 사유 / 후킹 포인트 / 활용 방법 |
|---|---|---|---|---|
| 분류 규칙 적용 | 확정된 플랜만, 미확정 시 공란 | 미팅 대상 | 도입·활용 주체 | 도입 배경·핵심 후킹·활용 요약 |

## 핵심 원칙

- **불명확하면 추측하지 않고 비워둔다.** (특히 플랜은 명시적으로 확정된 경우만 기입)
- 미팅 기록에 없는 통계·정보를 지어내지 않는다.
- 시트는 **append 전용** — 기존 행 수정·삭제 금지, 중복 기업은 확인 후 처리.
- 게재·발송은 하지 않는다. 정리와 Slack 요약까지만, 이후 사람 검수.

## 현재 상태 / 제약

- ✅ 읽기·추출·판단·가드레일 파이프라인 동작 확인 (유니클로 미팅으로 실측, 기존 시트 행 재현).
- ⚠️ **시트 쓰기(append) 미지원** — 현 세션의 Google Drive MCP는 읽기 전용이라,
  실제 행 기입에는 **쓰기 가능한 Google Sheets MCP 연동이 별도 필요**하다.
- Slack MCP는 인증 후 사용 가능 (`callabo-sales-meeting` 접근 시).
