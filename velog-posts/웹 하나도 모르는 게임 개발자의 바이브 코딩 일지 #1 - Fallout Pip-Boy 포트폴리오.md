<h2 id="🎮-prologue--유니티-켜다가-vs-code-켜본-개발자-웹은-또-다른-게임이었다">🎮 Prologue : 유니티 켜다가 VS Code 켜본 개발자 (웹은 또 다른 게임이었다)</h2>
<p>&quot;포트폴리오 사이트 하나 만들어보세요.&quot;</p>
<p>취업 준비하면서 듣는 소리다. 근데 난 유니티랑 언리얼만 만지던 놈이다. HTML? 그거 유니티 TextMeshPro에서 폰트 설정할 때나 보는 거 아닌가? CSS? 씬(Scene)이랑 뭔 차인데?</p>
<p>그래도 방법은 있다. <strong>바이브 코딩(Vibe Coding)</strong>.</p>
<p>AI한테 &quot;이런 느낌으로 만들어줘&quot; 하고 던지면 알아서 코드를 짜주는 마법. 마치 게임 기획자가 개발자한테 &quot;이 몬스터 느낌 살려주세요&quot; 하는 거랑 똑같다. AI가 내 개발자고, 내가 기획자가 되는 거지.</p>
<p>그래서 결정했다.</p>
<p><strong>&quot;폴아웃 Pip-Boy 스타일의 터미널 UI 포트폴리오를 만들어달라고 해보자.&quot;</strong></p>
<p>왜 폴아웃이냐고? 그녀석의 녹색 형광 CRT 느낌, 타이핑하는 느낌, 그 레트로한 분위기가 좋았으니까. 그리고 무엇보다 내 인생 게임이니까.</p>
<hr />
<h2 id="⚔️-scene-1--프로젝트-세팅-및-첫-번째-부팅-react가-뭔데요">⚔️ Scene 1 : 프로젝트 세팅 및 첫 번째 부팅 (React가 뭔데요?)</h2>
<p>AI 파트너에게 요구사항을 전달했다.</p>
<blockquote>
<p>&quot;폴아웃 Pip-Boy 느낌의 터미널 UI 포트폴리오 만들어줘. CRT 효과, 스캔라인, 부팅 시퀀스 다 넣고 싶어.&quot;</p>
</blockquote>
<p>AI가 던져준 첫 번째 빌드:</p>
<pre><code>
React 19 + Vite

Tailwind CSS 4.x

Framer Motion
</code></pre><p>&quot;리액트가 뭔데요?&quot; 하고 물어봤더니, <strong>&quot;컴포넌트 기반 UI 라이브러리입니다&quot;</strong> 라고 한다.</p>
<p>유니티로 치면 프리팹(Prefab) 시스템 같은 건가 보다. Vite는 빌드 시스템? IL2CPP 같은 거려니 하고 넘어갔다.</p>
<p><code>npm run dev</code> 명령어를 치고 로컬호스트 5173번 포트로 접속했다.</p>
<p><strong>...검은 화면에 녹색 글씨가 뜬다.</strong></p>
<pre><code>
VAULT-TEC TERMINAL v1.0

INITIALIZING...
</code></pre><p>오오오오! 분위기 산다! 이게 바로 그 맛인가? 근데 왜 글씨가 이렇게 깨져서 나오지?</p>
<hr />
<h2 id="💀-scene-2--ascii-아트와의-전쟁-폰트-렌더링-이슈">💀 Scene 2 : ASCII 아트와의 전쟁 (폰트 렌더링 이슈)</h2>
<p>부팅 시퀀스에 NOTNULL이라는 ASCII 아트를 넣기로 했다. 내 닉네임이다.</p>
<pre><code>
███╗   ██╗ ██████╗ ████████╗███╗   ██╗██╗   ██╗██╗     ██╗
████╗  ██║██╔═══██╗╚══██╔══╝████╗  ██║██║   ██║██║     ██║
██╔██╗ ██║██║   ██║   ██║   ██╔██╗ ██║██║   ██║██║     ██║
██║╚██╗██║██║   ██║   ██║   ██║╚██╗██║██║   ██║██║     ██║
██║ ╚████║╚██████╔╝   ██║   ██║ ╚████║╚██████╔╝███████╗███████╗
╚═╝  ╚═══╝ ╚═════╝    ╚═╝   ╚═╝  ╚═══╝ ╚═════╝ ╚══════╝╚══════╝
</code></pre><p>근데 이게 브라우저에서 렌더링되니까 줄이 다 밀려버렸다.</p>
<p><strong>&quot;AI야, 줄 맞춰줘.&quot;</strong></p>
<p><strong>&quot;폰트를 monospace로 변경했습니다.&quot;</strong></p>
<p>아하! 유니티에서 TextMeshPro 폰트 에셋 설정하는 거랑 비슷하구나. 고정폭 폰트를 써야 ASCII 아트가 안 깨진다. JetBrains Mono라는 폰트를 CDN에서 긁어왔다.</p>
<p>그래도 여전히 뭔가 부족하다. 폴아웃 느낌이 안 난다.</p>
<hr />
<h2 id="🔥-scene-3--crt-효과-구현-쉐이더-아니면-css">🔥 Scene 3 : CRT 효과 구현 (쉐이더? 아니면 CSS?)</h2>
<p>폴아웃 특유의 그 느낌. 화면이 지직거리고, 스캔라인이 지나가고, 가끔 플리커(깜빡임)가 터지는 그 레트로한 느낌이 필요했다.</p>
<p>유니티였면 포스트프로세싱 볼륨(Post-Processing Volume)에 쉐이더 하나 박았을 텐데... 웹은 어떻게 하지?</p>
<p><strong>&quot;AI야, CRT 효과 줘. 스캔라인, 글로우, 플리커 다.&quot;</strong></p>
<p>AI가 뱉어낸 CSS 코드를 봤다.</p>
<pre><code class="language-css">
.crt::before {

  content: &quot; &quot;;

  display: block;

  position: absolute;

  top: 0;

  left: 0;

  bottom: 0;

  right: 0;

  background: linear-gradient(

    rgba(18, 16, 16, 0) 50%,

    rgba(0, 0, 0, 0.25) 50%

  );

  background-size: 100% 2px;

  z-index: 2;

  pointer-events: none;

}
</code></pre>
<p>와... 이게 되네? 쉐이더 없이 CSS만으로 스캔라인을 구현했다. <code>::before</code> 가상 요소에 그라디언트를 깔아서 홀수/짝수 라인을 번갈아가며 어둡게 만드는 방식이었다.</p>
<p>글로우 효과는 <code>text-shadow</code>와 <code>box-shadow</code>를 중첩해서 만들었다.</p>
<pre><code class="language-css">
.glow {

  text-shadow:

    0 0 5px #00ff00,

    0 0 10px #00ff00,

    0 0 20px #00ff00;

}
</code></pre>
<p>플리커는 CSS 애니메이션 <code>@keyframes</code>로 구현했다. 랜덤하게 <code>opacity</code>를 흔들어서 마치 오래된 CRT 모니터처럼 보이게.</p>
<p><strong>&quot;이거 진짜 폴아웃 같은데?!&quot;</strong></p>
<hr />
<h2 id="🌪️-scene-4--타이핑-애니메이션-코루틴-아니면-useeffect">🌪️ Scene 4 : 타이핑 애니메이션 (코루틴? 아니면 useEffect?)</h2>
<p>부팅 시퀀스에 시스템 메시지가 한 글자씩 타이핑되는 효과를 넣고 싶었다.</p>
<p>유니티였면 <code>IEnumerator</code> 코루틴 돌려서 <code>yield return new WaitForSeconds()</code> 로 구현했을 텐데... React는?</p>
<p><strong>&quot;useEffect와 setTimeout의 조합입니다.&quot;</strong></p>
<pre><code class="language-jsx">
useEffect(() =&gt; {

  if (currentIndex &lt; text.length) {

    const timeout = setTimeout(() =&gt; {

      setDisplayedText(prev =&gt; prev + text[currentIndex]);

      setCurrentIndex(prev =&gt; prev + 1);

    }, 30);

    return () =&gt; clearTimeout(timeout);

  }

}, [currentIndex]);
</code></pre>
<p>음... 뭔가 비슷하다. <code>useEffect</code>가 <code>Update()</code> 루프 같고, <code>setTimeout</code>이 <code>Invoke()</code> 같고, <code>clearTimeout</code>이 코루틴 취소 같고.</p>
<p>그래, 웹도 결국 똑같구나. 상태 관리랑 타이머랑 콜백. 게임 개발이랑 다를 게 없잖아?</p>
<hr />
<h2 id="⚔️-scene-5--인증-로직">⚔️ Scene 5 : 인증 로직</h2>
<p>폴아웃 터미널하면 빠질 수 없는 게 해킹 미니게임이지 않은가. 근데 그걸 구현하기엔 내 웹 지식이 부족하니까, 일단은 비밀번호 입력 방식으로 구현했다.</p>
<p>ACCESS KEY: <code>_ _ _ _ _</code></p>
<p>비밀번호를 틀리면 랜덤한 에러 메시지가 뜬다:</p>
<pre><code>
&gt; ACCESS DENIED - USER PRESENCE QUESTIONABLE

&gt; ERROR 404: TALENT NOT FOUND

&gt; FATAL ERROR: TRY DRINKING COFFEE

&gt; WARNING: SUSPICIOUS ACTIVITY DETECTED
</code></pre><p>이거 AI가 알아서 넣어줬는데, 꽤 웃기다. 나도 모르게 웃음이 나왔다.</p>
<p>맞으면 화면이 지직거리면서 글리치 효과와 함께 메인 포트폴리오 화면으로 전환된다. <code>AnimatePresence</code> 라는 Framer Motion 기능을 썼는데, 유니티의 <code>SceneManager.LoadScene()</code> 이랑 애니메이션 시스템을 합쳐놓은 느낌이었다.</p>
<hr />
<h2 id="🏰-scene-6--pip-boy-탭-네비게이션-ui-빌드-완료">🏰 Scene 6 : Pip-Boy 탭 네비게이션 (UI 빌드 완료)</h2>
<p>메인 화면은 Pip-Boy 스타일의 탭 네비게이션으로 구성했다.</p>
<ul>
<li><p><strong>STAT</strong> 탭: 내 소개, 스킬, 경력 (RPG 캐릭터 스탯처럼)</p>
</li>
<li><p><strong>DATA</strong> 탭: 프로젝트 목록 (아이템 인벤토리처럼)</p>
</li>
</ul>
<p>프로젝트 카드를 누르면 모달 팝업이 뜨면서 상세 정보를 보여준다. TUI 스타일의 카드 디자인으로, 마치 터미널에서 파일 목록을 보는 것 같은 느낌.</p>
<p>그리고 살짝 숨겨진 <strong>&quot;???&quot;</strong> 버튼. 이건 관리자(Admin) 모드 진입용이다. 나중에 프로젝트를 동적으로 추가/수정/삭제할 수 있게 만들 예정이다.</p>
<hr />
<h2 id="🏆-epilogue--phase-1--2-클리어-아직-갈-길이-멀다">🏆 Epilogue : Phase 1 &amp; 2 클리어 (아직 갈 길이 멀다)</h2>
<p>오늘 작업으로 완성된 것들:</p>
<p>✅ CRT 효과 (스캔라인, 플리커, 글로우)</p>
<p>✅ 터미널 부팅 시퀀스 + NOTNULL ASCII 아트</p>
<p>✅ 인증 로직 (비밀번호: alena) + 랜덤 에러 메시지</p>
<p>✅ 화면 전환 글리치 효과</p>
<p>✅ Pip-Boy 탭 네비게이션 (STAT/DATA)</p>
<p>✅ 프로젝트 카드 + 상세 모달 팝업</p>
<p>커밋 메시지: <code>Feat: Phase 1 &amp; 2 완료 - Fallout 스타일 TUI 포트폴리오 구현</code></p>
<p>근데 아직 할 게 많다:</p>
<ul>
<li><p>🔲 Phase 3: Admin Tool (로그인, CRUD, 로컬 스토리지)</p>
</li>
<li><p>🔲 Phase 4: 사운드 이펙트, 반응형, 배포</p>
</li>
</ul>
<p><strong>웹 개발, 생각보다 재밌네?</strong></p>
<p>유니티 켜고 쉐이더 짜고 프리팹 만들고 하는 거랑 크게 다를 게 없다. 컴포넌트, 상태, 이벤트, 애니메이션. 다 똑같다. 그냥 엔진이 다를 뿐.</p>
<p>그리고 무엇보다 <strong>AI랑 같이 코딩하는 재미</strong>가 쏠쏠하다. 내가 기획서 던지면 AI가 코드를 짜고, 나는 그걸 보고 &quot;이거 좀 이상한데?&quot; 하면 AI가 다시 고치고. 마치 시니어 개발자 옆에 주니어가 앉아 있는 기분? 근데 나보다 AI가 더 시니어 같다는 게 함정.</p>
<p>암튼, 오늘은 여기까지.</p>
<p>다음 편에는 <strong>Admin Tool 만들기</strong>랑 <strong>Vercel 배포</strong>로 돌아오겠다.</p>
<p><strong>War. War never changes.</strong></p>
<p><strong>But coding? Coding is fun with AI.</strong> 🚀</p>
<hr />
<p><em>P.S. 아직도 CSS flexbox랑 grid 헷갈린다. 유니티 uGUI의 HorizontalLayoutGroup이랑 GridLayoutGroup이 그리워진다...</em></p>