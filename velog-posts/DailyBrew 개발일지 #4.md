<h2 id="1-사설">1. 사설</h2>
<p>#2에서 문서 동기화 빚을 갚았고, #3에서 코드 로직 빚을 갚았다.
오늘은 <strong>색상 빚</strong>을 갚는 날이었다.</p>
<p>4개 State의 UI Guide, Dev Spec, C# 스크립트를 전부 열어보니 색상이 제각각이었다.
MiniGameBadge에 <code>#F39C12</code>(웹 오렌지)가 박혀있고, 만족도 표시에 <code>rgb(0.18, 0.8, 0.44)</code>(부트스트랩 그린)가 들어있고, QuantityBadge 배경은 <code>#1C2833</code>(어디서 온 건지도 모를 다크 블루).</p>
<p>카페 게임인데 카페 느낌이 안 나는 색들이 곳곳에 숨어있었다.
그래서 오늘 한 일은 간단하다. <strong>17색 카페 팔레트로 전부 통일하기.</strong></p>
<h2 id="2-작업-내용">2. 작업 내용</h2>
<h3 id="2-1-ui-guide-4개-카페-팔레트-통일">2-1. UI Guide 4개 카페 팔레트 통일</h3>
<p>UI Guide가 Source of Truth이니까 여기서부터 시작했다.</p>
<p>각 문서를 열어서 카페 팔레트에 없는 색상을 전부 찾아 교체했다:</p>
<ol>
<li><strong>StartDay_UI_Guide v1.2.0</strong>: 16색 수정. 날씨 색상(Sunny/Cloudy/Rainy), 트렌드 색상 4종, 이벤트 카드 색상 전부 카페 팔레트로.</li>
<li><strong>SetRecipe_UI_Guide v1.4.0</strong>: 40개 이상의 색상 인스턴스 수정. TraySection 270px로 변경, ConfirmSection을 ContentArea 내부로 이동, ItemDetailPopup 추가까지 구조 변경도 같이.</li>
<li><strong>Operation_UI_Guide v1.2.0</strong>: 6색 수정. MiniGameBadge 3종(<code>#F39C12</code>→<code>#BE9757</code>, <code>#9B59B6</code>→<code>#9B7CB9</code>, <code>#3498DB</code>→<code>#6B8E9B</code>), 만족도 코멘트 색상, QuantityBadge 배경.</li>
<li><strong>DailyEnd_UI_Guide v1.2.0</strong>: 6색 수정 + Grade.F/파산 섹션 130줄 통째로 삭제.</li>
</ol>
<p>교체 기준은 명확했다. 카페 팔레트 17색에 없으면 가장 가까운 카페 색으로 바꾼다.</p>
<table>
<thead>
<tr>
<th>용도</th>
<th>변경 전</th>
<th>변경 후</th>
</tr>
</thead>
<tbody><tr>
<td>Takeout Badge</td>
<td>#F39C12 (웹 오렌지)</td>
<td>#BE9757 (Cafe Gold)</td>
</tr>
<tr>
<td>LatteArt Badge</td>
<td>#9B59B6 (웹 퍼플)</td>
<td>#9B7CB9 (Lavender Latte)</td>
</tr>
<tr>
<td>DripCoffee Badge</td>
<td>#3498DB (웹 블루)</td>
<td>#6B8E9B (Blue Coffee)</td>
</tr>
<tr>
<td>만족도 높음</td>
<td>rgb(0.18, 0.8, 0.44)</td>
<td>#7ED321 (Fresh Green)</td>
</tr>
<tr>
<td>만족도 낮음</td>
<td>rgb(0.91, 0.3, 0.24)</td>
<td>#D26946 (Terracotta)</td>
</tr>
<tr>
<td>트렌드 Sweet</td>
<td>#E91E63 (머티리얼 핑크)</td>
<td>#D4A574 (Caramel)</td>
</tr>
<tr>
<td>트렌드 Strong</td>
<td>#795548 (머티리얼 브라운)</td>
<td>#4E342E (Dark Coffee)</td>
</tr>
<tr>
<td>트렌드 Fresh</td>
<td>#00BCD4 (머티리얼 시안)</td>
<td>#6B8E9B (Blue Coffee)</td>
</tr>
<tr>
<td>트렌드 Mild</td>
<td>#8BC34A (머티리얼 라임)</td>
<td>#9B7CB9 (Lavender Latte)</td>
</tr>
</tbody></table>
<h3 id="2-2-c-스크립트-7개-색상-업데이트">2-2. C# 스크립트 7개 색상 업데이트</h3>
<p>UI Guide를 고쳤으니 코드도 맞춰야 한다. 7개 파일을 수정했다:</p>
<table>
<thead>
<tr>
<th>파일</th>
<th>수정 내용</th>
</tr>
</thead>
<tbody><tr>
<td>StartDayStateUI.cs</td>
<td>Weather 3색(Sunny/Cloudy/Rainy), Trend 4색, Event 3색(Green/Red/Gold)</td>
</tr>
<tr>
<td>MenuCard.cs</td>
<td>Make #7ED321, Buy #BE9757, Serve #6B8E9B, Disabled #8B7355</td>
</tr>
<tr>
<td>CustomerSlot.cs</td>
<td>Takeout/LatteArt/DripCoffee Badge 3색</td>
</tr>
<tr>
<td>TraySlot.cs</td>
<td>Special gold #D4AF37</td>
</tr>
<tr>
<td>RecipeEntry.cs</td>
<td>Unlocked #7ED321, Locked #8B7355</td>
</tr>
<tr>
<td>GoldenGoblinCardUI.cs</td>
<td>Gold highlight #BE9757</td>
</tr>
<tr>
<td>DailyEndUI.cs</td>
<td>STAR_EMPTY #4E342E</td>
</tr>
</tbody></table>
<p>SpecialRecipeCard.cs는 색상이 <code>[SerializeField]</code>로 Inspector에서 설정되는 구조라 코드 수정 대신 코멘트만 업데이트했다. Unity Editor에서 값을 바꿔야 한다.</p>
<h3 id="2-3-layout↔hierarchy-불일치-수정">2-3. Layout↔Hierarchy 불일치 수정</h3>
<p>색상 작업 중에 더 큰 문제를 발견했다.
UI Guide 문서 내부에서 &quot;UI 레이아웃(와이어프레임)&quot;과 &quot;Unity Hierarchy 구조&quot;가 서로 다른 이야기를 하고 있었다.</p>
<p>4개 State를 전부 비교했더니 2건이 걸렸다:</p>
<ol>
<li><strong>StartDay</strong>: 와이어프레임에서 EventCard가 별도 [2] 섹션인데, Hierarchy에서는 MainCardPanel의 자식. → Hierarchy 기준으로 와이어프레임 수정.</li>
<li><strong>SetRecipe</strong>: ConfirmSection이 와이어프레임에서 ContentArea 외부인데, Hierarchy에서는 내부. → Hierarchy 기준으로 수정.</li>
</ol>
<p>Operation은 반대로 UI 레이아웃 기준으로 Hierarchy를 수정했다. 케이스마다 어느 쪽이 의도에 맞는지 판단해서 방향을 정했다.</p>
<h3 id="2-4-dev-spec-4개-ui-guide-동기화">2-4. Dev Spec 4개 UI Guide 동기화</h3>
<p>UI Guide와 C# 스크립트가 정리됐으니, 중간 층인 Dev Spec도 맞춰야 한다.</p>
<ol>
<li><strong>StartDay_Dev_Spec v1.2.1</strong>: Weather/Trend/Event 색상 전부 카페 팔레트, EventCard 위치 반영.</li>
<li><strong>SetRecipe_Dev_Spec v1.4.0</strong>: TraySection 270px, ConfirmSection→ContentArea 내부, ItemDetailPopup, UI Guide v1.4.0 참조.</li>
<li><strong>Operation_Dev_Spec v1.2.0</strong>: CustomerSlot 간소화(Button 제거→IPointerClickHandler), Badge/OrderBubble/WaitGauge/CoffeeSlot 컴포넌트 상세 추가, 이벤트 연결 코드 수정.</li>
<li><strong>DailyEnd_Dev_Spec v1.2.0</strong>: 이미 올바른 상태 확인 완료.</li>
</ol>
<h3 id="2-5-operation_ui_guide-컴포넌트-상세-감사">2-5. Operation_UI_Guide 컴포넌트 상세 감사</h3>
<p>색상 작업 중에 Operation_UI_Guide를 꼼꼼히 읽다가 누락된 컴포넌트 상세를 대량 발견했다.
Hierarchy 트리에는 오브젝트가 나열돼 있는데, 하단 &quot;컴포넌트 상세&quot; 섹션에 스펙이 빠져있는 것들.</p>
<p>문서 전체를 처음부터 끝까지 읽고 9건을 보강했다:</p>
<table>
<thead>
<tr>
<th>#</th>
<th>누락 항목</th>
<th>수정 내용</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>CustomerSlot CanvasGroup</td>
<td>Hierarchy + 컴포넌트 상세에 추가 (DOFade 애니메이션 사용)</td>
</tr>
<tr>
<td>2</td>
<td>CustomerSlot Root 상세</td>
<td>RectTransform, CanvasGroup, IPointerClickHandler 명시</td>
</tr>
<tr>
<td>3</td>
<td>OrderBubble 자식 3개</td>
<td>BubbleBackground, OrderIcon, OrderCount 컴포넌트 상세 추가</td>
</tr>
<tr>
<td>4</td>
<td>WaitGauge &gt; GaugeBackground</td>
<td>RectTransform, Image 컴포넌트 상세 추가</td>
</tr>
<tr>
<td>5</td>
<td>WaitGauge &gt; GaugeFill</td>
<td>Fill Origin/Method 등 상세 보강</td>
</tr>
<tr>
<td>6</td>
<td>CoffeeSlot BadgeBackground</td>
<td>Anchors, Color, Raycast Target, Ignore Layout 추가</td>
</tr>
<tr>
<td>7</td>
<td>CoffeeSlot GrayOverlay CanvasGroup</td>
<td>DOFade 애니메이션용 CanvasGroup 추가</td>
</tr>
<tr>
<td>8</td>
<td>CoffeeSlot LayoutElement</td>
<td>Root의 Preferred Width/Height 추가</td>
</tr>
<tr>
<td>9</td>
<td>이벤트 연결 Button 불일치</td>
<td>GetComponent<button>() → IPointerClickHandler/OnSlotClicked 패턴</td>
</tr>
</tbody></table>
<p>이건 색상 문제보다 더 위험한 누락이었다.
Hierarchy에 &quot;BadgeBackground (Image) - #4E342E&quot;라고만 적혀있고, 정작 LayoutElement Ignore Layout이 빠져있으면?
Unity에서 만들 때 LayoutGroup이 BadgeBackground를 잡아먹는다.
문서에 안 적혀있으면 구현자는 &quot;왜 Badge가 이상한 데 가있지?&quot; 하면서 30분을 날린다.</p>
<h2 id="3-3-layer-동기화-최종-상태">3. 3-Layer 동기화 최종 상태</h2>
<table>
<thead>
<tr>
<th>State</th>
<th>UI Guide</th>
<th>Dev Spec</th>
<th>C# Script</th>
</tr>
</thead>
<tbody><tr>
<td>StartDay</td>
<td>v1.2.0</td>
<td>v1.2.1</td>
<td>완료</td>
</tr>
<tr>
<td>SetRecipe</td>
<td>v1.4.0</td>
<td>v1.4.0</td>
<td>완료</td>
</tr>
<tr>
<td>Operation</td>
<td>v1.2.0</td>
<td>v1.2.0</td>
<td>완료</td>
</tr>
<tr>
<td>DailyEnd</td>
<td>v1.2.0</td>
<td>v1.2.0</td>
<td>완료</td>
</tr>
</tbody></table>
<p>4개 State, 3개 층, 전부 동기화 완료.
이제 이 문서들을 보고 Unity Editor에서 UI를 만들면 된다.</p>
<h2 id="4-다음-단계">4. 다음 단계</h2>
<p><strong>Unity Editor UI 작업 (E1~E8).</strong></p>
<p>코드 완료, 문서 완료. 남은 건 Unity Inspector에서 GameObject를 만들고 SerializeField를 할당하는 수동 작업이다.</p>
<p>작업 순서:</p>
<ol>
<li><strong>E1</strong>: CafeScene Canvas에 4개 State UI 패널 생성 (초기 Inactive)</li>
<li><strong>E2</strong>: StartDayStateUI Inspector - 날씨/트렌드/이벤트 Sprite 할당</li>
<li><strong>E3</strong>: SetRecipeUI Inspector - 트레이, 탭, 팝업, 영업시작 버튼</li>
<li><strong>E4</strong>: OperationUI Inspector - customerSlots, coffeeSlots, 미니게임 팝업 3개</li>
<li><strong>E5</strong>: CustomerSlot 프리팹 생성 (220x320px, CanvasGroup 필수, IPointerClickHandler)</li>
<li><strong>E6</strong>: 미니게임 팝업 3개 (Takeout/LatteArt/DripCoffee)</li>
<li><strong>E7</strong>: DailyEndUI Inspector - 등급/순이익/평판/이벤트/상세모달</li>
<li><strong>E8</strong>: CafeFlowController Inspector 확인</li>
</ol>
<p>E5가 #3에서와 달라졌다. Button 컴포넌트 대신 CanvasGroup + IPointerClickHandler를 쓴다.
오늘 Operation_UI_Guide에서 CustomerSlot을 간소화하면서 바뀐 부분이다.</p>
<hr />
<h2 id="5-교훈">5. 교훈</h2>
<p>색상 통일은 작아 보이지만 파급이 크다.</p>
<p>UI Guide 4개 문서에서 60개 이상의 색상 인스턴스를 바꿨다.
그 중 상당수가 &quot;어디서 복사해온 건지 모를&quot; 범용 웹 색상이었다.
머티리얼 디자인 핑크(<code>#E91E63</code>)가 카페 게임 트렌드 표시에 쓰이고 있었다. 어울릴 리가 없다.</p>
<p>색상 팔레트를 정해놓고 그 안에서만 쓰는 규칙은, 코드에서 상수를 쓰는 것과 같다.
매직 넘버가 위험한 이유와 매직 컬러가 위험한 이유는 똑같다.
나중에 &quot;이 색 왜 쓴 거지?&quot; 할 때, 팔레트에 이름이 있으면 의도를 알 수 있다.
<code>#F39C12</code>는 의도를 모르겠지만, <code>Cafe Gold #BE9757</code>은 의도가 명확하다.</p>
<p>그리고 문서 감사(audit)는 중요하다.
색상 작업하다가 Operation_UI_Guide에서 컴포넌트 상세 누락 9건을 발견했다.
원래 목적(색상 통일)과 다른 문제였지만, 발견한 김에 바로 고쳤다.
이걸 Unity Editor 작업할 때 발견했으면 &quot;문서에 안 적혀있네? 어떻게 하지?&quot; 하면서 삽질했을 것이다.</p>
<p>결론: <strong>팔레트는 법이다. 법 밖의 색은 존재해선 안 된다.</strong></p>