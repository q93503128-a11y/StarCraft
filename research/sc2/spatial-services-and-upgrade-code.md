# 공간 서비스·강화·저장 공개 코드 조사

2026-10-04 갱신. 게임의 소개와 실제로 읽은 코드를 구분합니다. 아래 패턴은 조사·응용 대상이며 외부 코드를 우리 맵에 복사하거나 외부 모드를 직접 플레이했다는 뜻은 아닙니다.

## Crash RPG: 건물 강화의 데이터 연결

확인 리비전: `c83cbb612affb3d62d3a05eb479521bbb648e570`. [제작자 저장소](https://github.com/Alzarath/Crash-RPG).

- [Galaxy 튜토리얼](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/MapScript.galaxy#L2305): 무기고 위치를 기본 선택 모델로 강조하고 공격·방어·에너지의 기본 버튼을 강조한 뒤 해제합니다. 세계의 건물과 명령창을 연결한 안내입니다.
- [능력 데이터](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/Base.SC2Data/GameData/AbilData.xml#L2100): 강화는 즉시 효과 능력이며 자원 비용, 버튼, 연구 조건, 시전자 플레이어가 연결됩니다.
- [효과 데이터](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/Base.SC2Data/GameData/EffectData.xml#L5645): 플레이어 수정 효과가 대응 업그레이드를 한 단계 올립니다.
- [업그레이드 데이터](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/Base.SC2Data/GameData/UpgradeData.xml#L3): 공격·방어·에너지가 기본 속성 행동의 서로 다른 포인트 필드를 바꿉니다.

우리 작업에는 시설 위치로 접근하는 흐름을 적용했습니다. 비용 처리는 아직 Galaxy 함수이며 위 데이터 체인을 그대로 구현한 상태는 아닙니다. 후속으로 여러 강화의 비용·효과·요구조건을 데이터에 모으고, 명령창에서 구매 전 정보를 확인하게 하는 근거로 삼습니다. 저장소는 LGPL-3.0을 표기하므로 코드를 실제 재사용할 때 조건을 별도로 적용합니다.

## Night of the Dead: 상점 화면의 반복 구조

확인 HEAD: `6153a6e9d09492574258c6b753a88f7253736fe6`. [Shop.SC2Layout](https://github.com/ArcanePariah/Night-of-the-Dead/blob/6153a6e9d09492574258c6b753a88f7253736fe6/src/NOTD.SC2Map/Base.SC2Data/UI/Layout/Shop.SC2Layout).

행·열 간격 상수, 탭 버튼과 상품 버튼 템플릿, 상품 페이지와 탭 컨트롤을 분리합니다. 항목마다 새 화면을 만드는 대신 공통 구조를 재사용하는 참고입니다. 우리 맵에는 이 레이아웃을 가져오지 않았으며 현재 바닐라 경계에서는 시설에서 여는 기본 SC2 창의 반복 행에 응용합니다. 다른 모드의 실제 상점 스크린샷을 이번 작업에서 비교·검증한 것은 아닙니다.

## Undead Assault 3: 개인 저장의 검증과 버전

확인 HEAD: `5083f0e9eef272af3bb947d11b3b1d0e29c22a7f`. [MapScript.galaxy](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/MapScript.galaxy#L2658).

저장 로더는 섹션·키·값 타입을 검사하고 계정 식별자와 Bank 서명을 확인합니다. 저장 함수는 인증 상태를 확인합니다. 개인 진행 직렬화에는 버전 번호와 값의 범위 제한이 있습니다. 상위 저장 함수에는 유효하지 않은 상태를 지우는 경로도 있으므로 그대로 채택하지 않습니다. 우리 프로필은 읽기 실패 시 원본을 보존하고 쓰기를 막는 쪽으로 설계합니다. 복잡한 비트 압축·암호 구현은 초기 네 강화 저장에 필요하지 않습니다. 우리 맵의 Bank 저장은 사전 로딩 수정 뒤 솔로 정상 맵 종료·재실행에서 군수와 체력 강화 복원을 확인했습니다. 멀티 저장은 미검증입니다.

## 제작자 소개와 화면으로 확인한 다른 사례

[Curse of Tristram 제작자 페이지](https://www.curseforge.com/sc2/maps/tristram)는 솔로/파티, 상점, 인원에 따른 적 변화, 저장·불러오기, 이벤트를 설명합니다. 공유 사냥과 개인 성장의 공존을 참고합니다. 해당 모드의 내부 코드·포탈 구현은 이번에 확인하지 않았습니다.

## 현재 코드 선택

플레이어별 진행 배열과 공유 적을 분리하고, 동기화된 게임 시간으로 적과 보상을 처리합니다. 한 사람의 귀환이 사냥터를 정리하지 않게 했습니다. 공격은 플레이어별 피해 효과 카탈로그, 방어는 유닛 방어 카탈로그, 체력·속도는 유닛 속성으로 적용합니다. UI 클릭 콜백만 늘리는 대신 시설을 목표로 한 일반 명령을 처리합니다.

[Blizzard 원본 예제가 포함된 명령 이벤트 API](https://mapster.talv.space/galaxy/reference/trigger-add-event-unit-order), [이동속도 속성 API](https://mapster.talv.space/galaxy/reference/unit-set-property-fixed), [카탈로그 수정 API](https://mapster.talv.space/galaxy/reference/catalog-field-value-set)를 함께 확인했습니다. 문법 검사 통과와 실제 런타임 적용은 별도 판정합니다.

## 2026-10-04 실제 적용과 추가 조사

무기고 하나에서 네 강화를 구매하는 SC2 기본 창을 연결했습니다. NOTD의 반복 행 구조를 참고해 효과·단계·가격을 같은 순서로 표시하고, 구매 후 같은 창을 갱신합니다. 외부 SC2Layout/아트 파일은 복사하지 않았습니다. 접촉만으로 구매하는 방식은 제거했습니다. 개발 진행·정상 저장 상태·“모든 강화는 여기서” 같은 설명은 게임 문구에서 제외합니다.

UA3의 [BankList.xml](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/BankList.xml)은 이름과 플레이어가 고정된 사전 로딩 목록을 실제로 포함합니다. [에디터 Bank 설명](https://s2editor-guides.readthedocs.io/New_Tutorials/03_Trigger_Editor/051_Banks/)도 사전 로딩을 플레이어별 로딩·동기화로 설명합니다. 처음에는 이 목록을 빠뜨려 파일에 구매 값이 기록돼도 재실행 때 신규 상태로 취급되는 실패가 관찰됐습니다. 수정본에 1~4인 목록을 넣고 BankLoad/Wait 뒤 존재·섹션을 검사합니다. 기존 저장 로더의 타입/서명 검사만으로 사전 로딩을 대체할 수 없습니다. 실제 재검증 결과는 프로젝트 제작 기준에 별도로 기록합니다.

UA3 Galaxy의 도시 지역 초기화(26575~26597)와 WanderingLoop(26619~26664)는 지역별 유닛 그룹과 지역 내 임의 지점 명령을 사용합니다. 이는 민간인/방랑자 코드이며 적 사냥터 생성기로 소개하지 않습니다. 우리 적 배치는 종류·생성점·재생성 상태를 분리한 24개 지점으로 구성했습니다. 외부 무리 밸런스 수치를 복사한 것은 아니며 실제 난도는 조정 중입니다.

## 실제 확인한 외부 화면

Curse of Tristram 제작자의 [마을 화면](https://media.forgecdn.net/attachments/160/321/tristram_1.JPG)과 [스킬·장비 화면](https://media.forgecdn.net/attachments/160/346/Screenshot2012-04-26_19_51_33.jpg)을 브라우저에서 직접 보았습니다. 마을은 세계 안 건물·지형과 기본 RTS HUD를 함께 쓰고, 스킬·장비는 SC2 스타일 창에서 페이지와 요구치를 구분합니다. 우리 작업에는 공간 서비스와 기존 SC2 창 사용 원칙을 참고합니다. 해당 내부 코드나 포탈 동작은 확인하지 않았고, 외부 모드를 직접 플레이하지도 않았습니다. 제작자 페이지는 All Rights Reserved를 표기하므로 이미지나 모델을 맵 자산으로 가져오지 않았습니다.

[Blizzard 기본 네이티브 원본 미러](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/core.sc2mod/base.sc2data/TriggerLibs/natives.galaxy)와 실제 게임을 대조해 카메라 입력 잠금·Y 전환, 테란 UI 종족 지정, 보행 차단을 구현했습니다. 낡은 네이티브 선언의 c_pathingNoPathing이 실제 빌드에 없어 컴파일 실패했고 현재 선언의 c_pathingUnpathable로 수정한 뒤 실제 차단을 확인했습니다. 문법 검사만으로 런타임 성공을 판단하지 않습니다.
