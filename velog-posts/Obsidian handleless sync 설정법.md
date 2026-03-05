<h1 id="2026년-2월-말-출시된-공식-headless-sync-cli-기반">2026년 2월 말 출시된 공식 <strong>Headless Sync</strong> (CLI 기반)</h1>
<p>이번에 Obsidian에서 실시간 동기화를 앱 없이 CLI 환경에서 할 수 있는 기능을 추가했다.
우리 옵쪽이의 장족의 발전이다.</p>
<p>노션을 쓰는 이유가 뭔가?
느리고 무겁지만 실시간 Sync가 너무 압도적이기 때문이다. 
옵시디언은 가볍고 범용적인 반면에 너무 수동으로 제어해줘야 하는 것이 많다.
따라서 좀 불편한 점이 많은데 이번에는 사용자들의 가려운 부분을 아주 잘 긁어준 것 같다는 느낌이 들었고, 옵시디언 개발진들이 계속해서 무언가 일을 하고 있구나 라는 생각이 들었다.</p>
<p>진작에 나왔어야 했는데ㅋㅋㅋ그동안 노트앱을 유목하던 기간이 너무 길었어서 아쉬운 마음은 어쩔 수 없다.</p>
<p>이 방식은 터미널(Command Line Interface) 환경에서 작동하며, 서버나 자동화 스크립트에서 활용하기 좋음.</p>
<h1 id="windows">Windows</h1>
<hr />
<h1 id="1-필수-사항">1. 필수 사항</h1>
<ul>
<li>활성화된 <strong>Obsidian Sync 구독</strong></li>
<li>Node.js가 설치된 환경.</li>
</ul>
<h1 id="2-설치">2. 설치:</h1>
<ul>
<li>터미널에서 다음 명령어를 입력하여 설치</li>
</ul>
<pre><code class="language-bash">npm install -g obsidian-headless</code></pre>
<h1 id="3-로그인">3. 로그인:</h1>
<ul>
<li>옵시디언 계정으로 로그인</li>
</ul>
<pre><code class="language-bash">ob login</code></pre>
<h1 id="4-원격-볼트-연결-setup">4. 원격 볼트 연결 (Setup):</h1>
<ul>
<li>로컬 볼트 폴더로 이동하여 원격 볼트와 연결</li>
</ul>
<pre><code class="language-bash">cd /path/to/your/local/vault
ob sync-setup --vault &quot;원격 보관소 이름&quot;</code></pre>
<h1 id="5-동기화-실행">5. 동기화 실행</h1>
<ul>
<li><p><strong>1회성 동기화</strong> command:</p>
<pre><code class="language-bash">ob sync</code></pre>
</li>
<li><p><strong>지속적 동기화 (감시 모드)</strong> command : ob sync --continuous</p>
<pre><code class="language-bash">ob sync --continuous</code></pre>
</li>
</ul>
<hr />
<h1 id="mac">Mac</h1>
<h1 id="1-필수-사항-1">1. 필수 사항</h1>
<ul>
<li>Windows와 동일함</li>
</ul>
<h1 id="2-설치-1">2. 설치:</h1>
<ul>
<li>터미널에서 다음 명령어를 입력하여 설치:</li>
</ul>
<pre><code class="language-bash">npm install -g obsidian-headless</code></pre>
<h1 id="2-1-만약-글로벌-설치에서-권한-문제가-생길-경우-sudo-사용">2-1 만약 글로벌 설치에서 권한 문제가 생길 경우 sudo 사용</h1>
<pre><code class="language-bash">sudo npm install -g obsidian-headless</code></pre>
<h1 id="3-로그인-1">3. 로그인:</h1>
<ul>
<li>Window와 동일함</li>
</ul>
<h1 id="4-원격-볼트-연결-setup-1">4. 원격 볼트 연결 (Setup):</h1>
<ul>
<li>Window와 동일함</li>
</ul>
<h1 id="5-동기화-실행-1">5. 동기화 실행</h1>
<ul>
<li>Window와 동일함</li>
</ul>
<h1 id="5-1-뭔가-터미널을-꺼도-백그라운드에서-돌아가게-할-순-없을까">5-1. 뭔가 터미널을 꺼도 백그라운드에서 돌아가게 할 순 없을까?</h1>
<ul>
<li>해서 nohup을 사용하는 방법도 생각해봤다.</li>
</ul>
<pre><code class="language-bash">nohup ob sync --continuous &amp;</code></pre>
<p>이렇게 하면 되지 않을까 생각해봤는데, 커맨드 입력하고 터미널을 끄면, &quot;현재 터미널에서 실행되고 있는 프로세스를 종료하시겠습니까?&quot; 라는 알림 멘트가 나온다.
따라서 나는 이렇게 생각했다. 
혹시 옵시디언 볼트 설정에 터미널 세션 단위로 되는 것 아닐까?
(실제로 터미널을 끄면 터미널이 바라보는 볼트 세팅을 다시 해야함)
그렇기에 터미널을 벗어나면 볼트와의 연결이 끊어지는 것으로 추론했다.</p>
<p>그래서 그냥 아래의 커맨드를 입력하고, 사용하는 걸 추천한다.</p>
<pre><code class="language-bash">ob sync --continuous</code></pre>
<p>왜냐하면 그럼 적어도 현재 Sync되고 있는지 아닌지 터미널에 보여지게 된다.
지금 이 글을 옵시디언에 적고 있는 지금도 실시간으로 로그가 올라가는 걸 확인할 수 있었다.</p>
<hr />
<h1 id="번외-openclaw와의-미친-호환">[번외] OpenClaw와의 미친 호환</h1>
<p>번외로 이번에 나온 <strong>Headless Sync</strong> (CLI 기반)와 OpenClaw를 같이 사용하면 굉장한 시너지가 생긴다는 것을 알리고 싶었다.
일단 OpenClaw와 옵시디언은 상성이 매우 좋은데, 그 이유는 옵시디언을 OpenClaw의 대뇌피질과 같이 사용할 수 있다는 것이다.
<strong>Skill</strong>로 묶어도 되고, 인간의 뇌을 OpenClaw에 이식한다고 생각 했을 때, 중장기 기억을 옵시디언이 담당하게 할 수 있다.
또한 개인화에도 매우 도움이 된다.</p>
<p>여기서 OpenClaw에서 옵시디언을 사용하는 데, 약간 불편했던 점은 Sync의 주기가 불규칙하다는 것이었다.
이게 무슨 얘기냐하면 OpenClaw가 옵시디언 mcp 사용하지 않고 파일 시스템 도구를 써서 md 파일을 직접 수정하는 경우 옵시디언 앱이 켜져있지 않거나, 켜져 있다고 하더라도 Sync가 바로바로 되는 것이 아니기 때문에 여기서 생기는 오차가 있다.
또는 내가 vs code나 코드 편집기로 md 파일을 열고 수정하고 있는 경우, 옵시디언 앱을 켜고 있고 같은 md 파일을 보고 있더라도 Sync와 꼬여서 버전의 문제가 생긴다.</p>
<p>예를 들어 A.md 파일을 코드 편집기로 수정하는 나와 옵시디언 mcp를 사용하는 OpenClaw 또는 AI와 같은 파일을 보고 있음에도 충돌이 생기는 것이다.
이걸 OpenClaw가 있는 로컬 환경의 옵시디언 볼트에 <strong>Headless Sync</strong> 를 연결해 놓는다면? OpenClaw가 문서를 수정하는 동시에 바로 동기화 된다.
큰 파일의 경우에도 수동으로 동기화 할 필요가 더 이상 없어지는 것이다.</p>
<p>물론 외부 작업 PC에도 <strong>Headless Sync</strong> 를 연결해놓으면 좋겠지만, 일단 OpenClaw가 있는 로컬 볼트에 연결만 해두어도 당신의 비서가 열심히 만든 문서를 실시간으로 동기화해서 볼 수 있다.</p>