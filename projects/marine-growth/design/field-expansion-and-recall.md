# 사냥터 확장과 비전투 귀환

2026-10-04 사용자 테스트: 현재 밸런스는 괜찮다는 평가. 기존 적 수치·보상·강화 비용을 유지한다. 사용자가 멀리서도 비전투 귀환을 요청했다.

## 현재 구현

해병 기본 명령창의 귀환(B)을 누르면 자기 해병만 전초기지로 돌아간다. 클릭도 같은 능력을 사용한다. 공격하거나 피해를 받은 뒤 8초 동안은 귀환을 거부하고 남은 초를 표시한다. 무료이며 별도 메뉴를 열지 않는다. 기존 현장 귀환 포탈은 유지한다. 귀환은 공유 적을 제거하거나 다른 플레이어를 이동시키지 않는다. 정상 귀환과 구조 모두 기존 개인 저장·회복 처리를 사용한다. 전투 타이머는 세션 상태이며 영구 성장 저장에 넣지 않는다.

SC2 기본 소환 아이콘과 해병 명령창을 사용한다. 새 일러스트·모델·별도 시각 디자인은 만들지 않는다. 툴팁은 기능과 사용 조건만 표시한다.

## 다음 지역 공간 방향

현재 지형은 MCP로 확인한 128×128 셀이다. 허브28~60×48~82, 외곽 사냥터72~124×12~116을 유지하고, 다음 확장에서 192×192를 목표로 여유 영역을 마련한다. 전체 면적은 현재의2.25배가 되지만 이동 거리를 강제로 늘리지 않는다. 새 사냥터는 북쪽/동쪽 추가 영역의 독립 지역으로 만들고, 전초기지의 월드 포탈로 연결한다. 각 지역은 진입점·안전 여유 공간·분기 길·복수 적 무리를 갖춘다. 지역명과 포탈 목적지만 표시한다.

이것은 확장 계획이며 아직 맵 크기를 변경하거나 두 번째 사냥터를 구현하지 않았다. 지형 변경은 원본 지역의 좌표를 보존하고 모든 지형 바이너리·보행·카메라 경계·미니맵을 함께 확인한다. 현 단계에서는 한 정본 맵 안에 지역을 늘려 같은 협동 세션과 개인 성장을 유지한다. 별도 맵 전환은 로비/저장 인계까지 검증한 뒤 별도로 결정한다.

## 근거와 검증

- [Blizzard 지도 생성 가이드](https://s2editor-guides.readthedocs.io/New_Tutorials/01_Introduction/005_Creating_a_Map/): 지도 크기32~256,8단위 증분. [Map Properties](https://s2editor-guides.readthedocs.io/New_Tutorials/01_Introduction/008_Map_Properties/): 전체 크기와 실제 플레이 영역은 구분한다.
- [능력·명령창 예제](https://s2editor-guides.readthedocs.io/New_Tutorials/07_Lessons/090_Basic_Spellswap_System/): 데이터 능력/버튼과 Unit Uses Ability 이벤트를 연결하는 실제 제작 방법을 참고했다. 별도 메뉴 대신 기본 명령창을 사용했다.
- [Crash RPG AbilData](https://github.com/Alzarath/Crash-RPG/blob/c83cbb612affb3d62d3a05eb479521bbb648e570/CrashRPG.SC2Map/Base.SC2Data/GameData/AbilData.xml): 실제 읽은 능력/버튼 정의를 참고했다. 전체 스크립트는 가져오지 않았다.
- [UA3 Galaxy](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/MapScript.galaxy) Frag Out Warning: 능력 이벤트에서 EventUnit을 처리하는 패턴을 읽었다. 자체 소유자/해병 확인과 전투 시간 검사를 적용했다.
- [Blizzard ButtonData](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/liberty.sc2mod/base.sc2data/GameData/ButtonData.xml)의 MassRecall 아이콘 경로를 실제 재사용했다. Marine UnitData 명령창/AbilArray와 Stimpack EffectInstant 정의도 읽었다. 아이콘 파일을 외부에서 내려받지 않고 SC2 의존성을 사용한다.

첫 실행에서 클릭 귀환은 성공했지만 B키와 툴팁 키가 누락됐다. GameHotkeys와 실제 표시 문자열을 enUS/koKR 테이블에 추가했다. 통과/실패와 최종 실제 테스트 결과를 현재 제작 기준에 별도로 기록한다. 멀티 독립 귀환은 실제2클라이언트 시험 전이다.

최종 정본 ReturnHome_v4에서 한국어 귀환[B]/8초 조건을 확인했다. 실제 비전투 입장 후 B허브귀환과, 일반 공격3초 뒤B입력 시8초 제한/필드 유지가 통과했다. 현재 적 수치와 강화 비용은 변경하지 않았다. 정본 실행 전후 런타임/원본 SHA-256도 일치했다.
