# 적 캠프 추적 수정과 외부 패턴 대조 — 2026-10-10

## 정본을 직접 읽고 확인한 결함

정본 `RPG_Test.SC2Map`을 MCP read-only workspace로 열어 확인했다. 기존 `MG_SpawnEnemy`는 모든 적을 사냥터 중앙 지점으로 공격 이동시켰고, `MG_Tick`에는 플레이어를 찾는 적 표적 갱신이 없었다. 외곽 입구에서 생성된 적은 해병을 지나쳐 중앙으로 이동할 수 있었다. 과거 로컬 회귀 검사는 “스폰 시 공격 명령 한 번, 반복 명령 없음”만 단언했으므로 실제로 해병을 표적으로 정하는지 검사하지 않았다.

## 제작자 코드와 게임 플레이 대조

- [UA3 MapScript.galaxy — 고정 revision `5083f0e9eef272af3bb947d11b3b1d0e29c22a7f`](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/MapScript.galaxy), preserved source lines 7892–7951: 적 무리를 캠프 단위로 관리하고, 유효 생존자가 있으면 무리에 표적 공격 명령을 주며, 적 유닛 휴면 이벤트를 등록한다. 이 맵은 GPL-2.0 소스로 공개되어 있다. 구현은 기존 개별 적 슬롯/캠프 자료에 맞춰 유효한 같은 필드의 해병을 감지 반경에서 고르고 같은 캠프 대상은 공유하게 했다. 외부 함수/소스는 복사하지 않았다.
- [Crash RPG MapScript.galaxy — 고정 revision `c83cbb612affb3d62d3a05eb479521bbb648e570`](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/MapScript.galaxy), lines 596–625: 생성 지점을 고정하고 짧은 이동 waypoint 순서를 둔다. 이 코드는 구체 동작 비교에만 사용했고 라이선스가 확인되지 않아 복사하지 않았다. 이번 수정은 waypoint 순찰을 추가한 것이 아니라, 표적을 잃은 유닛을 원래 캠프 위치로 돌려보내는 데 한정했다.
- [The Cave RPG 제작자 가이드](https://sites.google.com/view/thecave-rpg/guides/general-guide)는 퀘스트로 여는 여러 tele-pad 지역과 던전 종료 귀환 포탈을 설명한다. [Legends of the Void 제작자 소개](https://us.forums.blizzard.com/en/sc2/t/legends-of-the-void-rpg-official/329)는 여러 구역마다 임무와 보스를 두고 임무 완료 후 다음 구역으로 진행하는 구조를 설명한다. 두 모드의 실제 콘텐츠 진행 폭은 현재 한 맵의 외곽·북부 사냥 구역보다 넓다. 지역별 동선과 해금은 아직 후속 제작 항목이다.

## 적용과 검증

- 정본 코드는 이제 플레이어가 적 종별 감지거리 안에 들어왔을 때만 기본 `attack` 명령으로 같은 필드의 해병을 추적한다. 캠프 짝/세 명 무리는 현재 유효한 표적을 공유한다. 표적이 사라지거나 사냥터를 떠나면 적의 표적 상태를 비우고 자기 생성 지점으로 `move`한다.
- 성장·보상·Bank·수치·자산은 변경하지 않았다. 적 세 종, 22캠프, 같은 단일 192×192 맵도 그대로다.
- 현재 MCP staging 파일을 읽어 네 가지 적 표적/구역 격리/캠프 집결/캠프 복귀 어댑터 검사를 수행해 통과했다. MCP Galaxy 구문 검사 0오류, 문서 검증 10개 범주 0오류, 기존 경고 23개(스톡 버튼 참조 22, 번역 키 1)다. 이는 SC2 엔진의 시야, 경로찾기, 무기 명중이나 동기화 테스트가 아니다.
- 후보/정본/runtime SHA-256: `9a99c3ad3840ee84895accb9ad43fdf1f46cfa8a9669f81c4c7d3c8eead3f67f`. 이전 정본 `aff82d...`를 백업한 뒤 pipeline으로 설치하고 MCP에서 정본을 다시 열었다. `pipeline.mjs`는 `-displaymode 1`로 맵을 열었고 실행 전후 정본/runtime 해시가 일치했으며 ScriptError/Alerts는 비었다.
- 전투는 사용자가 직접 확인하도록 맵을 열어 둔다. 적이 실제로 공격하는지, 죽은 유닛의 시체가 일관되게 보이는지, 멀티플레이·경계 통과·게임 체감은 확인하지 않았다.

## 다음 작업

적의 실제 공격과 시체 표시를 사용자의 정상 플레이에서 확인하고 문제를 다시 기록한다. 이후에는 The Cave와 Legends of the Void의 구역 해금·이동·지역 활동을 바탕으로 레벨 1~100에 필요한 서로 다른 사냥 지역과 길을 정리한다. 다만 이 두 콘텐츠 사례만을 근거로 동작/코드/아트를 가져왔다고 표현하지 않는다.
