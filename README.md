<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>영어회화100 스피드 퀴즈</title>
    <style>
        :root {
            --primary: #4a90e2;
            --accent: #ff922b;
            --bg: #f8f9fa;
            --text: #2d3436;
        }

        /* [보안] 텍스트 드래그 및 선택 금지 */
        body {
            font-family: 'Pretendard', -apple-system, sans-serif;
            background-color: var(--bg);
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }

        #setup-screen {
            padding: 40px 20px;
            text-align: center;
            width: 100%;
            max-width: 600px;
        }

        h1 { margin-bottom: 10px; color: var(--text); }
        .desc { color: #666; margin-bottom: 30px; }

        .range-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
        }

        .range-btn {
            padding: 20px;
            font-size: 1.1rem;
            font-weight: bold;
            background: white;
            border: 2px solid #e9ecef;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.2s;
            color: var(--text);
        }

        .range-btn:hover {
            border-color: var(--primary);
            color: var(--primary);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
        }

        #quiz-screen {
            display: none;
            width: 100%;
            max-width: 500px;
            padding: 20px;
            flex-direction: column;
            align-items: center;
        }

        .progress {
            margin-bottom: 15px;
            color: #868e96;
            font-weight: bold;
            font-size: 1.1rem;
        }

        .card-container {
            perspective: 1000px;
            width: 100%;
            height: 420px;
            cursor: pointer;
        }

        .card {
            position: relative;
            width: 100%;
            height: 100%;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
        }

        .card.flipped { transform: rotateY(180deg); }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            flex-direction: column;
            justify-content: center;
            padding: 35px;
            box-sizing: border-box;
            border-radius: 25px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            background: white;
        }

        .card-front { border: 4px solid var(--primary); }
        .card-back { 
            border: 4px solid var(--accent);
            background: #fffcf5;
            transform: rotateY(180deg); 
        }

        .kr-text { font-size: 1.35rem; font-weight: 800; color: #1a1a1a; margin-bottom: 25px; line-height: 1.5; word-break: keep-all; }
        .en-text { font-size: 1.25rem; color: #444; line-height: 1.6; word-break: break-word; }
        .highlight { color: var(--accent); font-weight: 800; border-bottom: 3px solid var(--accent); }

        .summary-box {
            margin-top: 25px;
            font-size: 0.95rem;
            color: #555;
            background: rgba(255,255,255,0.7);
            padding: 15px;
            border-radius: 12px;
            border-left: 5px solid var(--accent);
            text-align: left;
            line-height: 1.5;
        }

        .btn-next {
            margin-top: 30px;
            padding: 18px 50px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 50px;
            font-size: 1.2rem;
            font-weight: bold;
            cursor: pointer;
            display: none;
            box-shadow: 0 5px 15px rgba(74, 144, 226, 0.3);
        }
    </style>
</head>
<body oncontextmenu="return false" onselectstart="return false" ondragstart="return false">

    <div id="setup-screen">
        <h1>🎓 영어회화100 스피드 퀴즈</h1>
        <p class="desc">학습 구간을 선택하세요. (랜덤 8문항 출제)</p>
        <div class="range-grid">
            <button class="range-btn" onclick="startQuiz(1, 5)">Day 1 ~ 5</button>
            <button class="range-btn" onclick="startQuiz(6, 10)">Day 6 ~ 10</button>
            <button class="range-btn" onclick="startQuiz(11, 15)">Day 11 ~ 15</button>
            <button class="range-btn" onclick="startQuiz(16, 20)">Day 16 ~ 20</button>
            <button class="range-btn" onclick="startQuiz(21, 25)">Day 21 ~ 25</button>
            <button class="range-btn" onclick="startQuiz(1, 25)">Day 1 ~ 25 (전체)</button>
        </div>
    </div>

    <div id="quiz-screen">
        <div class="progress" id="progress-text">1 / 8</div>
        <div class="card-container" onclick="flipCard()">
            <div class="card" id="main-card">
                <div class="card-face card-front" id="front-content"></div>
                <div class="card-face card-back" id="back-content"></div>
            </div>
        </div>
        <button class="btn-next" id="next-btn" onclick="nextQuestion()">다음 문제로 →</button>
    </div>

    <script>
        // [보안] 키보드 단축키 방지 (F12, Ctrl+Shift+I, Ctrl+U 등)
        window.addEventListener('keydown', function(e) {
            if (e.keyCode == 123 || (e.ctrlKey && e.shiftKey && (e.keyCode == 73 || e.keyCode == 74)) || (e.ctrlKey && e.keyCode == 85)) {
                e.preventDefault();
                return false;
            }
        });

        const allData = [
            { d: 1, kr: "재택근무는 저랑 안 맞아요.", en: "Working from home [isn’t] [for] [me].", sum: "개인적 성향/취향에 맞지 않음을 나타냅니다." },
            { d: 1, kr: "애들하고 정말 잘 노는군요. 선생님 할 생각은 해 보셨나요?", en: "No, no. Teaching [isn’t] [really] [for] [me].", sum: "be for: 성향상 나에게 맞는지 여부를 뜻합니다." },
            { d: 1, kr: "혼자 일하는 건 나랑 안 맞는다는 걸 느꼈어.", en: "I’ve found that working on my own [doesn’t] [really] [suit] [me].", sum: "suit: 스타일이나 상황이 어울리지 않는 뉘앙스입니다." },
            { d: 1, kr: "있잖아. 재택근무는 내 체질이 아니야.", en: "You know what? Working from home [doesn’t] [really] [work] [for] [me].", sum: "work for: 시간, 계획, 방식이 맞지 않거나 효과가 없는 경우입니다." },
            { d: 2, kr: "하루빨리 새 집으로 이사 가고 싶어요.", en: "I [can’t] [wait] [to] move into the new house.", sum: "너무 기대되어 참을 수 없다는 강한 설렘의 구어체입니다." },
            { d: 2, kr: "이 프로젝트가 빨리 끝났으면 좋겠어요.", en: "I [can’t] [wait] [to] be done with this project.", sum: "빨리 마무리되기를 몹시 바라는 상태입니다." },
            { d: 2, kr: "그 여성분 어서 만나 보고 싶어.", en: "I’m really [anxious] [to] meet her.", sum: "anxious to: 기대감에 초조함이나 조바심이 섞인 뉘앙스입니다." },
            { d: 2, kr: "하루빨리 함께 일하고 싶습니다.", en: "I [look] [forward] [to] working with you.", sum: "look forward to: 기대감을 나타내는 더 격식 있고 정중한 표현입니다." },
            { d: 3, kr: "죄송한데 조금 짧게 해 주시겠어요?", en: "[Do] [you] [mind] keeping it a bit short?", sum: "공손하게 부탁하거나 허락을 구하는 일반적인 표현입니다." },
            { d: 3, kr: "에어컨 좀 약하게 하면 안 될까요? 좀 추워서요.", en: "[Do] [you] [mind] turning down the air-conditioning?", sum: "Do you mind: 상대의 의사를 존중하는 정중한 질문입니다." },
            { d: 3, kr: "미안하지만 좀 도와주실 수 있을까요?", en: "[Would] [you] [mind] giving me a hand?", sum: "Would you mind: Do you mind보다 한층 더 정중한 격식체입니다." },
            { d: 4, kr: "물가가 올라도 너무 올라요.", en: "Everything is getting [super] expensive.", sum: "super: '엄청, 매우'라는 의미의 강조 구어체입니다." },
            { d: 4, kr: "제가 요즘 이사 준비 때문에 엄청 바빴어요.", en: "I’ve been [super] busy with my upcoming move.", sum: "일상 대화에서 매우 바쁨을 강조합니다." },
            { d: 4, kr: "당신은 영어를 상당히 잘하는군요.", en: "Your English is [quite] good.", sum: "quite: '꽤, 상당히'라는 뜻으로 super보다 점잖은 느낌입니다." },
            { d: 5, kr: "중고차 같은 중고 물품 사는 거 어떻게 생각하세요?", en: "[How] [do] [you] [feel] [about] buying something second-hand?", sum: "상대방의 의견이나 느낌을 부드럽게 물어보는 표현입니다." },
            { d: 5, kr: "저녁 먹고 우리 집에 가서 공포 영화 어때?", en: "[How] [do] [you] [feel] [about] horror movies?", sum: "How do you feel about: 주제나 제안에 대한 선호도를 묻습니다." },
            { d: 6, kr: "재충전에는 캠핑만 한 게 없죠.", en: "[There] [is] [nothing] [like] camping to recharge your batteries.", sum: "There is nothing like: ~만 한 것이 없다, 최고다라는 뜻입니다." },
            { d: 6, kr: "주말 내내 넷플릭스 드라마 보는 게 최고야.", en: "[There’s] [nothing] [like] binging a show on Netflix all weekend.", sum: "최상의 경험을 강조할 때 사용합니다." },
            { d: 6, kr: "서울에서는 이 집 빵이 최고야.", en: "[You] [can’t] [find] [any] [better] [bread] in Seoul.", sum: "can't find any better: 비교급을 이용한 최상급 강조입니다." },
            { d: 7, kr: "너무 매운 것만 아니면 뭐든 다 좋아요.", en: "I [am] [up] [for] [anything], as long as it’s not too spicy.", sum: "be up/down for: 제안에 응하거나 할 의향이 있음을 뜻합니다." },
            { d: 7, kr: "뭐 하고 싶어? 난 뭐든 다 좋아.", en: "I’d [be] [up] [for] just about anything.", sum: "제안을 기꺼이 수락할 때 씁니다." },
            { d: 7, kr: "같이 갈 사람이 필요해. 관심 있을까?", en: "Do you think you’d be [down]?", sum: "down 역시 up과 동일하게 제안에 참여할 의사를 묻습니다." },
            { d: 8, kr: "오늘 몸이 좀 안 좋아요.", en: "I [don’t] [feel] [quite] [right] [today].", sum: "not feel quite right: 컨디션이 평소 같지 않을 때 씁니다." },
            { d: 8, kr: "오늘은 저녁 안 먹을래. 오늘 속이 좀 안 좋아서.", en: "My stomach [doesn’t] [feel] [quite] [right] [today].", sum: "몸의 특정 상태가 불편함을 부드럽게 표현합니다." },
            { d: 8, kr: "몸이 너무 안 좋아.", en: "I am [really] [not] [feeling] [well].", sum: "not feeling well: 몸이 안 좋다는 가장 직접적인 표현입니다." },
            { d: 8, kr: "감기 기운이 있다.", en: "I feel like I’m [coming] [down] [with] a cold.", sum: "coming down with: 병의 초기 증상을 표현합니다." },
            { d: 9, kr: "저 지금 스타벅스인데 커피 사다 드릴까요?", en: "[Would] [you] [like] [me] [to] grab you some coffee?", sum: "Would you like me to: 공손하게 도움이나 행동을 제안합니다." },
            { d: 9, kr: "내가 따라가 줄까?", en: "[Do] [you] [want] [me] [to] come along with you?", sum: "Do you want me to: 편한 사이에서 사용하는 제안입니다." },
            { d: 9, kr: "Jeff와 회의 잡아 주십시오.", en: "[I’d] [like] [you] [to] set up a meeting with Jeff.", sum: "I'd like you to: 정중하게 요청하는 표현입니다." },
            { d: 10, kr: "가격대는 어느 정도 생각하세요?", en: "What price range [do] [you] [have] [in] [mind]?", sum: "have something in mind: ~를 염두에 두다, 생각하다." },
            { d: 10, kr: "딱히 염두에 둔 차는 없습니다.", en: "I don’t really [have] [any] [car] [in] [mind].", sum: "특정한 대상을 고려 중인지 물을 때 사용합니다." },
            { d: 11, kr: "통번역대학원 진학을 고민하고 있어요.", en: "I [was] [thinking] [of] going to translation grad school.", sum: "was thinking of: ~할까 생각 중이다라는 부드러운 계획 표현." },
            { d: 11, kr: "다음 여행은 몽골을 생각 중이었는데, 안 가기로 했습니다.", en: "I [was] [thinking] [of] Mongolia for my next trip.", sum: "과거 진행형으로 조심스러운 계획을 나타냅니다." },
            { d: 11, kr: "그쪽 부사장님과 회의를 잡았으면 합니다.", en: "I [was] [hoping] [to] set up a meeting.", sum: "was hoping to: ~하기를 바라고 있었다는 공손한 어조입니다." },
            { d: 12, kr: "나도 그렇게 돈이 많으면 좋으련만.", en: "I [wish] [I] [had] that much money.", sum: "I wish I: 현재 사실과 반대되는 소망을 나타내는 가정법입니다." },
            { d: 12, kr: "제가 해산물을 못 먹어서 너무 아쉽네요.", en: "I [wish] [I] [could] eat seafood.", sum: "할 수 없는 능력에 대한 아쉬움을 표현합니다." },
            { d: 12, kr: "너무 일찍 일어나게 돼서 아쉬워.", en: "It’s [a] [shame] we have to leave so early.", sum: "It's a shame: ~라서 아쉽다, 유감이다라는 감정입니다." },
            { d: 13, kr: "2시 30분 어때요?", en: "[How] [does] [2:30] [sound]?", sum: "How does...sound: 제안에 대한 상대의 의견을 묻습니다." },
            { d: 13, kr: "경기도 가서 휴가 보내야 할 듯해. 어때?", en: "[How] [does] [that] [sound]?", sum: "자신의 계획을 제시하며 동의를 구합니다." },
            { d: 13, kr: "내가 보기엔 사기 같은데.", en: "That [sounds] [like] a scam to me.", sum: "sound like: ~처럼 들린다, ~인 것 같다는 주관적 의견입니다." },
            { d: 14, kr: "BTS는 뭔가 좀 달라.", en: "There is [something] [different] [about] BTS.", sum: "There is something about: 묘하게 특별한 점이 있다는 뉘앙스입니다." },
            { d: 14, kr: "그 사람에게는 뭔가 끌리는 점이 있어요.", en: "There is [something] [about] him that I am attracted to.", sum: "말로 다 설명하기 힘든 매력이 있음을 뜻합니다." },
            { d: 15, kr: "다 먹은 거니?", en: "[Are] [you] [done] [with] your plate?", sum: "be done with: ~을 끝내다, 다 마쳤음을 의미합니다." },
            { d: 15, kr: "제가 빌려준 책 다 읽은 거죠?", en: "[Are] [you] [done] [with] the book I lent you?", sum: "동작의 완료 여부를 확인하는 표현입니다." },
            { d: 15, kr: "서울 생활이 이젠 너무 지친다.", en: "I’m [done] [with] living in Seoul.", sum: "질려서 더 이상 못 참겠다는 확장된 의미입니다." },
            { d: 16, kr: "이 티셔츠 너한테 잘 어울려.", en: "This t-shirt [looks] [good] [on] you.", sum: "look good on: 옷이나 액세서리가 사람에게 어울릴 때 씁니다." },
            { d: 16, kr: "너 핑크 엄청 잘 어울려.", en: "You [look] [great] [in] pink.", sum: "look great in: 특정 색상이나 스타일 안에서 멋져 보임을 뜻합니다." },
            { d: 16, kr: "클러치 백이 더 잘 어울릴 듯합니다.", en: "I think a clutch would [suit] [you] [better].", sum: "suit: 스타일이나 상황이 전체적으로 잘 맞는다는 뉘앙스입니다." },
            { d: 17, kr: "화요일 시간 괜찮으세요?", en: "[Does] [Tuesday] [work] [for] [you]?", sum: "work for: 시간이나 계획이 상대에게 괜찮은지 묻습니다." },
            { d: 17, kr: "1시 이후에는 다 좋습니다.", en: "Anytime after 1 would [work] [for] [me].", sum: "해당 시간이 자신에게 편함을 의미합니다." },
            { d: 18, kr: "말이 나왔으니 말인데, 어젯밤에 무슨 일이 있었던 거야?", en: "[Speaking] [of] [which], what happened last night?", sum: "Speaking of which: 방금 언급된 주제로 말을 이어갈 때 씁니다." },
            { d: 18, kr: "날씨 이야기가 나왔으니 말인데, 이번 가을은 따뜻했어.", en: "[Speaking] [of] [the] [weather], this autumn was warm.", sum: "특정 화제를 연결 고리로 대화를 이어갑니다." },
            { d: 19, kr: "나 내일 쉬어.", en: "I’m [taking] [tomorrow] [off].", sum: "take (시간) off: ~만큼 휴가를 내거나 쉬다." },
            { d: 19, kr: "너 올해는 단 하루도 안 쉬었구나.", en: "You haven’t even [taken] [a] [single] [day] [off] this year.", sum: "하루도 쉬지 못했음을 강조할 때 씁니다." },
            { d: 19, kr: "나 당분간 진짜 좀 쉬어야겠어.", en: "I feel like I could really [use] [some] [time] [off].", sum: "use some time off: 휴식이 절실히 필요함을 나타냅니다." },
            { d: 20, kr: "제가 논문 쓰느라 바쁩니다.", en: "I’m [busy] [working] [on] my dissertation.", sum: "be busy V-ing: ~하느라 바쁜 상태를 말합니다." },
            { d: 20, kr: "나 행정 업무 하느라 무지 바빠!", en: "I am [busy] [with] [all] this admin work!", sum: "be busy with: 명사(일)로 인해 바쁜 상태입니다." },
            { d: 20, kr: "요새 뭐 때문에 그렇게 바쁜 거야?", en: "What’s [keeping] [you] [so] [busy] these days?", sum: "무엇이 상대를 바쁘게 만드는지 묻는 뉘앙스입니다." },
            { d: 21, kr: "제 생각은 좀 다릅니다.", en: "I [don’t] [see] [it] [that] [way].", sum: "I don't see it that way: 정중하게 반대 의견을 제시합니다." },
            { d: 21, kr: "부동산 가격이 계속 하락할 거라지만 제 생각은 다릅니다.", en: "But I [don’t] [see] [it] [that] [way].", sum: "저는 그렇게 보지 않는다는 부드러운 거절입니다." },
            { d: 21, kr: "그 점은 동의하기 힘듭니다.", en: "I’m [not] [sure] [I] [agree] [with] [you].", sum: "not sure I agree: 직접적으로 동의하기 어렵음을 알립니다." },
            { d: 22, kr: "저한테는 좀 부담스러운 금액이었어요.", en: "It was something I could [barely] [afford].", sum: "afford: 금전적/시간적 여유가 있음을 뜻합니다." },
            { d: 22, kr: "외식할 형편이 안 됩니다.", en: "We can’t [afford] [to] eat out.", sum: "형편이 안 되어 감당할 수 없음을 말합니다." },
            { d: 23, kr: "제가 생각했던 것보다 비싸네요.", en: "This is all [out] [of] [my] [price] [range].", sum: "out of my price range: 생각한 예산을 벗어났다는 뜻입니다." },
            { d: 23, kr: "제일 저렴한 것도 제 예산 밖이더라고요.", en: "Even the cheapest one was [out] [of] [my] [price] [range].", sum: "가격대가 너무 높아 부담스러울 때 씁니다." },
            { d: 23, kr: "강남에서는 커피가 너무 비싸다.", en: "Coffee is [overpriced] in Gangnam.", sum: "overpriced: 품질에 비해 가격이 과하게 책정되었다는 뜻." },
            { d: 24, kr: "싼 게 다 그렇지 뭐.", en: "[You] [get] [what] [you] [pay] [for].", sum: "지불한 만큼 얻는다, 즉 싼 게 비지떡이라는 의미입니다." },
            { d: 24, kr: "왠지 너무 싸다 싶었어요. 싼 게 비지떡이죠.", en: "[You] [get] [what] [you] [pay] [for].", sum: "품질과 가격의 상관관계를 말하는 속담 같은 표현입니다." },
            { d: 24, kr: "가성비가 가장 좋기 때문입니다.", en: "It is [the] [best] [bang] [for] [the] [buck].", sum: "best bang for the buck: 가성비가 최고라는 구어체입니다." },
            { d: 25, kr: "베이비시터 구했다니 다행입니다.", en: "I’m [glad] [you] [found] a babysitter.", sum: "be glad: ~해서 다행이다, 기쁘다라는 긍정적 표현." },
            { d: 25, kr: "못 올 줄 알았더니 와서 다행이다!", en: "I’m [glad] [you] [could] [make] [it]!", sum: "make it: 도착하다, 참석하다. 와줘서 고맙다는 뉘앙스입니다." },
            { d: 25, kr: "시즌 3도 나온다니 너무 좋아.", en: "I am [happy] [that] they are going to come out with it.", sum: "상황에 대해 만족감과 기쁨을 나타냅니다." }
        ];

        let currentQuestions = [];
        let currentIndex = 0;

        function startQuiz(start, end) {
            const filtered = allData.filter(item => item.d >= start && item.d <= end);
            currentQuestions = filtered.sort(() => 0.5 - Math.random()).slice(0, 8);
            if(currentQuestions.length === 0) { alert("문제가 없습니다."); return; }
            currentIndex = 0;
            document.getElementById('setup-screen').style.display = 'none';
            document.getElementById('quiz-screen').style.display = 'flex';
            showQuestion();
        }

        function showQuestion() {
            const q = currentQuestions[currentIndex];
            const card = document.getElementById('main-card');
            const nextBtn = document.getElementById('next-btn');
            card.classList.remove('flipped');
            nextBtn.style.display = 'none';
            document.getElementById('progress-text').innerText = `${currentIndex + 1} / ${currentQuestions.length}`;
            const blankedEn = q.en.replace(/\[(.*?)\]/g, (match, p1) => {
                const words = p1.trim().split(/\s+/).length;
                return "____ ".repeat(words).trim();
            });
            document.getElementById('front-content').innerHTML = `<div class="kr-text">${q.kr}</div><div class="en-text">${blankedEn}</div><p style="color:#aaa; font-size:0.85rem; margin-top:30px; font-weight:bold;">탭하여 정답 확인</p>`;
            const highlightedEn = q.en.replace(/\[(.*?)\]/g, '<span class="highlight">$1</span>');
            document.getElementById('back-content').innerHTML = `<div class="kr-text">${q.kr}</div><div class="en-text">${highlightedEn}</div><div class="summary-box">💡 ${q.sum}</div>`;
        }

        function flipCard() {
            const card = document.getElementById('main-card');
            if(!card.classList.contains('flipped')) {
                card.classList.add('flipped');
                document.getElementById('next-btn').style.display = 'block';
            }
        }

        function nextQuestion() {
            currentIndex++;
            if (currentIndex < currentQuestions.length) { showQuestion(); } 
            else { alert("🎉 학습 완료! 초기 화면으로 돌아갑니다."); location.reload(); }
        }
    </script>
</body>
</html>
