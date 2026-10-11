# PM Skills 저장소 분석 & 활용 가이드 (대화 정리)

> 작성일: 2026-10-11
> 원본 저장소(업스트림): https://github.com/phuryn/pm-skills
> 내 포크 저장소: https://github.com/bmshin94/pm-skills
> 기준 버전: v2.1.0 (+ Unreleased 변경분), 라이선스: MIT

이 문서는 "이 저장소가 뭔지, 언제 쓰는지, 나한테 무슨 도움이 되는지"를 전수조사한 결과와 이어서 나눈 질문·답변(설치, 정체, API 토큰, 에이전트, 수익화, React/PHP, 유튜브)을 한곳에 정리한 것이다.

---

## 1. 전수조사 결과: 이게 뭐하는 저장소인가?

### 한 줄 요약
**AI 코딩 어시스턴트(Claude Code / Claude Cowork 등)에 "프로덕트 매니저(PM)의 업무 노하우"를 설치해 주는 플러그인 마켓플레이스.**
코드가 실행되는 프로그램이 아니라, AI가 읽고 따라 하는 **마크다운 지침서 묶음**이다.

### 규모
| 항목 | 개수 |
|---|---|
| 플러그인 | 9개 |
| 스킬(Skill) | 69개 |
| 커맨드(Command) | 42개 |
| 스킬 지침 총 분량 | 약 6,600줄 |

> 참고: `CLAUDE.md`에는 "68 skills"로 적혀 있지만, 최근 `pm-ai-shipping`에 `code-review` 스킬이 추가되면서 실제 디스크·README·marketplace.json 기준은 69개다. (README의 pm-ai-shipping 섹션 안 "Skills (2)" 목록에도 `code-review`가 빠져 있는 사소한 문서 불일치가 있다.)

### 폴더 구조
```
pm-skills/
├── .claude-plugin/marketplace.json   ← 마켓플레이스 목록(9개 플러그인 등록)
├── .docs/images/                     ← README용 이미지
├── .github/workflows/                ← CI: tests.yml(테스트), tag-on-merge.yml(자동 릴리스)
├── CHANGELOG.md                      ← 버전/릴리스의 기준 문서
├── CLAUDE.md / AGENTS.md             ← AI 에이전트용 작업 지침 (+ 카리나 페르소나 추가됨)
├── CONTRIBUTING.md, LICENSE(MIT), README.md
├── validate_plugins.py               ← 플러그인 형식 검증기(파이썬)
├── tests/                            ← 일관성 테스트(개수, 버전 동기화 등)
└── pm-{이름}/                         ← 플러그인 9개
    ├── .claude-plugin/plugin.json    ← 플러그인 정보(이름, 버전, 설명)
    ├── skills/{스킬}/SKILL.md         ← 스킬 1개 = 폴더 1개
    ├── commands/{커맨드}.md           ← 커맨드 1개 = 파일 1개
    └── README.md
```

### 핵심 개념 3가지
- **스킬(Skill) = 명사/지식.** "린 캔버스", "프리모템", "시장 규모 추정" 같은 프레임워크. 대화 주제가 맞으면 Claude가 **자동으로** 불러와 사용한다. 예: `create-prd` 스킬은 PRD를 8개 섹션(요약·담당자·배경·목표·시장·가치제안·솔루션·릴리스)으로 쓰게 지시한다.
- **커맨드(Command) = 동사/워크플로.** 사용자가 `/discover`, `/write-prd`처럼 직접 실행. 여러 스킬을 순서대로 엮는다. 예: `/discover`는 아이디어 발산 → 가정 도출 → 가정 우선순위 → 실험 설계 → 디스커버리 계획서 저장까지 7단계, 중간중간 사용자 확인(체크포인트)을 받는다.
- **플러그인(Plugin) = 묶음.** 관련 스킬+커맨드를 하나로 포장한 설치 단위. 플러그인끼리는 서로 직접 참조하지 않아 하나만 설치해도 동작한다.

### 9개 플러그인 상세
| # | 플러그인 | 스킬/커맨드 | 하는 일 | 대표 커맨드 |
|---|---|---|---|---|
| 1 | pm-product-discovery | 13 / 5 | 아이디어 발산, 위험 가정 찾기, 실험 설계, 기능 요청 분석, 인터뷰 스크립트/요약, 지표 대시보드 | `/discover`, `/brainstorm`, `/interview`, `/triage-requests`, `/setup-metrics` |
| 2 | pm-product-strategy | 12 / 5 | 제품 비전·전략 캔버스, 린 캔버스, 비즈니스 모델 캔버스, 가격·수익화, SWOT, PESTLE, 포터 5 Forces, 앤소프 | `/strategy`, `/business-model`, `/pricing`, `/market-scan`, `/value-proposition` |
| 3 | pm-execution | 16 / 11 | PRD, OKR, 로드맵, 스프린트·회고, 릴리스 노트, 프리모템, 이해관계자 맵, 유저/잡 스토리, 테스트 시나리오, 더미 데이터, 레드팀 | `/write-prd`, `/sprint`, `/write-stories`, `/test-scenarios`, `/generate-data`, `/red-team-prd` |
| 4 | pm-market-research | 7 / 3 | 페르소나, 시장/사용자 세분화, 고객 여정 지도, TAM·SAM·SOM, 경쟁사 분석, 감성 분석 | `/research-users`, `/competitive-analysis`, `/analyze-feedback` |
| 5 | pm-data-analytics | 3 / 3 | 자연어 → SQL(BigQuery/PostgreSQL/MySQL), 코호트·리텐션, A/B 테스트 유의성 | `/write-query`, `/analyze-cohorts`, `/analyze-test` |
| 6 | pm-go-to-market | 6 / 3 | GTM 전략, 비치헤드 시장, ICP, 그로스 루프, GTM 모션, 경쟁 배틀카드 | `/plan-launch`, `/growth-strategy`, `/battlecard` |
| 7 | pm-marketing-growth | 5 / 2 | 마케팅 아이디어, 포지셔닝, 가치 제안 문구, 제품 이름 짓기, 노스스타 지표 | `/market-product`, `/north-star` |
| 8 | pm-toolkit | 4 / 5 | PM 이력서 리뷰·맞춤화, NDA 초안, 개인정보처리방침(GDPR/CCPA), 문법·흐름 교정 | `/review-resume`, `/tailor-resume`, `/draft-nda`, `/privacy-policy`, `/proofread` |
| 9 | pm-ai-shipping | 3 / 5 | **AI로 만든(바이브 코딩) 앱 출시 전 점검**: 시스템 문서화, 정확성 코드리뷰, 보안·성능 정적 감사, 테스트 커버리지 맵, 출시 패킷 | `/ship-check`, `/document-app`, `/derive-tests`, `/security-audit-static`, `/performance-audit-static` |

### 그 외 파일들의 역할
- `validate_plugins.py`, `tests/`: 저장소 관리용 품질 검사(필수 항목, 이름 일치, 개수, 버전 동기화). 사용자가 플러그인을 쓸 때는 실행되지 않는다.
- `.github/workflows/tag-on-merge.yml`: `main`에 CHANGELOG의 새 `## vX.Y.Z` 제목이 올라가면 자동으로 태그와 릴리스를 만든다.
- 스킬 끝의 `Further Reading`: 저자(Paweł Huryn)의 Product Compass 글 링크(56개 스킬).
- 기반 이론: Teresa Torres, Marty Cagan, Alberto Savoia, Ash Maurya, Strategyzer 등 유명 PM 서적의 프레임워크.

### 언제 쓰나?
- 새 아이디어가 있을 때 → `/discover`
- 사업 방향/전략이 헷갈릴 때 → `/strategy`, `/business-model`
- 기획서(PRD)를 써야 할 때 → `/write-prd`
- 개발 작업을 쪼갤 때 → `/write-stories`, `/test-scenarios`
- 출시·마케팅 준비 → `/plan-launch`, `/north-star`, `/market-product`
- 데이터 분석 → `/write-query`, `/analyze-test`
- AI로 짠 코드를 배포해도 되는지 확인 → `/ship-check`

### 나(개발자)한테 무슨 도움이 되나?
1. **사이드 프로젝트 기획을 체계적으로**: 아이디어 → 가정 검증 → PRD → 유저 스토리 → 테스트 시나리오까지 한 흐름으로.
2. **바이브 코딩 결과물 안전 점검**: `pm-ai-shipping`이 권한·보안·N+1 쿼리 같은 문제를 "문서에 적힌 의도 vs 실제 코드" 비교로 찾아준다. React/PHP로 빠르게 만든 앱에 특히 유용.
3. **개발 보조 산출물 자동화**: SQL 생성, 더미 데이터(CSV/JSON/SQL), 테스트 시나리오, 릴리스 노트.
4. **출시·수익화 준비**: 가격 전략, GTM, 마케팅 문구, 제품 이름.
5. **커리어**: PM 이력서 리뷰/맞춤화.
6. **AI 에이전트·프롬프트 설계 교과서**: 잘 만든 SKILL.md의 구조 자체가 학습 자료.

---

## 2. 더 쉬운 설명 (비유 버전)

- **Claude = 실력 좋은 요리사.** 뭐든 만들 수 있지만, 우리 가게 레시피는 모른다.
- **스킬 = 레시피 카드.** "PRD는 이 8단계로 만든다" 같은 카드. 손님(나)이 PRD 얘기를 꺼내면 요리사가 알아서 그 카드를 꺼내 본다.
- **커맨드 = 코스 요리 주문 버튼.** `/discover` 버튼을 누르면 "애피타이저(아이디어) → 메인(가정 검증) → 디저트(실험 계획)"가 순서대로 나온다. 중간에 "이대로 갈까요?" 물어봐 준다.
- **플러그인 = 레시피 책 한 권.** 기획 책, 전략 책, 마케팅 책… 총 9권.
- **마켓플레이스 = 그 책들을 파는 서점.** 서점 주소(`phuryn/pm-skills`)만 등록하면 9권을 골라 설치할 수 있다.

**한마디로:** "AI한테 'PM 선배의 업무 매뉴얼'을 쥐여 줘서, 기획·전략·출시·점검을 선배처럼 순서대로 해 주게 만드는 도구."
**코딩 지식 없이도** 채팅창에 `/write-prd 우리 앱 알림 기능`처럼 치기만 하면 된다.

---

## 3. 질문별 답변

### 3-1. 설치 및 사용법
**Claude Code (터미널)**
```bash
# 1) 마켓플레이스 등록 (원본 또는 내 포크)
claude plugin marketplace add phuryn/pm-skills
# 내 포크를 쓰고 싶으면: claude plugin marketplace add bmshin94/pm-skills

# 2) 필요한 플러그인 설치
claude plugin install pm-execution@pm-skills
claude plugin install pm-product-discovery@pm-skills
claude plugin install pm-ai-shipping@pm-skills
# ... 나머지도 같은 방식 (총 9개)
```
Claude Code 안에서는 `/plugin` 메뉴로도 같은 작업을 할 수 있다.

**Claude Cowork (데스크톱 앱, 비개발자 추천)**
Customize → Browse plugins → Personal → `+` → Add marketplace from GitHub → `phuryn/pm-skills` 입력.

**Codex CLI (OpenAI)**
`codex plugin marketplace add phuryn/pm-skills` → `codex plugin add pm-execution@pm-skills` … (스킬은 동작, `/커맨드`는 Codex에서 슬래시 명령으로 실행되지 않으므로 자연어로 단계를 요청).

**Gemini CLI / Cursor / OpenCode / Kiro**
`pm-*/skills/*` 폴더를 각 도구의 skills 폴더(`.gemini/skills/`, `.cursor/skills/` 등)로 복사 → 스킬만 사용 가능.

**사용법**
- 커맨드: `/write-prd 알림 피로도를 줄이는 스마트 알림`, `/discover 원격팀용 AI 회의 요약기`, `/ship-check 결제 서비스`
- 스킬: 그냥 대화 — "우리 앱 아이디어의 가장 위험한 가정이 뭐야?" (자동 로딩)
- 스킬 강제 로딩: `/pm-execution:create-prd` 또는 `/create-prd`
- 한국어로 질문해도 되고, 결과를 한국어로 달라고 요청하면 된다.

### 3-2. 이거 플러그인이야? 스킬이야? MCP야?
- **플러그인 마켓플레이스**다. 그 안에 **플러그인 9개**, 각 플러그인 안에 **스킬 + 커맨드**가 들어 있다. → "플러그인이면서 스킬 묶음"이 정답.
- **MCP는 아니다.** MCP는 Notion·Jira·DB 같은 외부 시스템과 연결하는 서버(프로그램)인데, 이 저장소에는 MCP 서버 설정(`.mcp.json`)도, 실행 코드도 없다. 전부 마크다운 지침이다.
- 함께 쓰면 좋다: 예) Jira MCP + `/write-stories` → 스토리를 만들어 바로 Jira에 등록.

### 3-3. API 토큰을 사용해야 돼?
- **플러그인 자체에는 별도 API 키가 필요 없다.** 설치에 돈도 들지 않는다(MIT, 무료).
- 다만 플러그인을 **돌리는 AI 도구**는 계정이 필요하다:
  - Claude Code / Cowork → Claude 유료 구독(Pro/Max/Team/Enterprise) 로그인 **또는** Anthropic API 키(사용량 과금).
  - Codex → OpenAI 계정.
- 실행하면 그 계정의 사용량(토큰)이 소모된다. `/ship-check`, `/discover`처럼 긴 워크플로는 사용량을 많이 쓴다.
- **직접 웹 서비스를 만들 경우**(3-6 참고)에는 Anthropic API 키가 필요하고 토큰당 과금된다. 키는 반드시 서버(PHP)에만 두고 프런트(React)에는 절대 노출하지 않는다.

### 3-4. AI 에이전트를 구축하는 데 도움이 될까?
**도움이 된다 — 특히 "설계 교과서"로.**
1. **프롬프트/스킬 설계 표본 69개**: frontmatter의 `description`에 트리거 문구를 넣어 자동 로딩되게 하고, 본문에는 목적·맥락·단계별 지시·출력 템플릿을 두는 구조(점진적 공개, progressive disclosure).
2. **멀티스텝 워크플로 설계**: 커맨드가 스킬을 체인으로 엮고 중간 체크포인트에서 사람 확인을 받는 패턴(Human-in-the-loop).
3. **고급 에이전트 패턴(pm-ai-shipping)**: 서브에이전트 병렬 분산(fan-out), 발견 사항 스스로 반박(self-refute), 증거(파일:라인) 필수, 감사 대상 코드를 "신뢰할 수 없는 입력"으로 취급(프롬프트 인젝션 방어), 다른 모델로 독립 교차 검토.
4. **품질 관리**: `validate_plugins.py` + `tests/` + CI로 스킬 팩을 검증하는 방법.
5. **재사용 경로**: SKILL.md를 Claude API의 시스템 프롬프트로 넣거나, Claude API의 Skills 기능(커스텀 스킬 업로드 + 코드 실행 컨테이너), Claude Agent SDK(Claude Code를 라이브러리로 쓰는 SDK), Managed Agents(스킬·MCP 지원)에 그대로 활용할 수 있다.

**한계**: 에이전트 "프레임워크"는 아니다. 도구(툴) 코드, 메모리, 외부 연동은 없으므로 실제 에이전트는 API/SDK/MCP로 따로 구성해야 한다.

### 3-5. 수익화할 만한 아이디어가 있어?
있다. 자세한 내용은 4장 참고. 요약: ① 한국형 스킬팩 판매 ② React+PHP 기반 "AI 기획서 생성기" SaaS ③ 바이브 코딩 앱 출시 점검 대행 ④ 외주 생산성 향상 ⑤ 유튜브·유료 강의 ⑥ 기업 맞춤 스킬 제작 ⑦ 정부지원사업 사업계획서 스킬 ⑧ 템플릿/전자책.

### 3-6. 우리가 React나 PHP로 만들 수 있어?
**가능하다.** 단, 두 가지 의미를 구분해야 한다.
- **플러그인 자체**는 마크다운이라 React/PHP가 필요 없다. 새 스킬을 만들려면 `SKILL.md`만 쓰면 된다.
- **이 스킬들을 쓰는 웹 서비스**는 React + PHP로 만들 수 있다.

구조 예시
```
[React 프런트]  폼/채팅 UI, 마크다운 렌더링, 결과 PDF/Notion 내보내기
      │  (fetch / SSE 스트리밍)
[PHP 백엔드 (Laravel 등)]  로그인·결제·사용량 제한, SKILL.md 로드, API 키 보관
      │
[Claude API]  SKILL.md 본문을 system 프롬프트로 사용
```

PHP 예시 (공식 SDK: `composer require "anthropic-ai/sdk"`)
```php
use Anthropic\Client;

$client = new Client(apiKey: getenv('ANTHROPIC_API_KEY'));
$skill  = file_get_contents(__DIR__ . '/skills/create-prd/SKILL.md');

$message = $client->messages->create(
    model: 'claude-opus-5-5',
    maxTokens: 16000,
    system: [
        ['type' => 'text', 'text' => $skill, 'cacheControl' => ['type' => 'ephemeral']],
    ],
    messages: [
        ['role' => 'user', 'content' => '알림 피로도를 줄이는 스마트 알림 기능 PRD를 한국어로 작성해줘'],
    ],
);

foreach ($message->content as $block) {
    if ($block->type === 'text') {
        echo $block->text;
    }
}
```
팁
- SKILL.md 안의 `$ARGUMENTS` 자리에는 사용자 입력을 치환해서 넣는다.
- 같은 스킬을 반복 사용하므로 프롬프트 캐싱(`cacheControl`)으로 비용을 줄일 수 있다.
- 커맨드처럼 여러 단계를 엮으려면 단계별로 API를 호출하고 중간 결과를 다음 단계 입력으로 넘긴다.
- 모델 가격(2026-10 기준, 100만 토큰당 입력/출력): Opus 5.5 $4/$20, Sonnet 5.5 $2/$10, Haiku 5.5 $0.10/$0.50.

### 3-7. 유튜브 강의 영상으로 제작 가능할까?
**가능하고, 한국어 콘텐츠가 거의 없어 기회가 크다.** MIT 라이선스라 소개·시연·변형이 자유롭다(원저작자 표기와 저장소 링크 권장).

시리즈 구성 예시
1. 입문: "AI에게 PM 선배를 붙여주는 법" — 개념(스킬/커맨드/플러그인) + 설치
2. 아이디어 검증: `/discover`로 사이드 프로젝트 아이디어 검증 라이브
3. 기획서: `/write-prd` → `/write-stories` → `/test-scenarios`로 개발 착수까지
4. 전략·가격: `/strategy`, `/pricing`, `/business-model`
5. 출시: `/plan-launch`, `/north-star`, `/market-product`
6. 바이브 코딩 안전 점검: React/PHP 앱에 `/ship-check` 실행 (조회수 기대 큼)
7. 개발자 심화: SKILL.md로 나만의 스킬 만들기
8. 실전 프로젝트: React + PHP로 "AI 기획서 생성기" 만들기 (3-6 응용)

주의
- 화면에 API 키·개인 정보 노출 금지
- 원본 저장소 링크와 저자 표기, 저자 브랜드(Product Compass)를 내 것처럼 쓰지 않기
- AI 결과물은 검토가 필요하다는 점 명시

---

## 4. 수익화 아이디어 상세

| # | 아이디어 | 난이도 | 초기 비용 | 수익 형태 |
|---|---|---|---|---|
| 1 | 한국형 PM 스킬팩 | 낮음 | 거의 없음 | 판매/구독 |
| 2 | AI 기획서 생성기 SaaS (React+PHP) | 중~높음 | 서버+API 비용 | 월 구독/크레딧 |
| 3 | 바이브 코딩 앱 출시 점검 대행 | 중간 | 거의 없음 | 건당 용역 |
| 4 | 외주·프리랜서 생산성 향상 | 낮음 | 구독료 | 간접(단가·처리량↑) |
| 5 | 유튜브 + 유료 강의 | 중간 | 장비/시간 | 광고·강의 판매 |
| 6 | 기업 맞춤 스킬 제작/도입 컨설팅 | 중간 | 거의 없음 | B2B 계약 |
| 7 | 정부지원사업 사업계획서 스킬/서비스 | 중간 | 낮음 | 판매/구독/컨설팅 |
| 8 | 템플릿·전자책 | 낮음 | 거의 없음 | 디지털 상품 판매 |

### ① 한국형 PM 스킬팩
- **무엇**: 원본 스킬을 한국어·한국 시장에 맞게 재작성. 예) 한국식 기획서/요구사항정의서 양식, 네이버·카카오·인스타 중심 GTM, 원화 가격 전략, 한국 개인정보보호법 관점 체크리스트.
- **판매처**: 크몽, Gumroad, 자체 사이트. 비공개 GitHub 저장소 접근권 판매 방식도 가능.
- **가격 예시**: 스킬팩 1~5만 원, 업데이트 구독 월 5천~1만 원.
- **포인트**: MIT라 상업적 변형 가능(원본 저작권 고지 유지). 번역만으로는 차별화가 약하므로 "한국 상황 특화"가 핵심.

### ② AI 기획서 생성기 SaaS (React + PHP)
- **무엇**: Claude Code를 모르는 사람(초기 창업자, 학생, 기획 초보)을 위한 웹 서비스. 질문 몇 개에 답하면 PRD·린 캔버스·페르소나·경쟁사 분석 문서를 생성하고 PDF/Notion으로 내보낸다.
- **구조**: 3-6의 React + PHP + Claude API 구조.
- **원가 감각(대략)**: 문서 1건에 입력 5천 + 출력 4천 토큰이라고 가정하면 Sonnet 5.5 기준 약 $0.05, Opus 5.5 기준 약 $0.10. (실제 사용량은 측정 필요)
- **요금 예시**: 무료 3건/월 → 프로 월 9,900~19,900원(무제한에 가까운 한도) → 팀 플랜.
- **차별화**: 한국어 양식, 문서 간 연결(아이디어 → PRD → 유저 스토리 → 테스트 시나리오), 팀 공유·버전 관리.
- **리스크**: 범용 AI 챗봇과의 경쟁 → "정해진 양식·체계·품질"과 워크플로 연결로 승부.

### ③ 바이브 코딩 앱 출시 점검 대행
- **무엇**: AI로 앱을 만든 1인 창업자·소상공인·스타트업의 코드를 `pm-ai-shipping`(`/document-app`, `/ship-check`, `/security-audit-static`, `/performance-audit-static`)으로 점검하고, 사람이 검증한 보고서를 납품.
- **가격 예시**: 소규모 앱 30만~100만 원/건, 재점검·유지보수 월 계약.
- **포인트**: 개발자인 내가 AI 결과를 **직접 검증**한다는 것이 가치. 보고서에 "정적 분석이며 모든 취약점을 보장하지 않음" 면책 문구 필수.

### ④ 외주·프리랜서 생산성 향상
- `/write-prd`, `/write-stories`, `/test-scenarios`, `/generate-data`, `/write-query`로 견적·기획·QA 문서를 빠르게 만들어 납기 단축 → 같은 시간에 더 많은 프로젝트 또는 "기획 포함 패키지"로 단가 상승.

### ⑤ 유튜브 + 유료 강의
- 3-7의 시리즈로 무료 유튜브 → 인프런/클래스101 등 유료 강의 → 기업 워크숍 순으로 확장.
- 강의 상품 예시: "개발자를 위한 AI PM 워크플로", "바이브 코딩 앱 안전하게 출시하기", "나만의 Claude 스킬 만들기".

### ⑥ 기업 맞춤 스킬 제작/도입 컨설팅
- 회사의 기획서 양식, 코드리뷰 규칙, 보고서 포맷을 SKILL.md로 만들어 팀 전체가 같은 품질로 쓰게 해 주는 B2B 서비스.
- 산출물: 사내 플러그인 저장소 + 검증 스크립트(이 저장소의 `validate_plugins.py`·`tests/` 구조 참고) + 교육.

### ⑦ 정부지원사업 사업계획서 스킬/서비스
- 예비·초기창업패키지 등 사업계획서는 문제 인식·실현 가능성·성장 전략·팀 구성 같은 정형 항목이 있어 스킬과 궁합이 좋다.
- 기존 스킬 조합 활용: `lean-canvas`, `market-sizing`, `competitor-analysis`, `beachhead-segment`, `pricing-strategy`, `pre-mortem`.
- 상품: 스킬팩 판매, 웹 서비스(②와 결합), 1:1 검토 컨설팅.

### ⑧ 템플릿·전자책
- 스킬 결과물을 Notion/Google Docs 템플릿, "AI로 기획하는 법" 전자책으로 판매. 강의·유튜브와 묶음 상품으로.

### 추천 실행 순서
1. **1~2주차**: 유튜브 1~2편 + 한국형 스킬팩 소규모 버전(무료 배포)으로 반응 확인
2. **3~6주차**: 반응 좋은 주제로 유료 스킬팩/출시 점검 대행 시작 (초기 비용 0에 가까움)
3. **2~3개월차**: 수요가 확인되면 React+PHP SaaS MVP 개발
4. 각 단계 검증 자체를 이 저장소의 `/discover`, `/pricing`, `/plan-launch`로 진행

### 공통 주의사항
- **라이선스**: MIT — 상업적 이용·수정·재배포 가능, 단 원본 저작권·라이선스 고지 유지.
- **브랜드**: 원저자 이름·Product Compass 브랜드를 내 상품처럼 쓰지 않기.
- **법률 문서**: NDA·개인정보처리방침 결과물은 초안일 뿐이며, 판매·제공 시 법률 전문가 검토 필요(원본은 GDPR/CCPA 기준).
- **품질 책임**: AI 결과물은 사람이 검증한 뒤 납품.
- **API 키 보안**: 키는 서버에만 보관, 사용량 한도·과금 알림 설정.

---

## 참고 링크
- 원본 저장소: https://github.com/phuryn/pm-skills
- 내 포크: https://github.com/bmshin94/pm-skills
- 저자 뉴스레터: https://www.productcompass.pm
- 동반 프로젝트 PM Brain: https://github.com/phuryn/pm-brain
