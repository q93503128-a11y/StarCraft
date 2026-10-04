# 사냥터 지형·무리·순찰 구현 조사

2026-10-04. 작업마다 실제 제작자 코드나 장면을 먼저 확인한다. 아래는 이번 배치에서 읽은 자료와 실제 적용 범위다. 참고, 코드 재사용, 실행 검증을 구분한다.

| 자료 | 실제 확인 | 적용 |
|---|---|---|
| [Tristram 제작자 페이지](https://www.curseforge.com/sc2/maps/tristram)의 [마을](https://media.forgecdn.net/attachments/160/323/Tristram_2.JPG), [필드](https://media.forgecdn.net/attachments/160/327/Tristram_6.JPG) | 풀 가장자리, 굽은 흙길, 암석·나무로 구역을 구분한 실제 장면 | 기존 생성점을 연결하는 흙길, 풀 경계, 마을 바닥 구성에 참고. 이미지·모델·스크립트는 가져오지 않았다. |
| [Blizzard TerrainData](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/liberty.sc2mod/base.sc2data/GameData/TerrainData.xml), [TerrainTexData](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/liberty.sc2mod/base.sc2data/GameData/TerrainTexData.xml) | BelShir의 8개 실제 텍스처 ID와 카탈로그 | DirtLight/DirtDark/Brush/GrassLight/GrassDark/SmallTiles/BricksSmall/BricksLarge 팔레트 ID를 재사용. 그래픽은 SC2 의존성에서 제공한다. |
| [Blizzard 지형 가이드](https://s2editor-guides.readthedocs.io/Classic_Tutorials/01_Terrain_Module/1/), [Terrain Layer](https://s2editor-guides.readthedocs.io/New_Tutorials/02_Terrain_Editor/020_Terrain_Layer/) | 텍스처 혼합, 길과 주변 재질, 높이와 보행 차단의 차이 | 흙길을 풀과 혼합. 기존 보행 차단을 유지하고 높이만으로 단절을 주장하지 않는다. |
| [UA3 실제 Galaxy](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/MapScript.galaxy), gt_LNPeriodicRally_Func, 30360~30410 | 영역 내부의 무작위 명령과 영역 밖 귀환; WanderingLoop는 별도 민간인 순찰 | 전투 대상이 없을 때 작은 영역을 순찰하고 전투 뒤 귀환하는 패턴을 자체 적 루프에 적용. 함수 원문이나 전체 AI를 복사하지 않았다. |

SC2 기본 나무33개·덤불26개를 배치했다. 허브와 사냥터 경계의 기존 암석/보행 차단은 보존한다. MCP에는 팔레트 이름과 대량 브러시 writer가 없어, 새 MCP 작업 사본 안에서만 코덱으로 지형 마스크·팔레트를 수정한 뒤 MCP 검사·패킹·정본 재열기·실행을 거쳤다. 정본 직접 수정이나 구형 빌드 스크립트는 사용하지 않았다.

적은 저글링18·히드라6·바퀴6, 총30마리/12무리다. 입구도 저글링3마리이며 입구 체력28, 다른 저글링36, 히드라80, 바퀴110을 시험한다. 적 무기 표시 효과의 기본 피해는 3/7/8로 설정했다. 이 수치는 타 모드에서 가져온 검증된 밸런스가 아니라 자체 프로토타입 수치다. 감지6칸·생성점 추격10칸, 비전투 순찰과 귀환, 근처 해병이 있으면 재생성을 늦추는 규칙을 구현했다.

실제 솔로에서 입구 저글링 처치·반복 생성·보상, 사냥으로 번 군수의 강화 구매를 확인했다. 화면 검토 중 게임이 계속 진행돼 구조된 구간은 자발적 귀환 성공으로 기록하지 않는다. 30마리 동시 시야, 모든 길의 보행, 모든 무리 난도, 실제 2클라이언트, 사람의 재미는 별도 검증 대상이다. 미니맵 캐시는 아직 새 지형 길 표현을 반영하지 않는다.

강화 최대 단계에서 남아 있던 구매 가격은 '최대'로 수정했다. 기존 NOTD 반복 행 구성 참고를 유지하며 SC2 기본 창만 사용한다. 외부 layout 파일이나 새 UI 디자인은 만들지 않았다. 성공 저장/개발 설명/중복 사용 안내는 게임에 넣지 않는다.
