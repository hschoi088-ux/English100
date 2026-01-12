<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>영어회화100 스피드 퀴즈</title>
    <style>
        :root {
            --primary-color: #4a90e2;
            --bg-color: #f0f2f5;
            --card-front: #ffffff;
            --card-back: #fff9db;
        }

        body {
            font-family: 'Pretendard', -apple-system, sans-serif;
            background-color: var(--bg-color);
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            margin: 0;
            min-height: 100vh;
        }

        h1 { color: #1a1a1a; margin-bottom: 5px; }
        .subtitle { color: #666; margin-bottom: 30px; font-size: 0.95rem; }

        #quiz-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
            gap: 25px;
            width: 100%;
            max-width: 1200px;
        }

        .card {
            perspective: 1000px;
            height: 280px;
            cursor: pointer;
        }

        .card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            text-align: center;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
            box-shadow: 0 10px 20px rgba(0,0,0,0.08);
            border-radius: 20px;
        }

        .card.flipped .card-inner {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 30px;
            box-sizing: border-box;
            border-radius: 20px;
        }

        .card-front {
            background-color: var(--card-front);
            border: 2px solid #e1e8ed;
        }

        .card-back {
            background-color: var(--card-back);
            transform: rotateY(180deg);
            border: 2px solid #fcc419;
        }

        .day-badge {
            position: absolute;
            top: 15px;
            left: 15px;
            background: #eee;
            padding: 4px 10px;
            border-radius: 12px;
            font-size: 0.75rem;
            color: #777;
        }

        .kr-text { font-size: 1.15rem; font-weight: 700; margin-bottom: 20px; color: #2d3436; word-break: keep-all; }
        .en-blank { font-size: 1.25rem; color: var(--primary-color); line-height: 1.4; font-weight: 500; }
        .answer-text { font-size: 1.4rem; font-weight: 800; color: #e67e22; margin-bottom: 15px; }
        .summary { font-size: 0.95rem; color: #444; line-height: 1.6; word-break: keep-all; text-align: left; background: rgba(255,255,255,0.6); padding: 12px; border-radius: 10px; border-left: 4px solid #fcc419; }

        .btn-refresh {
            margin: 50px 0;
            padding: 15px 40px;
            font-size: 1.1rem;
            font-weight: 700;
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(74, 144, 226, 0.3);
            transition: 0.3s;
        }

        .btn-refresh:hover { transform: translateY(-3px); box-shadow: 0 6px 20px rgba(74, 144, 226, 0.4); }
    </style>
</head>
<body>

    <h1>📝 영어회화100 스피드 퀴즈</h1>
    <p class="subtitle">오늘의 랜덤 8문제를 맞춰보세요! (72개 표현 수록)</p>

    <div id="quiz-container"></div>

    <button class="btn-refresh" onclick="location.reload()">새로운 문제 섞기</button>

    <script>
        const quizData = [
            // Day 1
            { day: 1, kr: "재택근무는 저랑 안 맞아요.", en: "Working from home [isn’t] [for] [me].", ans: "isn’t for me", sum: "개인적 성향/취향에 맞지 않음을 나타내는 일반적인 표현입니다." },
            { day: 1, kr: "애들하고 정말 잘 노는군요. 선생님 할 생각은 해 보셨나요?", en: "No, no. Teaching [isn’t] [really] [for] [me].", ans: "isn’t really for me", sum: "be for: 성향상 나에게 맞는지 여부를 뜻합니다." },
            { day: 1, kr: "혼자 일하는 건 나랑 안 맞는다는 걸 느꼈어.", en: "I’ve found that working on my own [doesn’t] [really] [suit] [me].", ans: "doesn’t really suit me", sum: "suit: 스타일이나 상황이 어울리지 않는다는 뉘앙스입니다." },
            { day: 1, kr: "있잖아. 재택근무는 내 체질이 아니야.", en: "You know what? Working from home [doesn’t] [really] [work] [for] [me].", ans: "doesn’t really work for me", sum: "work for: 시간, 계획, 방식이 맞지 않거나 효과가 없다는 의미입니다." },
            // Day 2
            { day: 2, kr: "하루빨리 새 집으로 이사 가고 싶어요.", en: "I [can’t] [wait] [to] move into the new house.", ans: "can’t wait to", sum: "무언가가 너무 기대된다는 강한 설렘을 표현하는 구어체입니다." },
            { day: 2, kr: "이 프로젝트가 빨리 끝났으면 좋겠어요.", en: "I [can’t] [wait] [to] be done with this project.", ans: "can’t wait to", sum: "빨리 ~하고 싶어 참을 수 없는 상태를 말합니다." },
            { day: 2, kr: "그 여성분 어서 만나 보고 싶어.", en: "I’m really [anxious] [to] meet her.", ans: "anxious to", sum: "기대감에 초조함이나 조바심이 섞인 뉘앙스입니다." },
            { day: 2, kr: "말씀 많이 들었습니다. 하루빨리 함께 일하고 싶습니다.", en: "I [look] [forward] [to] working with you.", ans: "look forward to", sum: "기대감을 나타내는 더 격식 있고 정중한 표현입니다." },
            // Day 3
            { day: 3, kr: "죄송한데 조금 짧게 해 주시겠어요?", en: "[Do] [you] [mind] keeping it a bit short?", ans: "Do you mind", sum: "공손하게 부탁하거나 허락을 구하는 일반적인 표현입니다." },
            { day: 3, kr: "에어컨 좀 약하게 하면 안 될까요? 좀 추워서요.", en: "[Do] [you] [mind] turning down the air-conditioning?", ans: "Do you mind", sum: "상대방의 의사를 존중하며 부탁할 때 씁니다." },
            { day: 3, kr: "미안하지만 좀 도와주실 수 있을까요?", en: "[Would] [you] [mind] giving me a hand?", ans: "Would you mind", sum: "Do you mind보다 한층 더 정중하고 격식 있는 표현입니다." },
            // Day 4
            { day: 4, kr: "물가가 올라도 너무 올라요.", en: "Everything is getting [super] expensive.", ans: "super", sum: "'엄청, 매우'라는 의미로 형용사를 강조하는 구어체 표현입니다." },
            { day: 4, kr: "제가 요즘 이사 준비 때문에 엄청 바빴어요.", en: "I’ve been [super] busy with my upcoming move.", ans: "super", sum: "일상 대화에서 매우 강조할 때 자주 쓰입니다." },
            { day: 4, kr: "당신은 영어를 상당히 잘하는군요.", en: "Your English is [quite] good.", ans: "quite", sum: "'꽤, 상당히'라는 의미로, super보다 점잖은 강조입니다." },
            // Day 5
            { day: 5, kr: "중고차 같은 중고 물품 사는 거 어떻게 생각하세요?", en: "[How] [do] [you] [feel] [about] buying something second-hand?", ans: "How do you feel about", sum: "상대방의 의견이나 느낌을 부드럽게 물어보는 표현입니다." },
            { day: 5, kr: "공포 영화 어때?", en: "[How] [do] [you] [feel] [about] horror movies?", ans: "How do you feel about", sum: "제안이나 주제에 대한 감상을 묻습니다." },
            // Day 6
            { day: 6, kr: "재충전에는 캠핑만 한 게 없죠.", en: "[There] [is] [nothing] [like] camping to recharge your batteries.", ans: "There is nothing like", sum: "~만 한 것이 없다, 최고다라는 의미로 최상의 경험을 강조합니다." },
            { day: 6, kr: "주말 내내 넷플릭스 드라마 보는 게 최고야.", en: "[There’s] [nothing] [like] binging a show on Netflix all weekend.", ans: "There’s nothing like", sum: "비교 불가의 즐거움을 강조할 때 씁니다." },
            { day: 6, kr: "서울에서는 이 집 빵이 최고야.", en: "[You] [can’t] [find] [any] [better] [bread] in Seoul.", ans: "You can’t find any better bread", sum: "비교급을 이용한 최상급 표현입니다." },
            // Day 7
            { day: 7, kr: "너무 매운 것만 아니면 뭐든 다 좋아요.", en: "I [am] [up] [for] [anything], as long as it’s not too spicy.", ans: "am up for anything", sum: "be up/down for: 제안에 응하거나 할 의향이 있음을 나타냅니다." },
            { day: 7, kr: "뭐 하고 싶어? 난 뭐든 다 좋아.", en: "I’d [be] [up] [for] just about anything.", ans: "be up for", sum: "상대방의 제안을 기꺼이 받아들일 때 사용합니다." },
            { day: 7, kr: "같이 갈 사람이 필요해. 관심 있을까?", en: "Do you think you’d be [down]?", ans: "down", sum: "be down for 역시 be up for와 같은 의미의 구어체입니다." },
            // Day 8
            { day: 8, kr: "오늘 몸이 좀 안 좋아요.", en: "I [don’t] [feel] [quite] [right] [today].", ans: "don’t feel quite right today", sum: "몸이 좀 안 좋거나 컨디션이 평소 같지 않을 때 씁니다." },
            { day: 8, kr: "오늘은 저녁 안 먹을래. 오늘 속이 좀 안 좋아서.", en: "My stomach [doesn’t] [feel] [quite] [right] [today].", ans: "doesn’t feel quite right today", sum: "특정 부위가 불편할 때도 사용할 수 있습니다." },
            { day: 8, kr: "몸이 너무 안 좋아.", en: "I am [really] [not] [feeling] [well].", ans: "really not feeling well", sum: "'몸이 안 좋다'는 가장 일반적이고 직접적인 표현입니다." },
            { day: 8, kr: "감기 기운이 있다.", en: "I feel like I’m [coming] [down] [with] a cold.", ans: "coming down with", sum: "감기 같은 병에 막 걸리려는 초기 증상을 표현합니다." },
            // Day 9
            { day: 9, kr: "저 지금 스타벅스인데 커피 사다 드릴까요?", en: "[Would] [you] [like] [me] [to] grab you some coffee?", ans: "Would you like me to", sum: "공손하게 제안하는 '~해 드릴까요?' 표현입니다." },
            { day: 9, kr: "내가 따라가 줄까?", en: "[Do] [you] [want] [me] [to] come along with you?", ans: "Do you want me to", sum: "좀 더 구어적이고 편한 상대에게 사용하는 제안입니다." },
            { day: 9, kr: "Jeff와 회의 잡아 주십시오.", en: "[I’d] [like] [you] [to] set up a meeting with Jeff.", ans: "I’d like you to", sum: "윗사람이 정중하게 요청할 때 주로 사용합니다." },
            // Day 10
            { day: 10, kr: "가격대는 어느 정도 생각하세요?", en: "What price range [do] [you] [have] [in] [mind]?", ans: "do you have in mind", sum: "have something in mind: ~를 염두에 두다, 생각하다." },
            { day: 10, kr: "딱히 염두에 둔 차는 없습니다.", en: "I don’t really [have] [any] [car] [in] [mind].", ans: "have any car in mind", sum: "특정 대상을 생각 중인지 물을 때 씁니다." },
            // Day 11
            { day: 11, kr: "통번역대학원 진학을 고민하고 있어요.", en: "I [was] [thinking] [of] going to translation grad school.", ans: "was thinking of", sum: "~할까 생각 중이다라는 부드러운 계획 표현입니다." },
            { day: 11, kr: "다음 여행은 몽골을 생각 중이었는데, 안 가기로 했습니다.", en: "I [was] [thinking] [of] Mongolia for my next trip.", ans: "was thinking of", sum: "과거 진행형을 써서 조심스럽게 고민했음을 나타냅니다." },
            { day: 11, kr: "그쪽 부사장님과 회의를 잡았으면 합니다.", en: "I [was] [hoping] [to] set up a meeting.", ans: "was hoping to", sum: "~하기를 바라고 있었다는 공손한 느낌을 줍니다." },
            // Day 12
            { day: 12, kr: "나도 그렇게 돈이 많으면 좋으련만.", en: "I [wish] [I] [had] that much money.", ans: "wish I had", sum: "현재 사실과 반대되는 소망을 나타내는 가정법입니다." },
            { day: 12, kr: "제가 해산물을 못 먹어서 너무 아쉽네요.", en: "I [wish] [I] [could] eat seafood.", ans: "wish I could", sum: "할 수 없는 것에 대한 아쉬움을 표현합니다." },
            { day: 12, kr: "너무 일찍 일어나게 돼서 아쉬워.", en: "It’s [a] [shame] we have to leave so early.", ans: "a shame", sum: "~라서 아쉽다, 유감이다라는 감정 표현입니다." },
            // Day 13
            { day: 13, kr: "2시 30분 어때요?", en: "[How] [does] [2:30] [sound]?", ans: "How does 2:30 sound", sum: "제안에 대해 상대의 의견을 물을 때 씁니다." },
            { day: 13, kr: "월요일 월차 못 내면 그냥 경기도 가서 휴가 보내야 할 듯해. 어때?", en: "[How] [does] [that] [sound]?", ans: "How does that sound", sum: "자신의 계획을 제안하며 동의를 구합니다." },
            { day: 13, kr: "내가 보기엔 사기 같은데.", en: "That [sounds] [like] a scam to me.", ans: "sounds like", sum: "주관적인 추측이나 의견을 말할 때 씁니다." },
            // Day 14
            { day: 14, kr: "BTS는 뭔가 좀 달라.", en: "There is [something] [different] [about] BTS.", ans: "something different about", sum: "말로 콕 집어 설명하기 어렵지만 특별한 점이 있다는 뉘앙스입니다." },
            { day: 14, kr: "그 사람에게는 뭔가 끌리는 점이 있어요.", en: "There is [something] [about] him that I am attracted to.", ans: "something about", sum: "무언가 묘한 매력이 있음을 뜻합니다." },
            // Day 15
            { day: 15, kr: "다 먹은 거니?", en: "[Are] [you] [done] [with] your plate?", ans: "Are you done with", sum: "~을 끝내다, 마치다라는 의미입니다." },
            { day: 15, kr: "제가 빌려준 책 다 읽은 거죠?", en: "[Are] [you] [done] [with] the book I lent you?", ans: "Are you done with", sum: "동작의 완료 여부를 묻습니다." },
            { day: 15, kr: "서울 생활이 이젠 너무 지친다.", en: "I’m [done] [with] living in Seoul.", ans: "done with", sum: "질려서 더 이상 못 참겠다는 확장된 의미로도 쓰입니다." },
            // Day 16
            { day: 16, kr: "이 티셔츠 너한테 잘 어울려.", en: "This t-shirt [looks] [good] [on] you.", ans: "looks good on", sum: "옷이나 액세서리가 사람에게 잘 어울릴 때 씁니다." },
            { day: 16, kr: "너 핑크 엄청 잘 어울려.", en: "You [look] [great] [in] pink.", ans: "look great in", sum: "특정 색상이나 스타일 안에서 멋져 보임을 뜻합니다." },
            { day: 16, kr: "손이 예쁘셔서, 클러치 백이 더 잘 어울릴 듯합니다.", en: "I think a clutch would [suit] [you] [better].", ans: "suit you better", sum: "suit: 더 넓은 의미로 스타일이나 상황이 맞음을 뜻합니다." },
            // Day 17
            { day: 17, kr: "화요일 시간 괜찮으세요?", en: "[Does] [Tuesday] [work] [for] [you]?", ans: "Does Tuesday work for you", sum: "시간, 날짜, 계획 등이 상대에게 괜찮은지 묻는 표현입니다." },
            { day: 17, kr: "1시 이후에는 다 좋습니다.", en: "Anytime after 1 would [work] [for] [me].", ans: "work for me", sum: "나에게 일정이 맞음을 뜻합니다." },
            // Day 18
            { day: 18, kr: "말이 나왔으니 말인데, 어젯밤에 너랑 Nicole 사이에 무슨 일이 있었던 거야?", en: "[Speaking] [of] [which], what happened last night?", ans: "Speaking of which", sum: "대화 주제와 관련된 이야기를 이어갈 때 씁니다." },
            { day: 18, kr: "날씨 이야기가 나왔으니 말인데, 이번 가을은 유난히 따뜻했어.", en: "[Speaking] [of] [the] [weather], this autumn was warm.", ans: "Speaking of the weather", sum: "특정 소재를 언급하며 화제를 이어갑니다." },
            // Day 19
            { day: 19, kr: "나 내일 쉬어.", en: "I’m [taking] [tomorrow] [off].", ans: "taking tomorrow off", sum: "take (시간) off: ~만큼 휴가를 내거나 쉬다." },
            { day: 19, kr: "너 올해는 단 하루도 안 쉬었구나.", en: "You haven’t even [taken] [a] [single] [day] [off] this year.", ans: "taken a single day off", sum: "하루도 쉬지 못했음을 강조합니다." },
            { day: 19, kr: "나 당분간 진짜 좀 쉬어야겠어.", en: "I feel like I could really [use] [some] [time] [off].", ans: "use some time off", sum: "휴식이 필요함을 소망하는 표현입니다." },
            // Day 20
            { day: 20, kr: "제가 논문 쓰느라 바쁩니다.", en: "I’m [busy] [working] [on] my dissertation.", ans: "busy working on", sum: "be busy V-ing: ~하느라 바쁜 상태를 말합니다." },
            { day: 20, kr: "나 행정 업무 하느라 무지 바빠!", en: "I am [busy] [with] [all] this admin work!", ans: "busy with all", sum: "be busy with 명사: ~로 바쁘다." },
            { day: 20, kr: "요새 뭐 때문에 그렇게 바쁜 거야?", en: "What’s [keeping] [you] [so] [busy] these days?", ans: "keeping you so busy", sum: "무엇이 너를 바쁘게 만드는지 묻는 뉘앙스입니다." },
            // Day 21
            { day: 21, kr: "제 생각은 좀 다릅니다.", en: "I [don’t] [see] [it] [that] [way].", ans: "don’t see it that way", sum: "정중하게 반대 의견을 제시할 때 씁니다." },
            { day: 21, kr: "부동산 가격이 계속 하락할 거라지만 제 생각은 다릅니다.", en: "But I [don’t] [see] [it] [that] [way].", ans: "don’t see it that way", sum: "저는 그렇게 보지 않는다는 부드러운 거절입니다." },
            { day: 21, kr: "그 점은 동의하기 힘듭니다.", en: "I’m [not] [sure] [I] [agree] [with] [you].", ans: "not sure I agree with you", sum: "동의하기 어렵다고 직접적으로 반대하는 표현입니다." },
            // Day 22
            { day: 22, kr: "저한테는 좀 부담스러운 금액이었어요.", en: "It was something I could [barely] [afford].", ans: "barely afford", sum: "afford: 금전적/시간적으로 감당할 수 있음을 뜻합니다." },
            { day: 22, kr: "외식할 형편이 안 됩니다.", en: "We can’t [afford] [to] eat out.", ans: "afford to", sum: "경제적인 여유가 없음을 나타냅니다." },
            // Day 23
            { day: 23, kr: "제가 생각했던 것보다 비싸네요.", en: "This is all [out] [of] [my] [price] [range].", ans: "out of my price range", sum: "생각한 가격대나 예산을 벗어났음을 뜻합니다." },
            { day: 23, kr: "제일 저렴한 것도 제 예산 밖이더라고요.", en: "Even the cheapest one was [out] [of] [my] [price] [range].", ans: "out of my price range", sum: "예산 초과를 나타내는 정중한 표현입니다." },
            { day: 23, kr: "강남에서는 커피가 너무 비싸다.", en: "Coffee is [overpriced] in Gangnam.", ans: "overpriced", sum: "가격이 가치에 비해 과도하게 비싸다는 뜻입니다." },
            // Day 24
            { day: 24, kr: "싼 게 다 그렇지 뭐.", en: "[You] [get] [what] [you] [pay] [for].", ans: "You get what you pay for", sum: "지불한 만큼 얻는다, 즉 싼 게 비지떡이라는 의미입니다." },
            { day: 24, kr: "왠지 너무 싸다 싶었어요. 싼 게 비지떡이죠.", en: "[You] [get] [what] [you] [pay] [for].", ans: "You get what you pay for", sum: "품질이 가격에 비례함을 말합니다." },
            { day: 24, kr: "가성비가 가장 좋기 때문입니다.", en: "It is [the] [best] [bang] [for] [the] [buck].", ans: "the best bang for the buck", sum: "투자 대비 효과가 최고라는 '가성비' 강조 표현입니다." },
            // Day 25
            { day: 25, kr: "베이비시터 구했다니 다행입니다.", en: "I’m [glad] [you] [found] a babysitter.", ans: "glad you found", sum: "be glad: ~해서 다행이다, 기쁘다라는 감정입니다." },
            { day: 25, kr: "못 올 줄 알았더니 와서 다행이다!", en: "I’m [glad] [you] [could] [make] [it]!", ans: "glad you could make it", sum: "참석이나 성공적인 도착을 반길 때 씁니다." },
            { day: 25, kr: "시즌 3도 나온다니 너무 좋아.", en: "I am [happy] [that] they are going to come out with it.", ans: "happy that", sum: "glad와 비슷하게 기쁨을 표현합니다." }
        ];

        function getRandomQuizzes(data, count) {
            const shuffled = [...data].sort(() => 0.5 - Math.random());
            return shuffled.slice(0, count);
        }

        function renderQuiz() {
            const container = document.getElementById('quiz-container');
            container.innerHTML = ''; // 초기화
            const selected = getRandomQuizzes(quizData, 8);

            selected.forEach(q => {
                const blankCount = q.ans.split(' ').length;
                const card = document.createElement('div');
                card.className = 'card';
                card.onclick = () => card.classList.toggle('flipped');

                card.innerHTML = `
                    <div class="card-inner">
                        <div class="card-front">
                            <span class="day-badge">Day ${q.day}</span>
                            <div class="kr-text">${q.kr}</div>
                            <div class="en-blank">${q.en.replace(/\[.*?\]/g, '______')}</div>
                            <small style="margin-top:15px; color:#aaa; font-weight:bold;">Hint: ${blankCount} words</small>
                        </div>
                        <div class="card-back">
                            <div class="answer-text">✔ ${q.ans}</div>
                            <div class="summary">${q.sum}</div>
                        </div>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        // 초기 로딩
        renderQuiz();
    </script>
</body>
</html>
