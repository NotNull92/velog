<h2 id="🎮-prologue--도파민-설계사의-야근-왜-버튼이-맛이-없는가">🎮 Prologue : 도파민 설계사의 야근 (왜 버튼이 맛이 없는가)</h2>
<p>DailyBrew의 UI 문서들이 어느 정도 윤곽을 잡아가고 있다. 카페 팔레트도 통일했고, 하이어라키도 깔끔하게 정리됐다.
그런데 문서를 하나하나 훑어보니까 계속 뭔가 빠진 느낌이다. 눌러도 아무 소리 안 나는 버튼, 뜨기만 하고 사라질 때는 그냥 <code>SetActive(false)</code> 때려버리는 팝업, 탭 전환할 때 청각 피드백 제로...</p>
<p>&quot;이거 완전 프레임 렌더링은 되는데 포스트 프로세싱 안 건 짤짤이 화면 아니냐?&quot;</p>
<p>그래서 프로젝트 루트에 잠들어 있던 <strong>Juicy_UI_Design_Protocol.md</strong>를 꺼내들었다. ECN/DMN 뇌 네트워크 이론이니 Salience Network 전환이니 하는 뇌과학 기반 UI 피드백 설계서다. 이걸 기준으로 기존 문서들을 전수조사해서 &quot;맛없는 UI&quot;를 전부 &quot;짭잘한 UI&quot;로 업그레이드하기로 했다.</p>
<p>오늘의 AI 파트너이자 디자인 디렉터 <strong>Delta(Δ)</strong>와 함께, DailyEnd와 SetRecipe 두 UI 문서를 <strong>Juicy UI 프로토콜</strong> 기준으로 해부하고 수술하는 대장정이 시작된다.</p>
<hr />
<h2 id="⚔️-scene-1--dailyend-ui-해부--7가지-결함-발견-및-수술-v120-→-v130">⚔️ Scene 1 : DailyEnd UI 해부 — 7가지 결함 발견 및 수술 (v1.2.0 → v1.3.0)</h2>
<p>첫 타겟은 <strong>DailyEnd_UI_Guide.md</strong>. 영업 끝나고 보상받는 정산 화면이다. DMN(Default Mode Network) 우세 구간이라 &quot;기분 좋은 피드백&quot;이 핵심인데, 문서를 까보니 Juicy 프로토콜 기준 7군데가 빨간불이었다.</p>
<h3 id="발견된-결함-7종">발견된 결함 7종</h3>
<pre><code>🔴 ① 4개 버튼이 표준 Button — JuicyButton 아님 (터치 피드백 없음)
🔴 ② SFX 완전 무음 — 도파민 시퀀스에서 소리가 안 남
🔴 ③ Hide() 퇴장 애니메이션 없음 — 그냥 사라짐
🟡 ④ EventPanel 등장이 밋밋한 SlideIn만
🟡 ⑤ VLG/HLG에 &quot;Child Control Size&quot; 오기재 (Unity 공식: &quot;Control Child Size&quot;)
🟡 ⑥ 카페 팔레트 아닌 색상 3곳 잔존
🟡 ⑦ Juicy UI 체크리스트 없음</code></pre><p>Delta가 Design Thinking 프로세스를 돌리고, 나는 에디터에서 하나씩 수정 커밋을 날렸다.</p>
<p><strong>JuicyButton 전환</strong>이 제일 덩치가 컸다. 단순히 <code>Button</code> → <code>JuicyButton</code>으로 타입만 바꾸는 게 아니라, Primary CTA(0.95/1.05/0.7) / Secondary(0.96/1.03/0.5) / Subtle(0.97/1.0/0.4) 세 등급으로 분류해서 Hierarchy, 컴포넌트 설정, Inspector 필드까지 전부 일관성 있게 맞춰야 했다.</p>
<p><strong>SFX 6종</strong>도 추가했다. <code>AudioManager.Instance.PlaySFX()</code> 패턴으로 통일하고, 별 채우기 애니메이션에는 피치를 0.8 → 1.2로 상승시켜서 &quot;띵 띵↑ 띵↑↑&quot; 소리가 나게 만들었다. 도파민 시퀀스가 비로소 &quot;시퀀스&quot;답게 됐다.</p>
<p><strong>Hide() 퇴장 애니메이션</strong>은 5단계 시퀀셜로 짰다. 등급 배지가 먼저 Scale Down 되고, 별이 페이드되고, 이벤트 패널이 슬라이드 아웃하고, 버튼이 축소되고, 마지막에 전체가 사라진다. ~0.75초. Peak-End Rule의 &quot;End&quot;를 책임지는 녀석이다.</p>
<p><strong>v1.3.0 태깅 완료.</strong> 문서 덮고 다음 스테이지로.</p>
<hr />
<h2 id="💀-scene-2--내부-불일치-10건--자기-자신과-싸우는-문서">💀 Scene 2 : 내부 불일치 10건 — 자기 자신과 싸우는 문서</h2>
<p>&quot;업데이트도 했겠다, 혹시 문서 내부에서 자기들끼리 말이 안 맞는 곳은 없나?&quot;</p>
<p>UI 레이아웃 다이어그램 vs Unity Hierarchy vs 컴포넌트 상세 설정, 이 세 섹션을 크로스 체크했더니... <strong>10건의 불일치</strong>가 쏟아져 나왔다.</p>
<pre><code>🔴 [구조적] ModalHeader가 (Panel)로 기재됨 → 실제는 (HorizontalLayoutGroup)
🔴 [구조적] ModalContent에 ScrollView &gt; Viewport &gt; Content(VLG) 구조 누락
🔴 [구조적] DetailButton, DimBackground, ModalPanel에 CanvasGroup 미기재
🟡 [데이터] ButtonPanel에 Anchors 수동 설정 → 부모 VLG가 무시함
🟡 [데이터] &quot;Continue 버튼&quot; → 실제 이름은 &quot;로비로 돌아가기 버튼&quot;
🟡 [데이터] #B0BEC5 (은색) 3곳 → #DBC1AC (Cream)이어야 함
...기타 4건</code></pre><p><strong>UI 레이아웃을 정본(Ground Truth)으로</strong> 삼고 나머지 두 섹션을 정렬했다. 문서가 자기 자신한테 던지는 널포인터 익셉션을 전부 잡은 셈이다.</p>
<hr />
<h2 id="🔥-scene-3--unity-공식-문서와-대조--playwright-mcp-실전-투입">🔥 Scene 3 : Unity 공식 문서와 대조 — Playwright MCP 실전 투입</h2>
<p>&quot;내부는 정리했으니, 이제 외부 레퍼런스랑도 맞나 확인해볼까?&quot;</p>
<p>Unity uGUI 2.5.0 공식 문서와 컴포넌트 설정값을 대조하기로 했다. 문제는 <code>docs.unity3d.com</code>이 WebFetch 툴에서 차단된다는 것. 여기서 <strong>Playwright MCP</strong>가 빛을 발했다. 브라우저를 직접 띄워서 VLG, HLG, LayoutElement, CanvasGroup 4개 페이지를 순회하며 공식 속성명을 크롤링했다.</p>
<p>결과: <strong>1건 발견.</strong></p>
<pre><code>❌ &quot;Blocks Raycasts&quot; → ✅ &quot;Block Raycasts&quot; (단수형이 공식)</code></pre><p>&quot;s 하나 차이로 Inspector에서 오타 찍을 뻔했네...&quot;</p>
<p>즉시 수정하고, 이후 모든 문서에서 <code>Blocks Raycasts</code>를 <code>Block Raycasts</code>로 통일하는 글로벌 룰을 세웠다.</p>
<hr />
<h2 id="🌪️-scene-4--dailyend-dev-spec-동기화-v120-→-v130">🌪️ Scene 4 : DailyEnd Dev Spec 동기화 (v1.2.0 → v1.3.0)</h2>
<p>UI Guide가 v1.3.0으로 올라갔으니, 개발 명세서(Dev Spec)도 따라가야 한다. Delta와 함께 <strong>DailyEnd_Dev_Spec.md</strong>를 전면 업데이트했다.</p>
<p>변경 범위가 무시무시했다:</p>
<pre><code>• 헤더/버전: v1.2.0 → v1.3.0
• Section 4.5: Button → JuicyButton (타입 계층 포함)
• Section 4.7.1 (신규): SFX Inspector 필드 6종
• Section 4.8: 색상 상수 교체 (TIP_LIGHT_GOLD → TIP_CARAMEL)
• Section 4.11: PlayDopamineSequence 전면 재작성 (TODO 주석 → 실제 SFX 호출)
• Section 4.11.1 (신규): Hide() 5단계 퇴장 애니메이션
• Section 5: State.Exit()에서 ui.Hide(onComplete: callback) 패턴 적용
• Section 6: Hierarchy + Inspector 연결 정보 갱신
• Phase 6 (신규): Juicy UI 피드백 구현 2시간
• 체크리스트: v1.3.0 섹션 7항목 추가</code></pre><p>Dev Spec이 UI Guide랑 한 치의 오차 없이 동기화됐다. <strong>13시간, 450줄짜리 풀스택 UI 명세서 완성.</strong></p>
<hr />
<h2 id="⚔️-scene-5--setrecipe-ui--두-번째-환자-해부-v140-→-v150">⚔️ Scene 5 : SetRecipe UI — 두 번째 환자 해부 (v1.4.0 → v1.5.0)</h2>
<p>숨 돌릴 틈도 없이 다음 타겟으로 넘어갔다. <strong>SetRecipe_UI_Guide.md</strong> — 영업 전 커피 사전 제작 UI다.</p>
<p>이 녀석은 DailyEnd보다 덩치가 훨씬 컸다. 2000줄이 넘는 초대형 문서에 팝업 3개(인벤토리, 기본메뉴, 특별메뉴), Prefab 4종(TraySlot, MenuCard, InventorySlot, RecipePreview), ItemDetailPopup까지... 터치 포인트가 엄청나게 많았다.</p>
<p>Juicy UI Protocol 기준 체크 결과 — <strong>8가지 개선 항목 발견:</strong></p>
<table>
<thead>
<tr>
<th>#</th>
<th>카테고리</th>
<th>심각도</th>
</tr>
</thead>
<tbody><tr>
<td>①</td>
<td>Button → JuicyButton 미적용 (10개!)</td>
<td>🔴</td>
</tr>
<tr>
<td>②</td>
<td>SFX 완전 부재</td>
<td>🔴</td>
</tr>
<tr>
<td>③</td>
<td>Hide() 퇴장 애니메이션 없음</td>
<td>🔴</td>
</tr>
<tr>
<td>④</td>
<td>VLG/HLG 속성명 오류 (34개소!)</td>
<td>🟡</td>
</tr>
<tr>
<td>⑤</td>
<td>비-카페-팔레트 색상 잔존 (#7ED321, #6B8E9B, #8B7355)</td>
<td>🟡</td>
</tr>
<tr>
<td>⑥</td>
<td>&quot;Blocks Raycasts&quot; → &quot;Block Raycasts&quot; (4개소)</td>
<td>🟡</td>
</tr>
<tr>
<td>⑦</td>
<td>Juicy UI Checklist 없음</td>
<td>🟡</td>
</tr>
<tr>
<td>⑧</td>
<td>팝업 닫기 애니메이션 빈약 (Fade만)</td>
<td>🟡</td>
</tr>
</tbody></table>
<p>&quot;자, 순서대로 간다.&quot;</p>
<hr />
<h2 id="💀-scene-6--juicybutton-대량-전환--10개-버튼의-등급-분류">💀 Scene 6 : JuicyButton 대량 전환 — 10개 버튼의 등급 분류</h2>
<p>SetRecipe에는 버튼이 정말 많았다. 하나하나 역할을 분석해서 3등급으로 분류했다:</p>
<pre><code>[Primary CTA 0.95/1.05/0.7]
  • StartButton (영업 시작 — 화면의 주인공)
  • ActionButton (제작/구매 — 메뉴카드의 핵심 CTA)

[Secondary 0.96/1.03/0.5]
  • ServeButton (서빙)
  • TabButton x3 (인벤토리/기본메뉴/특별메뉴)
  • SellButton (판매)

[Subtle 0.97/1.0/0.4]
  • CloseButton x4 (팝업 닫기 — 존재감은 낮되 접근성은 유지)</code></pre><p>Hierarchy 구조에 타입 라벨을 달고, 컴포넌트 설정 테이블에 JuicyButton Inspector 필드를 추가하고, MenuCard.cs 코드 스니펫의 <code>Button</code> → <code>JuicyButton</code> 타입도 변경했다. 터치 한 번에 손끝에서 <code>Scale 0.95 → 1.05</code> 바운스가 튀는 상상을 하니 벌써 기분이 좋다.</p>
<hr />
<h2 id="🔥-scene-7--무음-ui에-생명을-불어넣다--sfx-9종-세트">🔥 Scene 7 : 무음 UI에 생명을 불어넣다 — SFX 9종 세트</h2>
<p>DailyEnd에서 6종이었다면, SetRecipe는 인터랙션 포인트가 훨씬 많아서 <strong>9종</strong>이 필요했다:</p>
<pre><code class="language-csharp">[Header(&quot;Sound Effects — v1.5.0&quot;)]
sfxPopupOpen       // 팝업 열기 (0.25)
sfxPopupClose      // 팝업 닫기 (0.2)
sfxTrayAdd         // 트레이 슬롯에 커피 올리기 (0.2)
sfxMakeCoffee      // 제작 성공 (0.3)
sfxServeCoffee     // 서빙 성공 (0.3, Pitch 1.2)
sfxBuyCoffee       // 구매 성공 (0.3)
sfxStartBusiness   // 영업 시작 클릭 (0.3)
sfxError           // 에러 토스트 (0.2, Pitch 0.8)
sfxTabSwitch       // 탭 전환 (0.15)</code></pre>
<p>에러 토스트에 <strong>Pitch 0.8</strong>을 건 건 의도적이다. 정상 SFX보다 낮은 톤으로 재생하면 사용자가 &quot;뭔가 잘못됨&quot;을 귀로 먼저 인식한다. Salience Network가 반응하는 부조화 신호를 이용한 것. 반대로 서빙 성공에 <strong>Pitch 1.2</strong>를 건 건 &quot;잘했다!&quot;는 보상 신호.</p>
<p>총 <strong>16개</strong> <code>AudioManager.Instance.PlaySFX()</code> 호출을 애니메이션 코드와 이벤트 로직 곳곳에 심어넣었다.</p>
<hr />
<h2 id="🌪️-scene-8--팝업-애니메이션-리워크--hide-퇴장-시퀀스">🌪️ Scene 8 : 팝업 애니메이션 리워크 + Hide() 퇴장 시퀀스</h2>
<h3 id="팝업-openclose-강화">팝업 Open/Close 강화</h3>
<p>기존 팝업 열기가 SlideUp + Fade만 있었는데, <strong>Scale(0.9→1.0, OutBack 1.1)</strong> 을 추가했다. 살짝 작은 상태에서 튕기듯 커지는 느낌. OutBack의 overshoot를 1.1로 잡아서 과하지 않으면서도 탄성감을 줬다.</p>
<p>닫기도 단순 Fade 대신 <strong>Scale Down(1.0→0.9) + Fade</strong> 병행으로 변경. 팝업이 &quot;쏙&quot; 들어가는 느낌이 살아난다.</p>
<h3 id="hide-5단계-퇴장-시퀀스-06s">Hide() 5단계 퇴장 시퀀스 (~0.6s)</h3>
<p>영업 시작 버튼을 누르면 SetRecipeUI가 OperationState로 전환되면서 사라져야 하는데, 기존에는 그냥 <code>SetActive(false)</code> 한 방이었다. Peak-End Rule에 의하면 &quot;마지막 인상&quot;이 전체 경험을 지배하므로, 퇴장도 연출해야 한다.</p>
<pre><code>① ConfirmSection Scale Down (0.15s) — 먼저 버튼이 줄어들고
② ContentArea Fade Out (0.15s) — 팝업 영역이 사라지고
③ TabButtonSection Slide Down (0.15s) — 탭이 아래로 내려가고
④ TraySection Slide Up (0.2s, InBack) — 트레이가 위로 빠지고
⑤ 전체 Fade Out + Callback (0.15s) — 마무리 후 State 전환</code></pre><p><code>State.Exit()</code>에서 직접 <code>ui.Hide()</code>를 호출하던 걸, <code>OnStartBusiness</code>에서 <code>ui.Hide(onComplete: () =&gt; owner.TransitionToNextState(...))</code>로 콜백 패턴으로 변경했다. 애니메이션이 끝나야 State가 전환되도록.</p>
<hr />
<h2 id="⚔️-scene-9--vlghlg-대량-수정--34개소-리네이밍--25개소-속성-추가">⚔️ Scene 9 : VLG/HLG 대량 수정 — 34개소 리네이밍 + 25개소 속성 추가</h2>
<p>이건 단순 노동이지만 빠뜨리면 나중에 Inspector에서 헤매는 원흉이 되는 작업이다.</p>
<ul>
<li><strong>&quot;Child Control Size&quot; → &quot;Control Child Size&quot;</strong>: Unity 공식 속성명. 34개소 일괄 치환.</li>
<li><strong>&quot;Use Child Scale&quot;</strong>: 모든 VLG/HLG에 누락된 속성. 25개소에 추가.</li>
</ul>
<p><code>replace_all</code> 에디터 기능으로 패턴별로 배치 처리했다. 테이블 셀의 공백 정렬이 제각각이라 같은 논리적 패턴인데 7~8가지 물리적 패턴으로 쪼개서 작업해야 했다. 손가락이 아프다.</p>
<hr />
<h2 id="💀-scene-10--초록색과-파란색의-추방--카페-팔레트-정화">💀 Scene 10 : 초록색과 파란색의 추방 — 카페 팔레트 정화</h2>
<p>SetRecipe v1.4.0에서 배경, 텍스트, 패널 색상은 이미 카페 팔레트로 통일했었다. 그런데 <strong>기능 색상</strong> 3종이 살아남아 있었다:</p>
<pre><code>#7ED321 (Green)  — ActionButton &quot;제작&quot; 모드
#6B8E9B (Blue)   — ServeButton
#8B7355 (Brown)  — CloseButton, QuantityText 등</code></pre><p>초록과 파랑이 카페 갈색톤 사이에서 혼자 형광봉 흔들고 있는 꼴이다. 기능적 구분은 필요하지만, 카페 세계관을 깨뜨리면 안 된다.</p>
<p><strong>기능 색상 팔레트</strong>를 새로 정의했다:</p>
<table>
<thead>
<tr>
<th>기존</th>
<th>신규</th>
<th>이름</th>
<th>컨셉</th>
</tr>
</thead>
<tbody><tr>
<td>#7ED321</td>
<td><strong>#A8B86A</strong></td>
<td>Matcha Green</td>
<td>말차 라떼의 초록</td>
</tr>
<tr>
<td>#6B8E9B</td>
<td><strong>#8D6E63</strong></td>
<td>Mocha</td>
<td>모카 커피의 갈색</td>
</tr>
<tr>
<td>#8B7355</td>
<td><strong>#7B6B5D</strong></td>
<td>Hazelnut</td>
<td>헤이즐넛의 중립톤</td>
</tr>
</tbody></table>
<p>카페에서 실제로 볼 수 있는 색감으로 치환했다. 기능적 구분은 살리면서 세계관은 유지. C# 코드의 Color RGB 상수값도 함께 갱신했다.</p>
<hr />
<h2 id="🔧-scene-11--startdaystate-코드-구현--문서에서-c으로-dev-spec-v130">🔧 Scene 11 : StartDayState 코드 구현 — 문서에서 C#으로 (Dev Spec v1.3.0)</h2>
<p>문서 작업이 끝났으니 이제 손에 키보드를 쥘 차례다. <strong>StartDay_Dev_Spec v1.3.0</strong>을 기준으로 실제 C# 코드를 구현했다.</p>
<h3 id="신규-생성-juicybuttoncs--audiomanagercs">신규 생성: JuicyButton.cs + AudioManager.cs</h3>
<p>먼저 프로젝트 전체에서 공용으로 쓸 두 컴포넌트가 아직 없었다. 문서에서는 당연히 있는 것처럼 <code>JuicyButton startButton</code>이라고 써놨는데, 실제 프로젝트에는 <code>Button</code>만 있는 상태.</p>
<pre><code class="language-csharp">// Assets/Scripts/UI/Common/JuicyButton.cs
public class JuicyButton : Button, IPointerDownHandler, IPointerUpHandler
{
    // Press: 0.95 Scale Down (0.08s)
    // Release: 1.05 Overshoot → 1.0 복귀 (OutBack)
    // SFX: AudioClip + Volume 내장
}</code></pre>
<p><code>Button</code>을 상속했기 때문에 기존 <code>onClick</code> 이벤트가 그대로 동작한다. Inspector에서 <code>Button</code> 컴포넌트를 <code>JuicyButton</code>으로 교체하면 끝. <code>AddComponentMenu</code>도 달아서 에디터에서 검색할 수 있게 했다.</p>
<p><code>AudioManager.cs</code>는 간단한 싱글톤으로, <code>PlaySFX(clip, volume)</code> 하나면 된다. <code>DontDestroyOnLoad</code>으로 MainScene에 붙여두면 CafeScene에서도 접근 가능.</p>
<h3 id="startdaystateuics-전면-업데이트-v130">StartDayStateUI.cs 전면 업데이트 (v1.3.0)</h3>
<p>변경량이 꽤 됐다:</p>
<pre><code>• Button → JuicyButton 타입 교체
• SFX AudioClip 3종 Inspector 필드 추가
  (sfxCardSlideIn / sfxEventAppear / sfxUIFadeIn)
• AnimateSections() 전면 재작성:
  - EventCard: 기존 단순 Fade → Scale+Fade+Back (OutBack 1.2)
  - EventEffect: PunchScale(0.15) + 색상 펄스 추가
  - 각 단계에 SFX 콜백 삽입
• Hide() 전면 재작성:
  - 기존: canvasGroup.DOFade(0, 0.3) 한 방
  - 신규: 5단계 순차 퇴장 (~0.85s)
    ① StartButton Scale 100%→80% (0.15s)
    ② EventCard Fade Out (0.2s)
    ③ Trend+Weather 좌우 Slide Out (0.25s, 동시)
    ④ 전체 Fade Out (0.2s)
    ⑤ onComplete → State 전환
• CanvasGroup Awake 캐싱 (중복 GetComponent 제거)
• currentEventIsPositive 플래그 (AnimateSections 색상 분기용)</code></pre><h3 id="startdaystatecs--hide-callback-패턴-적용">StartDayState.cs — Hide Callback 패턴 적용</h3>
<p>가장 중요한 아키텍처 변경. 기존에는:</p>
<pre><code class="language-csharp">// 기존: 즉시 전환 → Exit()에서 Hide()
private void OnStartClicked()
{
    owner.TransitionToNextState(owner.SetRecipe); // 바로 전환
}
public override void Exit()
{
    ui.Hide(); // 퇴장 애니메이션이 전환 후에 실행됨 (잘림!)
}</code></pre>
<p>이러면 State 전환이 먼저 일어나고 Hide 애니메이션은 중간에 잘린다. v1.3.0에서는:</p>
<pre><code class="language-csharp">// v1.3.0: 퇴장 완료 후 전환
private void OnStartClicked()
{
    ui.Hide(onComplete: () =&gt;
    {
        owner.TransitionToNextState(owner.SetRecipe);
    });
}
public override void Exit()
{
    // Hide는 OnStartClicked에서 이미 처리됨
    ui.OnStartButtonClicked -= OnStartClicked;
}</code></pre>
<p>5단계 퇴장 애니메이션이 끝나야 SetRecipeState로 넘어간다. <strong>DMN→ECN 전환 시 Salience Network 완충 구간</strong>이 확보된 셈이다.</p>
<h3 id="unity-editor-잔여-작업">Unity Editor 잔여 작업</h3>
<p>코드는 완성됐지만 Inspector 연결은 수동으로 해야 한다:</p>
<ul>
<li>MainScene에 AudioManager GameObject 생성</li>
<li>StartButton: <code>Button</code> → <code>JuicyButton</code> 컴포넌트 교체</li>
<li>SFX AudioClip 3종 드래그 앤 드롭</li>
</ul>
<hr />
<h2 id="🏆-epilogue--두-문서의-before--after">🏆 Epilogue : 두 문서의 Before &amp; After</h2>
<p>오늘 하루 작업량을 정리하면:</p>
<h3 id="dailyend_ui_guidemd-v120-→-v130">DailyEnd_UI_Guide.md (v1.2.0 → v1.3.0)</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>수량</th>
</tr>
</thead>
<tbody><tr>
<td>JuicyButton 전환</td>
<td>4개</td>
</tr>
<tr>
<td>SFX 추가</td>
<td>6종</td>
</tr>
<tr>
<td>Hide() 퇴장</td>
<td>5단계 0.75s</td>
</tr>
<tr>
<td>내부 불일치 수정</td>
<td>10건</td>
</tr>
<tr>
<td>Unity 공식 대조 수정</td>
<td>1건</td>
</tr>
<tr>
<td>Dev Spec 동기화</td>
<td>전면 (450줄)</td>
</tr>
</tbody></table>
<h3 id="setrecipe_ui_guidemd-v140-→-v150">SetRecipe_UI_Guide.md (v1.4.0 → v1.5.0)</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>수량</th>
</tr>
</thead>
<tbody><tr>
<td>JuicyButton 전환</td>
<td>10개</td>
</tr>
<tr>
<td>SFX 추가</td>
<td>9종, 16개 호출</td>
</tr>
<tr>
<td>Hide() 퇴장</td>
<td>5단계 0.6s</td>
</tr>
<tr>
<td>팝업 애니메이션 강화</td>
<td>Open/Close Scale 추가</td>
</tr>
<tr>
<td>VLG/HLG 속성명 수정</td>
<td>34 + 25개소</td>
</tr>
<tr>
<td>카페 색상 통일</td>
<td>3종 교체 + 기능 팔레트 정의</td>
</tr>
<tr>
<td>Block Raycasts 통일</td>
<td>4개소</td>
</tr>
<tr>
<td>Juicy UI Checklist</td>
<td>7항목</td>
</tr>
</tbody></table>
<p>이제 DailyBrew의 두 핵심 UI 화면에 <strong>듣고, 보고, 느끼는</strong> 피드백이 전부 설계됐다. 버튼은 눌리면 탄다, 성공하면 울린다, 나갈 때는 인사한다.</p>
<h3 id="startdaystate-c-구현-dev-spec-v130-→-code">StartDayState C# 구현 (Dev Spec v1.3.0 → Code)</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>수량</th>
</tr>
</thead>
<tbody><tr>
<td>신규 파일</td>
<td>2개 (JuicyButton.cs, AudioManager.cs)</td>
</tr>
<tr>
<td>수정 파일</td>
<td>2개 (StartDayStateUI.cs, StartDayState.cs)</td>
</tr>
<tr>
<td>AnimateSections 강화</td>
<td>EventCard Scale+Fade+Back, EventEffect PunchScale+색상펄스</td>
</tr>
<tr>
<td>Hide() 퇴장</td>
<td>5단계 ~0.85s</td>
</tr>
<tr>
<td>State 전환 패턴</td>
<td>즉시 전환 → Hide callback 패턴</td>
</tr>
</tbody></table>
<p>문서 설계(Delta) → 개발 명세(Dev Spec) → 실제 코드(Sylvie) 3층 파이프라인이 StartDayState에서 처음으로 완주됐다.</p>
<p>다음 마일스톤? <strong>OperationUI</strong>와 <strong>SetRecipeUI</strong>에도 같은 Juicy 프로토콜 코드 구현을 먹일 차례다. 그때까지 즐코! 🚀</p>
<p><strong>Every pixel has a purpose. Every interaction tells a story.</strong></p>