# 해병 성장 RPG

상태: 192×192 안전 허브와 외곽·북부 공유 사냥터를 운영하는 프로토타입입니다. 한 해병/플레이어, 스톡 적 3종과 여러 캠프, 한 무기고, 개인 Bank, 영구 레벨·계급·훈련·스킬 성장 구조를 유지합니다. 사냥터 작전은 교신·잔당 소탕·진지 확보이며, 각 작전 순환에는 선택 임무가 붙습니다. 현재 선택 임무는 지정 잔당 처치, 비콘 두 곳 조사, 보급로 방어의 서로 다른 행동으로 나뉩니다. 최근 신호망 조사 코드는 비콘 접근용 지면 이동 명령을 추가했으며 새 자산이나 Bank 필드는 없습니다.

이번 작업은 [UA3/Crash RPG 제작자 코드](design/field-route-and-ux-refinement.md)와 [Deep Rock Galactic·Warframe 공개 자료](design/external-systems-review-and-content-plan.md)를 다시 대조했습니다. 주 목표와 선택 목표 분리, 솔로 완료 가능, 행동 방식에 따른 재방문을 기준으로 기록했습니다. 외부 코드는 읽고 원리만 참고했으며 스크립트·모델·레이아웃은 복사하지 않았습니다.

전용 신호 계약 17개와 필드 UX 117개, 회귀 90개 등 코드 어댑터 검사가 통과했습니다. MCP 문서 검증 오류 0, Galaxy 문법 진단 오류 0입니다. 후보와 유일 정본 `RPG_Test.SC2Map` SHA-256은 `033e8a321612fc8735ece81baeb5044a0e221723b2bab6fff5ebad917036896a`입니다. 실행 전후 정본/runtime 해시가 일치하고 ScriptError·Alerts 로그는 비었습니다. 실제 Print Screen으로 허브와 외곽 필드를 읽었지만 새 신호 임무를 활성화하지는 못했습니다. 최종 좌표 조작은 최신 Sky screenshotId를 얻지 못해 검증 증거로 쓰지 않습니다. 신호 임무 시작·협동·보상과 정상 플레이의 난도·재미는 미검증입니다.

정본 백업, 후보, 캡처, 런타임 해시·명령·로그 기록은 로컬 개발 디렉터리와 `marine-growing/verification`에 보존합니다.

## 문서

- [신호망 선택 임무와 비콘 접근](design/field-route-and-ux-refinement.md): 신호기 2곳 조사, 실제 코드 경계, 캡처 및 미검증 항목
- [외부 콘텐츠 구조와 난이도 가이드](design/field-route-and-ux-refinement.md): DRG/Warframe 공개 시스템 비교와 우리 범위에 적용할 원칙
- [사냥터 경로와 월드 UX 개선](design/field-route-and-ux-refinement.md): 최신 확보 후보6곳·접근자 보호·장식 이름 제거·기본 목표 툴팁 미리보기·검증 경계
- [지역 조사와 협동 활동 구현](design/field-surveys-and-coop.md): 최신 조사6지점·개인 기록·협동 속도·진지 확보 가속·묶음 UI/코드 검사
- [목표 안내와 허브 시설 구현](design/navigation-and-world-services.md): 최신 실제 코드, N 목적지·의무실·보급고·취소·체력 보존과 묶음 UI 검사
- [공유 사냥터 연쇄 정찰 구현](design/shared-field-operations.md): 최신 실제 코드, 세 경로·중간 합류·기여 보상과 UI/코드 검증 범위
- [스킬·훈련·진화 실제 구현](design/skill-training-implementation.md): 최신 실제 코드, 원본 패턴, 초안에서 바뀐 효과와 UI/검증 범위
- [영구 레벨·계급과 사냥터 활동 구현](design/level-rank-and-field-activities.md): 최신 실제 코드, 외부 원본, 수치와 검증 범위
- [기획 초안](design/pitch.md): 초기 스테이지 제안 기록; 현재 진행은 공유 사냥터 기준 우선
- [기획 브리프](design/brief.md): 사용자 방향, 열린 질문, 난도 원칙
- [성장·진행 후보](design/progression-options.md): 웨이브 외 진행 루프 비교
- [레벨·갈래·스킬 설계안](design/growth-and-skills.md): 전투 레벨 상한 제안과 세 역할 갈래, 스킬·패시브 조합
- [핵심 게임 설계안](design/core-game-plan.md): 한 판 흐름, 협동/난도, 보상 경제, 콘텐츠 확장 순서
- [전초기지 메인 허브](design/main-hub.md): 안전한 시작, 정비, 활동 선택과 출격·복귀 흐름
- [마을 시설과 공유 사냥터 제작 기준](design/spatial-hub-and-hunting-prototype.md): 현재 구현, 네 강화, 멀티·저장 검증 경계와 후속 작업
- [성장·보상 밸런스 초안](design/growth-economy-balance.md): 250레벨 XP 곡선, 계급 진급 비용, 지역별 작전 보상, 공격·체력 강화 가격
- [성장 시스템 통합표](design/growth-system-matrix.md): 레벨·계급·훈련·재화·강화·스킬 해금, 전투 수치 공식과 세션 간 저장 연결
- [스킬·패시브 트리와 진화](design/skill-tree-and-evolutions.md): 세 성장 갈래의 액티브/패시브 효과, 해금 순서와 포인트별 빌드 예시
- [작전 보상·저장·재접속 흐름](design/reward-and-save-flow.md): 개인별 협동 보상, 첫 완수 판정, 실패/저장 오류와 Bank 복구 규칙
- [첫 제작 범위와 완료 기준](design/prototype-build-criteria.md): 현재 정본 보존, 첫 해병 전투 프로토타입의 범위와 실제 제작/검증 순서
- [성장 단계별 체감·사냥터 효율](design/progression-feel.md): 레벨별 전투력 목표, 사냥터 성장대 배치, XP/분 및 만렙 소요시간 검증 기준
- [첫 완수부터 만렙까지의 성장 시간 계산](design/pacing-simulation.md): 티어별 첫 완수 누적 레벨과 반복 횟수/시간 시뮬레이션
- [스테이지·캠페인 구조](design/stage-structure.md): 지역, 스테이지 유형, 해금과 반복 보상 제안
- [스테이지와 사냥터 방향 비교](design/stage-vs-hunting-field.md): 고정 작전, 완전 오픈월드, 반개방 사냥터의 장단점
- [콘텐츠 다양성과 성장 체감](design/content-and-power.md): 사냥터 방식 선택, 보스/이벤트/도전 보상 역할, 강해지는 의미
- [첫 지역 플레이 묶음](design/first-region-slice.md): 첫 방문 5개 작전, 반복 계약, 지역 동선과 성장 체감 초안
- [5개 성장대 지역·캠페인 구성](design/region-campaign-outline.md): 성장대별 지역 테마, 25개 핵심 작전, 보스/이벤트 및 반복 조합
- [전투 조우·협동 난도·역할 기여](design/combat-coop-balance.md): 적 전술 역할, 1~4인 편성, 다운/구조, 일반/위험 난도와 테스트 기준
- [결정 기록](decisions/README.md): 합의된 사항과 미결정 사항
- [회의 메모](meetings/README.md): 대화별 방향 변화
- [구현·콘텐츠 전수 대조](implementation-audit-2026-10-04.md): 현재 코드/콘텐츠 범위, 저장·협동 위험, 외부 모드와 공개 코드 비교
- [외부 시스템 대조와 성장·콘텐츠 설계](design/external-systems-review-and-content-plan.md): Guild Wars 2, Destiny 2, Deep Rock Galactic, SC2 협동전의 시스템 비교와 적용안

스타크래프트 II 모드와 제작 방식의 공통 조사 자료는 저장소의 [사례집](../../research/sc2/mode-casebook.md)과 [구현 조사](../../research/sc2/implementation-patterns.md)를 참고합니다.
이번 시설·강화 작업에서 실제로 읽은 코드와 적용 범위는 [공개 코드 조사](../../research/sc2/spatial-services-and-upgrade-code.md)에 기록합니다.
다른 게임의 성장 시스템 비교는 [성장 구조 조사](../../research/comparative/growth-progression.md)에 기록합니다.
Arcade 진행 저장의 가능 범위와 한계는 [SC2 Banks 조사](../../research/sc2/persistent-data.md)를 참고합니다.

이번 지형·무리·순찰 작업의 실제 출처와 검증 범위는 [사냥터 지형·무리 조사](../../research/sc2/field-terrain-and-camp-patterns.md)에 기록합니다.

- [사냥터 확장과 비전투 귀환](design/field-expansion-and-recall.md): 실제192×192확장·북부10무리·월드 포탈·귀환(B),검증과 참고 출처
- [탐험 보급품과 월드 UX](design/exploration-and-world-ux.md): 개인별 영구 회수·감시탑 정찰·포탈 가시성,실제 참고 코드와 검증

- [탐험 갈래길과 지역별 바닥](design/landmark-routes-and-region-surfaces.md): 기존 탐험 지점의 연결 길·기본 포장·수목 시야,정상 접근과 회수 검증
