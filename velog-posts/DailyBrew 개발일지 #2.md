<h2 id="1-사설">1. 사설</h2>
<p>오늘은 빚을 갚는 날이었다.
기획서와 코드 사이에 벌어진 틈을 메우는 작업. 화려하진 않지만, 이걸 안 하면 나중에 더 크게 터진다.</p>
<p>문서를 4종류 만들어놓고 관리를 안 하면 어떻게 되는지 오늘 뼈저리게 느꼈다.
UI Guide 문서 4개, Dev Spec 문서 4개, C# 스크립트 수십 개.
세 층이 각자 다른 소리를 하고 있었다.</p>
<p>그래서 오늘 한 일은 간단하다. <strong>전부 맞추기.</strong></p>

<h2 id="2-작업-내용">2. 작업 내용</h2>

<h3 id="2-1-cafesystem-design-정리">2-1. CafeSystem_Design 정리</h3>
<p>CafeSystem_Design_v3.1.md에 구버전 잔재가 남아있었다.
"프리셋"이라는 단어가 아직 돌아다니고 있었고, "파산" 시스템도 영업이 하루 단위인 현재 설계에서는 성립하지 않는 개념이었다.</p>
<p>7군데를 수정했다:</p>
<ol>
<li>코어 루프 다이어그램: "4개 프리셋 메뉴" 제거, 기본 메뉴 4종 + 특별 메뉴 구조로 변경</li>
<li>Section 4.1.2: 프리셋 배합표 전체 삭제, C1/C2 코스트 체계로 재작성</li>
<li>등급표: F등급(파산) 제거, S/A/B/C 4등급으로 재설계 (순이익+평판 복합 조건)</li>
<li>파산 메커닉 섹션: 통째로 삭제</li>
<li>프리셋별 재료비 블록: 통째로 삭제</li>
<li>파산 기준 섹션: 통째로 삭제</li>
</ol>
<p>기획 문서에서 유령처럼 떠돌던 구시대 개념들을 성불시켰다.</p>

<h3 id="2-2-ui-guide-vs-dev-spec-동기화">2-2. UI Guide vs Dev Spec 동기화</h3>
<p>UI Guide 문서가 Source of Truth다.
Delta가 설계한 화면 명세서. 여기에 색상, 크기, 애니메이션, 히어라키가 전부 있다.</p>
<p>Dev Spec은 그 Guide를 C# 개발자(Sylvie)가 구현할 수 있게 번역한 문서다.
문제는 Guide가 계속 업데이트되면서 Dev Spec이 따라가지 못했다는 것.</p>
<p>4개 State 전부 비교했다:</p>
<ol>
<li><strong>StartDay</strong>: 28개 불일치 발견, 14개 수정</li>
<li><strong>SetRecipe</strong>: 25개 불일치 발견, 18개 수정</li>
<li><strong>Operation</strong>: 24개 불일치 발견, 19개 수정</li>
<li><strong>DailyEnd</strong>: 25개 불일치 발견, 23개 수정</li>
</ol>
<p>총 102개 불일치, 74개 수정. 나머지는 의도적 차이(Dev Spec이 더 상세한 구현 레벨 정보를 가지고 있는 경우)라서 그대로 뒀다.</p>
<p>주요 수정 사항:</p>
<ol>
<li>weatherPanel을 weatherCard로 통일 (UI Guide 기준)</li>
<li>DOTween Ease.OutQuad/vibrato 제거 (UI Guide에 없는 옵션)</li>
<li>MenuCard 통합 패턴 반영 (BasicMenuCard/SpecialMenuCard 분리 제거)</li>
<li>코스트 색상 C1/C2 팔레트 통일</li>
<li>만족도 임계값 4.0/3.0 기준 정리</li>
<li>등급 시스템 S/A/B/C (F등급 제거)</li>
<li>미니게임 시스템 스펙 추가 (Takeout 2.5s, LatteArt 3.5s, DripCoffee 4.0s)</li>
</ol>

<h3 id="2-3-스크립트-vs-dev-spec-동기화">2-3. 스크립트 vs Dev Spec 동기화</h3>
<p>이게 오늘의 메인 이벤트였다.
Dev Spec 문서가 정리됐으니, 이제 실제 C# 코드가 그 스펙과 맞는지 확인하는 작업.</p>
<p>프로젝트 내 Scripts 폴더의 모든 스크립트를 읽고, Dev Spec에 없는 코드는 제거하고 누락된 부분은 추가했다.</p>
<p><strong>StartDay (7개 변경):</strong></p>
<ol>
<li>StartDayStateUI.cs: eventTitle 필드 제거, Show() 리라이트, AnimateSections() Slide In 방식으로 변경, Hide() DOFade 추가</li>
<li>StartDayState.cs: 이벤트명 OnStartClicked를 OnStartButtonClicked로, Show() 호출 시그니처 변경</li>
</ol>
<p><strong>SetRecipe (6개 파일 수정):</strong></p>
<ol>
<li>SetRecipeUI.cs: 프리팹 필드 통합, 이벤트 통합 (OnCoffeeMade/Served/Bought)</li>
<li>SetRecipeState.cs: 전체 리라이트 (OnStartBusinessClicked 단일 핸들러)</li>
<li>TraySlot.cs: GetCostColor() 카페 팔레트 적용</li>
<li>MenuCard.cs: MenuCardType.None 제거</li>
<li>ItemDetailPopup.cs: RecipePreviewData 필드 변경, 크기 업데이트</li>
<li>RecipePreview.cs: Initialize() 단순화</li>
</ol>
<p><strong>Operation (2개 수정 + 7개 신규):</strong></p>
<ol>
<li>OperationStateUI.cs: [SerializeField] 확인</li>
<li>OperationState.cs: 전체 리라이트 (60초 타이머, 미니게임 플로우)</li>
<li>신규: CustomerSlot.cs, CoffeeSlot.cs, MiniGameBase.cs, TakeoutMiniGame.cs, DraggableItem.cs, LatteArtMiniGame.cs, DripCoffeeMiniGame.cs</li>
</ol>
<p><strong>DailyEnd (3개 파일 수정):</strong></p>
<ol>
<li>DailyEndUI.cs: Button 리스너 Start()로 이동, ShowBankruptScreen() 추가, 별 색상/판매율 수정</li>
<li>DailyEndState.cs: CalculateDailyEnd() GetAmount() 사용, CalculateGrade() PlayerInventory 사용</li>
<li>DailyEndData.cs: CurrentInventory 타입 PlayerInventory로 변경</li>
</ol>
<p>최종 상태: 0 에러, 13 경고 (미사용 변수, 이름 섀도잉 등 non-blocking).</p>

<h2 id="3-다음-단계">3. 다음 단계</h2>
<p><strong>CafeScene 먼저 완성하기로 했다.</strong></p>
<p>MainScene은 이미 동작하고, CafeScene으로 진입까지 된다.
문제는 CafeScene 내부 4-State Loop의 개별 State들이 아직 Unity Inspector에서 검증되지 않았다는 것.</p>
<p>순서:</p>
<ol>
<li>StartDay: Inspector 설정 + Play Mode 테스트</li>
<li>SetRecipe: Inspector 설정 + Play Mode 테스트 (가장 복잡)</li>
<li>Operation: Inspector 설정 + 미니게임 Play Mode 테스트</li>
<li>DailyEnd: Inspector 설정 + 등급/정산 Play Mode 테스트</li>
<li>전체 루프: Day 1 시작 ~ Day 1 종료 ~ Day 2 시작 사이클 테스트</li>
</ol>
<p>각 State에서 "다음 State로 전환"이 되는지가 핵심이다.
전환이 막히면 거기서 디버깅하고, 뚫리면 다음으로 넘어간다.</p>

<hr />

<h2 id="4-교훈">4. 교훈</h2>
<p>문서 3층 구조(UI Guide / Dev Spec / Script)는 강력하지만, 동기화 비용이 있다.
한 층을 바꾸면 나머지 두 층도 따라가야 한다.</p>
<p>오늘 102개 불일치를 찾은 건 좋은 일이다.
이걸 런타임 버그로 만나는 것보다 100배 낫다.</p>
<p>AI 에이전트 4개를 병렬로 돌려서 비교/수정한 건 효율적이었다.
사람이 하면 이틀은 걸릴 작업을 한 세션에 끝냈다.
다만 Rate Limit에 한 번 걸려서 기다렸다가 재시도했다. API 사용량 관리가 필요하다.</p>
<p>결론: <strong>문서를 많이 만들수록, 동기화를 자주 해야 한다. 안 하면 빚이 된다.</strong></p>


<hr />
<p>아무리 기본기가 좋다고 하지만, 새로운 기술을 배우는 역량이 떨어지면 도태된다. 
AI기술이 완전하지 못하고 그렇게 큰 영향을 주지 못한다고 하더라도, 사용하는 것을 미룬다면 손바닥으로 하늘을 가리는 것과 같다.
더 나아가 우리는 AI와 어떤 방식으로던지 밀접해질 수 밖에 없다.
뭐 지금이야, 선택지로써 남아있지만 앞으로는 선택지가 아닌 필수가 되게 될 것이다.</p>
<p>거시적으로는 인간의 인식/인지 능력이라는 영역이 AI가 세상에 나옴으로써 그 개념자체가 변할 것 같다는 생각이든다.
좀 과장되게 말하면 언젠가는 이걸 이해하지 못하면 구인류, 이해하고 사용할 수 있다면 신인류로 분류될 것 같다.</p>