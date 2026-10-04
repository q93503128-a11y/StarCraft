# 공개 SC2 맵의 코드·제작 방식

2026-10-03 조사. 이 문서는 구현 패턴을 조사한 기록입니다. 다른 제작자의 코드를 그대로 복사하거나 새 맵에 적용했다는 뜻은 아닙니다.

시설 강화의 데이터 체인과 개인 저장 검증을 실제로 읽은 후속 기록은 [공간 서비스·강화·저장 조사](spatial-services-and-upgrade-code.md)에 있습니다. 각 항목에 리비전, 원본 파일, 현재 적용 여부를 구분했습니다.

## Crash-RPG: 데이터 기반 경험치와 예산형 생성

[Crash-RPG](https://github.com/Alzarath/Crash-RPG)는 공개된 SC2 맵 프로젝트이며 Galaxy 스크립트와 유닛/업그레이드 데이터가 포함되어 있습니다.

- 공개 `MapScript.galaxy`에서 처치/웨이브 생성 로직을 별도 함수로 나누고, 생성 루프에 동시 적 공급량 제한을 둡니다. [생성 루프](https://github.com/Alzarath/Crash-RPG/blob/master/CrashRPG.SC2Map/MapScript.galaxy#L577-L690)
- 적의 종류를 무작위로 고르고 각 적의 비용을 남은 생성 예산에서 차감하는 예산형 구성입니다. 웨이브와 플레이어 수로 생성량을 늘립니다. [예산 공식](https://github.com/Alzarath/Crash-RPG/blob/master/CrashRPG.SC2Map/MapScript.galaxy#L828-L830)
- 레벨별 필요 경험치는 Galaxy 코드의 중복 상수 대신 `CRPGHeroVeterancy` 데이터 카탈로그에서 읽어 누적합니다. [레벨 경험치 조회](https://github.com/Alzarath/Crash-RPG/blob/master/CrashRPG.SC2Map/MapScript.galaxy#L1054-L1068)

**시사점:** 우리 게임이 계약·사냥 중심으로 바뀌더라도 “규칙 수치는 데이터 한 곳에 두고, Galaxy는 진행 이벤트와 보상 계산을 연결”하는 접근은 유용합니다. 예산형 생성은 웨이브를 선택할 때 참고할 수 있지만, 웨이브를 기본 루프로 삼지 않기로 한 현재 방향의 후보 시스템은 아닙니다.

## Night of the Dead: 공유 프로젝트 구조와 수동 테스트

[Night of the Dead 공개 저장소](https://github.com/ArcanePariah/Night-of-the-Dead)는 편집 가능한 `.SC2Components` 폴더를 소스로 관리하고, 테스트용 실행 명령과 트리거 디버깅 방법을 문서화합니다. 저장소에는 여러 게임 데이터 파일과 화면별 `.SC2Layout`이 분리되어 있습니다.

- [상점 SC2Layout 원본](https://github.com/ArcanePariah/Night-of-the-Dead/blob/master/src/NOTD.SC2Map/Base.SC2Data/UI/Layout/Shop.SC2Layout)은 반복되는 상점 버튼 템플릿, 탭, 열/행 간격 상수로 화면 구조를 재사용합니다.
- [저장소 README](https://github.com/ArcanePariah/Night-of-the-Dead)에는 테스트 환경에서 Bank를 사용하고, 경험치 명령이나 개별 트리거 실행을 디버그 목적으로 쓰는 방법이 있습니다.

**시사점:** 모드가 커져도 소스와 테스트 방법을 분리해 두는 점은 참고할 수 있습니다. 단, 이 기획 저장소에는 화면 파일이나 게임 맵을 넣지 않습니다. 사용자 지시대로 UI/UX는 외부 실물 사례를 조사하며, 이 커스텀 UI는 구조 참고일 뿐 채택 결정이 아닙니다.

## 데이터, 액터, Galaxy의 책임 분리

- SC2 유닛 데이터는 능력·무기·행동·액터와 연결됩니다. 체력·무기 같은 전투 수치와 기본 공격을 엔진 데이터에 맡기고, Galaxy는 보상·진급·목표·구매 흐름을 맡기는 구성이 조사 자료와 부합합니다. [SC2 Unit 데이터 구조](https://s2editor-guides.readthedocs.io/New_Tutorials/04_Data_Editor/059_Units/)
- 액터는 모델·사운드·애니메이션을 맡지만 클라이언트에서 비동기 처리될 수 있습니다. 액터 상태를 승패나 피해 판정의 기준으로 쓰면 안 됩니다. [Actor 이벤트와 동기화 설명](https://s2editor-guides.readthedocs.io/New_Tutorials/04_Data_Editor/060_Actors/)
- Galaxy 프로젝트는 생성되는 `MapScript.galaxy`를 직접 편집하기보다 별도 소스 파일을 포함시키는 방법을 안내합니다. 트리거 에디터는 이벤트 등록과 맵 초기화에, Galaxy는 재사용 함수와 카탈로그 작업에 활용하는 혼합 접근이 제시됩니다. [GalaxyScript 튜토리얼](https://s2editor-guides.readthedocs.io/New_Tutorials/03_Trigger_Editor/058_GalaxyScript/)

## 이번 단계에서 적용하지 않을 것

- 퀘스트, 장비, 무작위 드롭, 인벤토리를 전부 한 번에 넣지 않습니다.
- 웨이브 생성 예제를 현재 확정 진행 방식처럼 옮기지 않습니다.
- 외부 프로젝트의 UI 파일이나 모델 파일을 라이선스·사용 조건 확인 없이 복사하지 않습니다.
- 테스트 편의용 가속/강제 진행을 사람의 정상 플레이 검증으로 기록하지 않습니다.
