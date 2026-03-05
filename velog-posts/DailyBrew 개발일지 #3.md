<h2 id="1-사설">1. 사설</h2>
<p>지난번에 문서 동기화라는 빚을 갚았다면, 오늘은 코드 내부의 빚을 갚는 날이었다.
겉으로 보면 컴파일 잘 되고, 에러 하나 없는 깔끔한 상태였다.
근데 안을 열어보니 생각보다 많이 어지러웠다.</p>
<p>4-State 스크립트를 처음부터 끝까지 다시 읽었다.
StartDay → SetRecipe → Operation → DailyEnd.
한 줄 한 줄 따라가면서 "이 데이터가 여기서 만들어져서 저기로 가는 게 맞나?" 를 계속 물었다.</p>
<p>결론부터 말하면, <strong>12개 이슈를 찾아서 12개 전부 고쳤다.</strong></p>
<p>세션이 한 번 날아가서 심장이 철렁했는데, 컨텍스트 요약이 주입돼서 이어서 작업할 수 있었다.
Claude Code가 자기 대화 내용을 JSONL로 저장하고 있었다는 걸 이때 알았다.
세션이 날아가도 복구된다. 좋은 세상이다.</p>

<h2 id="2-작업-내용">2. 작업 내용</h2>

<h3 id="2-1-세션1-4-state-플로우-점검">2-1. 세션1: 4-State 플로우 점검 (7이슈)</h3>
<p>4개 State 스크립트를 전부 읽고 데이터 흐름을 추적했다.
"이 변수가 어디서 생기고, 어디서 쓰이고, 어디서 사라지는가"를 기준으로 점검했다.</p>

<p><strong>Issue #1 [CRITICAL] - SalesResult 덮어쓰기</strong></p>
<p>StartDayState에서 SalesResult를 미리 생성하고, OperationState.Enter()에서 다시 CreateEmpty()로 덮어쓰고 있었다.
중간에 SetRecipeStatf가 뭘 써넣어도 OperationState 진입하는 순간 증발하는 구조.
해결: StartDayState에서 SalesResult 생성을 제거했다. OperationState가 유일한 생성자.</p>

<p><strong>Issue #3 [MAJOR] - 고객 스폰 시스템 미구현</strong></p>
<p>OperationState에 60초 타이머만 있고, 정작 손님이 안 왔다.
Execute()에서 타이머만 깎이고 아무 일도 안 일어나는 상태였다.</p>
<p>구현한 것:</p>
<ul>
<li>OperationState: TrySpawnNewCustomer() (3~6초 간격), UpdateCustomerTimers() (매 프레임)</li>
<li>OperationUI: SpawnCustomer(), HasAvailableSlot(), UpdateCustomerTimers(), ResetAllSlots()</li>
<li>미니게임 타입 결정: Takeout 70% / LatteArt 15% / DripCoffee 15%</li>
<li>타임아웃 시 ForceLeave + 평판 -5</li>
</ul>

<p><strong>Issue #4, #5 [MEDIUM] - 이벤트 구독/해제 + UI Hide</strong></p>
<ul>
<li>StartDayState: Enter()에서 구독, Exit()에서 해제하도록 패턴 수정</li>
<li>SetRecipeState: Exit()에 ui.Hide() 추가 (안 숨겨지는 버그)</li>
</ul>

<p><strong>Issue #6 [MEDIUM] - 매출 이중 추적</strong></p>
<p>HandleMiniGameCompleted에서 currentGold에도 더하고 SalesResult.revenue에도 더하고 있었다.
FinishOperation에서 SalesResult.revenue = currentGold로 덮어쓰니까 결과적으로 동작은 했지만, 중간에 SalesResult.revenue를 읽는 코드가 있었으면 2배로 뻥튀기됐을 것.
해결: currentGold만 단일 소스로 사용, SalesResult.revenue 중간 누적 제거.</p>

<p><strong>Issue #7 [MEDIUM] - Grade.F / 파산 코드 완전 제거</strong></p>
<p>지난 세션에서 설계 문서에서는 F등급과 파산을 제거했는데, 코드에는 아직 남아있었다.
7개 파일에서 Grade.F enum 값, BANKRUPTCY_THRESHOLD 상수, CheckBankruptcy() 메서드, ShowBankruptScreen() UI 전체를 제거했다.</p>

<p><strong>추가: CurrentDay/MaxDays 필드 제거</strong></p>
<p>싱글 데이 모델로 전환했으니 CurrentDay와 MaxDays가 필요 없다.
CafeFlowController, StartDayState, DailyEndState, DailyEndData, CafeTestBox, PlayerData 총 6개 파일에서 제거.
SaveData.cs에는 v1 마이그레이션 호환용으로 남겨뒀다.</p>

<h3 id="2-2-세션2-state-재점검-5이슈">2-2. 세션2: State 재점검 (5이슈)</h3>
<p>세션1 수정 후 4개 State를 다시 처음부터 끝까지 읽었다.
"고쳤으니 끝이겠지?" 아니었다.</p>

<p><strong>Issue A [CRITICAL] - MiniGameType 재결정 버그</strong></p>
<p>손님이 스폰될 때 MiniGameType이 결정되어 CustomerSlot에 저장되는데,
플레이어가 손님을 사크하면 DetermineMiniGameType()을 다시 호출해서 랜덤으로 새 타입을 뽑고 있었다.
손님 머리 위에 "LATTE ART"라고 떠있는데, 클릭하면 테이크아웃 미니게임이 시작되는 상황.
해결: OnCustomerClicked 이벤트에 MiniGameType 파라미터를 추가해서 슬롯에 저장된 값을 그대로 전달.</p>

<p><strong>Issue B [CRITICAL] - 타임아웃 손님 미집계</strong></p>
<p>HandleMiniGameCompleted에서만 totalCustomers++를 하고, HandleCustomerTimeout에서는 안 하고 있었다.
DailyEndState에서 "손님 이탈 평판" 계산할 때 (totalCustomers - successfulSales) * 2를 하는데,
타임아웃 손님이 totalCustomers에 안 들어가니까 이탈 패널티가 실제보다 적게 나왔다.
해결: HandleCustomerTimeout에 totalCustomers++ 추가.</p>

<p><strong>Issue C [MEDIUM] - Dead Code 정리</strong></p>
<p>customerQueue라는 List&lt;CustomerData&gt;가 선언만 되고 한 번도 사용되지 않았다.
CustomerData 클래스도 마찬가지. using System.Collections.Generic까지 불필요해서 전부 삭제.</p>

<p><strong>Issue D [MEDIUM] - 중복 디버그 헤더</strong></p>
<p>DailyEndState의 PrintDailyReport에 "=== 일일 보고서 ===" 와 "=== Daily Report ===" 가 연속으로 출력되고 있었다.
CurrentDay 필드 제거할 때 편집 과정에서 생긴 버그. 하나 제거.</p>

<p><strong>Issue E [LOW] - ui.Hide() 이중 호출</strong></p>
<p>SetRecipeState에서 OnStartBusinessClicked()에서 ui.Hide() 호출 후, TransitionToNextState()가 Exit()를 호출하면서 또 ui.Hide(). 크래시는 안 나지만 불필요한 호출. OnStartBusinessClicked의 Hide 제거.</p>

<h3 id="2-3-ui-스크립트-전체-점검-2이슈">2-3. UI 스크립트 전체 점검 (2이슈)</h3>
<p>State 스크립트만 봐서는 부족하다. UI 스크립트도 봐야 한다.
StartDayStateUI, SetRecipeUI, OperationUI, DailyEndUI 4개를 전부 읽었다.</p>

<p><strong>Issue F [MEDIUM] - 상세 모달 미연결</strong></p>
<p>DailyEndUI에 ShowDetailModal() 메서드가 있고, 상세 모달 UI도 다 만들어져 있는데,
DailyEndState의 OnDetailClicked()가 로그만 찍고 아무것도 안 하고 있었다.
버튼을 눌러도 모달이 안 열리는 상태.
해결: OnDetailClicked()에 ui?.ShowDetailModal() 한 줄 추가.</p>

<p><strong>Issue G [LOW] - event 키워드 누락</strong></p>
<p>DailyEndUI와 StartDayStateUI에서 public Action으로 선언된 이벤트를 public event Action으로 변경.
event 키워드가 없으면 외부에서 = 로 덮어쓸 수 있어서 위험하다.</p>

<h2 id="3-수정-파일-요약">3. 수정 파일 요약</h2>

<table>
<thead>
<tr>
<th>파일</th>
<th>세션</th>
<th>주요 수정</th>
</tr>
</thead>
<tbody><tr>
<td>StartDayState.cs</td>
<td>1</td>
<td>SalesResult 생성 제거, 이벤트 패턴, CurrentDay 제거</td>
</tr>
<tr>
<td>SetRecipeState.cs</td>
<td>1+2</td>
<td>Exit() Hide 추가, 이중 Hide 제거</td>
</tr>
<tr>
<td>OperationState.cs</td>
<td>1+2</td>
<td>고객 스폰 구현, MiniGameType 수정, totalCustomers 수정, dead code 삭제</td>
</tr>
<tr>
<td>DailyEndState.cs</td>
<td>1+2</td>
<td>Grade.F 제거, 중복 헤더, 상세 모달 연결</td>
</tr>
<tr>
<td>OperationStateUI.cs</td>
<td>1+2</td>
<td>스폰/타이머/리셋 메서드, OnCustomerClicked에 MiniGameType 추가</td>
</tr>
<tr>
<td>DailyEndUI.cs</td>
<td>1+2</td>
<td>Grade.F UI 제거, event 키워드</td>
</tr>
<tr>
<td>StartDayStateUI.cs</td>
<td>2</td>
<td>event 키워드</td>
</tr>
<tr>
<td>CafeFlowController.cs</td>
<td>1</td>
<td>CurrentDay/MaxDays 제거</td>
</tr>
<tr>
<td>PlayerData.cs</td>
<td>1</td>
<td>IsBankrupt/CheckBankruptcy 제거</td>
</tr>
<tr>
<td>GameConstants.cs</td>
<td>1</td>
<td>BANKRUPTCY_THRESHOLD 제거</td>
</tr>
<tr>
<td>CafeSceneController.cs</td>
<td>1</td>
<td>CheckBankruptcy 제거</td>
</tr>
<tr>
<td>CafeTestBox.cs</td>
<td>1</td>
<td>CurrentDay 프로퍼티, SetDay 버튼 제거</td>
</tr>
<tr>
<td>DailyEndData.cs</td>
<td>1</td>
<td>Grade.F, IsBankrupt, CurrentDay 제거</td>
</tr>
<tr>
<td>IngredientConfig.cs</td>
<td>1</td>
<td>파산 관련 코멘트 제거</td>
</tr>
</tbody></table>
<h2 id="4-다음 단계">4. 다음 단계</h2>
<p><strong>코드 작업은 끝났다. 이제 Unity Editor 차례다.</strong></p>
<p>스크립트는 전부 검증됐고, 4-State + UI 스크립트 간 이벤트 시그니처도 맞다.
남은 건 Unity Inspector에서 SerializeField를 할당하는 수동 작업.</p>

<p>작업 순서:</p>
<ol>
<li><strong>E1</strong>: CafeScene Canvas에 4개 State UI 패널 생성 (초기 Inactive)</li>
<li><strong>E2</strong>: StartDayStateUI Inspector - 날씨/트렌드/이벤트 Sprite 할당</li>
<li><strong>E3</strong>: SetRecipeUI Inspector - 트레이, 탭, 팝업, 영업시작 버튼</li>
<li><strong>E4</strong>: OperationUI Inspector - customerSlots, coffeeSlots, 미니게임 팝업 3개</li>
<li><strong>E5</strong>: CustomerSlot 프리팹 생성 (220x320px, Button 컴포넌트 필수)</li>
<li><strong>E6</strong>: 미니게임 팝업 3개 (Takeout/LatteArt/DripCoffee)</li>
<li><strong>E7</strong>: DailyEndUI Inspector - 등급/순이익/평판/이벤트/상세모달</li>
<li><strong>E8</strong>: CafeFlowController Inspector 확인</li>
</ol>
<p>E4, E5, E6이 가장 복잡하다. CustomerSlot 프리팹과 미니게임 팝업 3개가 핵심.</p>
<p>Inspector 할당이 끝나면 Play Mode 통합 테스트. StartDay부터 DailyEnd까지 한 사이클 돌려본다.</p>

<hr />

<h2 id="5-교훈">5. 교훈</h2>
<p>"컴파일 성공"은 "작동함"을 의미하지 않는다.
에러 0개, 경고 몇 개인 상태에서 12개 로직 버그가 숨어있었다.</p>
<p>특히 Issue A(MiniGameType 재결정)는 코드만 봐서는 티가 안 난다.
DetermineMiniGameType()이라는 메서드가 두 군데에서 호출되는데, 하나는 스폰할 때 하나는 클릭할 때. 각각 따로 보면 정상이다. 흐름을 따라가야 비로소 "어? 이거 두 번 뽑히네?" 가 보인다.</p>
<p>교훈: <strong>코드 리뷰는 파일 단위가 아니라 플로우 단위로 해야 한다.</strong>
State A에서 만든 데이터가 State B를 거쳐 State C에서 어떻게 쓰이는지.
한 파일 안에서는 완벽해 보여도, 파일 간 연결에서 버그가 난다.</p>
<p>그리고 세션이 날아갔을 때 PROGRESS.json이 있어서 살았다.
진행 상황을 기록해두는 건 미래의 나를 위한 보험이다.
오늘 날아갔으면 "뭐 했더라?" 명미 이도 것이다.</p>