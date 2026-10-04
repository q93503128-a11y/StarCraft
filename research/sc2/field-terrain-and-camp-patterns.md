# 사냥터 지형·무리·순찰 구현 조사

2026-10-04. 작업마다 실제 제작자 코드나 장면을 먼저 확인한다. 아래는 이번 배치에서 읽은 자료와 실제 적용 범위다. 참고, 코드 재사용, 실행 검증을 구분한다.

| 자료 | 실제 확인 | 적용 |
|---|---|---|
| [Tristram 제작자 페이지](https://www.curseforge.com/sc2/maps/tristram)의 [마을](https://media.forgecdn.net/attachments/160/323/Tristram_2.JPG), [필드](https://media.forgecdn.net/attachments/160/327/Tristram_6.JPG) | 풀 가장자리, 굽은 흙길, 암석·나무로 구역을 구분한 실제 장면 | 기존 생성점을 연결하는 흙길, 풀 경계, 마을 바닥 구성에 참고. 이미지·모델·스크립트는 가져오지 않았다. |
| [Blizzard TerrainData](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/liberty.sc2mod/base.sc2data/GameData/TerrainData.xml), [TerrainTexData](https://github.com/SC2Mapster/SC2GameData/blob/master/mods/liberty.sc2mod/base.sc2data/GameData/TerrainTexData.xml) | BelShir의 8개 실제 텍스처 ID와 카탈로그 | DirtLight/DirtDark/Brush/GrassLight/GrassDark/SmallTiles/BricksSmall/BricksLarge 팔레트 ID를 재사용. 그래픽은 SC2 의존성에서 제공한다. |
| [Blizzard 지형 가이드](https://s2editor-guides.readthedocs.io/Classic_Tutorials/01_Terrain_Module/1/), [Terrain Layer](https://s2editor-guides.readthedocs.io/New_Tutorials/02_Terrain_Editor/020_Terrain_Layer/) | 텍스처 혼합, 길과 주변 재질, 높이와 보행 차단의 차이 | 흙길을 풀과 혼합. 기존 보행 차단을 유지하고 높이만으로 단절을 주장하지 않는다. |
| [UA3 실제 Galaxy](https://github.com/DrSuperGood/SC2-UA3/blob/5083f0e9eef272af3bb947d11b3b1d0e29c22a7f/Undead%20Assault%203%202015.SC2Map/MapScript.galaxy), gt_LNPeriodicRally_Func, 30360~30410 | 영역 내부의 무작위 명령과 영역 밖 귀환; WanderingLoop는 별도 민간인 순찰 | 전투 대상이 없을 때 작은 영역을 순찰하고 전투 뒤 귀환하는 패턴을 자체 적 루프에 적용. 함수 원문이나 전체 AI를 복사하지 않았다. |

SC2 기본 나무33개·덤불26개를 배치했다. 허브와 사냥터 경계의 기존 암석/보행 차단은 보존한다. MCP에는 팔레트 이름과 대량 브러시 writer가 없어, 새 MCP 작업 사본 안에서만 코덱으로 지형 마스크·팔레트를 수정한 뒤 MCP 검사·패킹·정본 재열기·실행을 거쳤다. 정본 직접 수정이나 구형 빌드 스크립트는 사용하지 않았다.

첫 배치의 적은 저글링18·히드라6·바퀴6,30마리12무리였다. 입구 저글링3마리의 체력28,다른 저글링36,히드라80,바퀴110,피해3/7/8을 사용했다. 타 모드에서 가져온 검증된 밸런스가 아닌 자체 프로토타입 수치다. 감지6칸·생성점 추격10칸,비전투 순찰/귀환,근처 해병이 있으면 재생성을 미루는 규칙이다. 아래 북부 추가에서도 이 수치와 기존 적을 유지했다.

첫 배치 실제 솔로에서 입구 저글링 처치·반복 생성·보상,사냥 군수의 강화 구매를 확인했다. 화면 검토 중 구조된 구간은 자발적 귀환 성공으로 기록하지 않는다. 모든 길/무리 난도·실제2클라이언트·사람의 재미는 별도 검증 대상이다. 당시 미니맵 캐시 문제는 아래 확장에서 실제 에디터 재생성으로 해결했다.

강화 최대 단계에서 남아 있던 구매 가격은 '최대'로 수정했다. 기존 NOTD 반복 행 구성 참고를 유지하며 SC2 기본 창만 사용한다. 외부 layout 파일이나 새 UI 디자인은 만들지 않았다. 성공 저장/개발 설명/중복 사용 안내는 게임에 넣지 않는다.

## 192×192확장과 북부 사냥터 — 2026-10-04

확장 전에 실제 제작자 [sc2-file-format-docs](https://github.com/sc2-arcade-watcher/sc2-file-format-docs)를 읽었다. MapInfo v39 문자열/필드 스트림과 무결성 공식,VTCL좌표 패치,MASK타일,CLIF/HardTile/Fluff를 확인했다. 로컬 source-snapshots/terrain-format에 실제 읽은 파일을 보존했다. 기존 렌더·동기화 지형 배열과 마스크 타일을 기존 좌표 그대로 확대하고 새 공간을 추가했다. MCP지형 코덱과 자체 staging전용 보조 코드에 문서 형식을 적용했다. 제작자 Galaxy·시각 자산을 복사하지 않았다.

[The Thing Objects](https://raw.githubusercontent.com/willuwontu/thethingrevivalremade.SC2Map/master/Objects)를 실제 읽고 Doodad직렬화를 참고했다. 원래 장면이나 모델을 가져오지 않고 SC2기본 BelShirTree42개를 새 지역에 추가했다. 기존59개는 보존했다. 이 형식은 현재 맵에 대한 검증을 거친 것이며 모든SC2맵 버전에서 일반적으로 안전하다는 주장은 아니다.

북부 제작 전에 [Tristram_6](https://media.forgecdn.net/attachments/160/327/Tristram_6.JPG)를 브라우저에서 다시 보았다. 굽은 흙길·풀 경계·나무와 바위의 방향 표식을 참고해 자체104×48지역에 두 길과 무리별 공간을 배치했다. Tristram의 그림·모델·맵 파일은 가져오지 않았다. UA3고정revision30360~30410도 다시 읽고 기존 자체 지역 순찰 루프를 새 적에 적용했다. SC2기본 텍스처/모델/UI만 사용했다.

총지형192×192,북부x80~184,y132~180,별도허브포탈x48,y77,현장귀환x86,y140이다.10개의3마리 무리에9저글링/12히드라/9바퀴,stock암석11개를 추가했다. 총60마리22무리다. 새지역에서 보상이 큰 적의 비중을 높였지만 실측 분당 효율·전체 난도는 미검증이다. 기존HP/피해/보상/강화비는 보존했다.

에디터가 기존미니맵을 그대로 저장하는 문제는 새MCPstaging캐시를 백업·제거하고 패킹 문서를 실제 에디터에서 저장해256×25624bit로 재생성했다. 잘못 추정한MapInfo필드 위치는 쓰기 전 거부됐고 실제 스트림을 파싱해offset58,플레이경계10,8~188,188,무결성130080을 적용했다. 실패한 중간 후보는 정상 플레이 통과로 기록하지 않았다.

정본에서 북부포탈 입장·입구 정상 전투/군수·전투중B8초 거부·안전입구 정지상태B귀환·재실행 군수590과네강화 복원을 실제PrintScreen으로 확인했다. 외부 강제이동/처치/재화지급/시간가속 없음. 북부 모든 길·깊은 무리·장기효율·실제2인 협동·사람의 재미는 남았다. 자세한 구현/검증은 [확장 기록](../../projects/marine-growth/design/field-expansion-and-recall.md)에 있다.

## 후속 탐험·월드 UX

이번 작업에서도 Tristram필드와[마을 장면](https://media.forgecdn.net/attachments/160/321/tristram_1.JPG)을 다시 보고 자연/건축 표식과 길 구성을 참고했다. Blizzard UnitData/AbilData의기본 비콘·내려간 보급고·젤나가 감시탑/TowerCapture를 읽고 실제 유닛/능력을 재사용했다. UA3고정코드26902~26927아이템 이벤트,30881의기존프로필 형태/타입/서명 검사도 읽었다. 인벤토리/사운드 코드는 조사만 했으며,기존 자체 주문·도착 처리와Bank검증을 확장했다. 그림/모델/창 레이아웃/외국 제작자 Galaxy 파일을 가져오지 않았다.

포탈 시작 가시성,보급품6곳개인영구회수,감시탑3곳정찰을 구현했다. 북부 첫 회수·중복 차단·탑 시야·기존Schema1저장 보존한Schema2이전·재접속 개인 상태를 실제 확인했다. 기존 지형 바이너리와전투 수치는 유지했다. 나머지 지점/협동/사람의 재미는 남았다. [상세 출처·적용·검증](../../projects/marine-growth/design/exploration-and-world-ux.md)을 참고한다. 최신 사용자 요청은 콘텐츠·디자인·UX우선이며 실제 밸런스는 사용자가 나중에 조정한다.

## 탐험 갈래·지역 바닥 후속 배치

[Tristram_2 실제 장면](https://media.forgecdn.net/attachments/160/323/Tristram_2.JPG)을 작업 전에 다시 보았다. 이동 공간을 비우고 건축물·수목을 가장자리에 배치하는 구성을 참고했다. Blizzard-TerrainTexData원문 스냅샷의기본 흙/SmallTiles/BricksSmall정의를 다시 읽고 기존 팔레트를 재사용했다. 제작자 이미지·모델·Galaxy·레이아웃을 가져오지 않았다.

탐험 갈래6개,목적지/입구 바닥10곳과수목4그루 이동을 새MCPstaging에 적용했다. 실제에디터 미니맵 재생성 뒤 기존스크립트/지형/Objects바이트 보존을 비교했다. 전투/저장/보행/높이는 그대로다. 정상 북부 첫탑 시야/실루엣·B귀환·외곽 첫보급품 접촉 미회수/직접회수50을 실제PrintScreen에서 확인했다. 나머지4보급품/2탑/사람의 재미/협동은 미검증. [배치·적용·검증 경계](../../projects/marine-growth/design/landmark-routes-and-region-surfaces.md).
