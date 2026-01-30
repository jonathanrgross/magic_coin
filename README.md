<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="https://fonts.googleapis.com/css2?family=IM+Fell+English&display=swap" rel="stylesheet">
    <title>Magic Coin Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'IM Fell English', serif;
            background: linear-gradient(330deg, #001a4d 0%, #004d7a 25%, #0066cc 50%, #00a3e0 75%, #00d9ff 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            background: white;
            border-radius: 20px;
            padding: 30px;
            max-width: 600px;
            width: 100%;
            box-shadow: 0 10px 40px rgba(0,0,0,0.3);
        }
        
        h1 {
            color: #333;
            margin-bottom: 20px;
            text-align: center;
        }
        
        .message {
            color: #1a1a1a;
            margin-bottom: 24px;
            text-align: center;
            font-size: 16px;
        }
        
        .screen {
            display: none;
        }
        
        .screen.active {
            display: block;
        }
        
        /* Prize Selection Screen */
        .prize-list {
            display: grid;
            gap: 12px;
            margin-bottom: 18px;
        }
        
        .prize-btn {
            padding: 15px;
            border: 2px solid #667eea;
            border-radius: 10px;
            background: white;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s;
        }
        
        .prize-btn:hover {
            background: #667eea;
            color: white;
        }
        
        .prize-btn.selected {
            background: #667eea;
            color: white;
        }
        
        /* Question Screen */
        .progress {
            text-align: center;
            margin-bottom: 20px;
            font-size: 18px;
            font-weight: bold;
            color: #667eea;
        }
        
        
        .question {
            margin-bottom: 20px;
            text-align: center;
            font-size: 18px;
            font-weight: bold;
            color: #333;
        }
        
        .options {
            display: grid;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .option-btn {
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 10px;
            background: white;
            cursor: pointer;
            font-size: 16px;
            transition: all 0.3s;
            text-align: left;
        }
        
        .option-btn:hover {
            border-color: #667eea;
            background: #f0f4ff;
        }
        
        .option-btn.selected {
            border-color: #667eea;
            background: #667eea;
            color: white;
        }
        
        .option-btn.correct {
            border-color: #4CAF50;
            background: #4CAF50;
            color: white;
        }

        .option-btn.incorrect {
            border-color: #f44336;
            background: #f44336;
            color: white;
        }

        .option-btn.disabled {
            cursor: not-allowed;
            opacity: 0.7;
        }

        /* Results Screen */
        .result-message {
            text-align: center;
            color: #333;
            font-size: 18px;
            margin-bottom: 30px;
            line-height: 1.6;
        }
        
        .result-emoji {
            font-size: 60px;
            margin-bottom: 20px;
        }
        
        /* Buttons */
        .button-group {
            display: grid;
            gap: 10px;
            margin-top: 30px;
        }
        
        button {
            padding: 15px 30px;
            font-size: 16px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .btn-primary {
            background: #667eea;
            color: white;
        }
        
        .btn-primary:hover {
            background: #764ba2;
            transform: translateY(-2px);
        }
        
        .btn-secondary {
            background: #ddd;
            color: #333;
        }
        
        .btn-secondary:hover {
            background: #bbb;
        }
        
        @media (max-width: 600px) {
            .container {
                padding: 20px;
                border-radius: 15px;
            }
            
            h1 {
                font-size: 24px;
            }
            
            .fish-image {
                height: 250px;
            }
        }

        .footer {
            position: fixed;
            bottom: 20px;
            left: 0;
            right: 0;
            text-align: center;
        }

        .footer a {
            color: white;
            font-size: 14px;
            text-decoration: none;
            opacity: 0.8;
            transition: opacity 0.3s;
        }

        .footer a:hover {
            opacity: 1;
        }

    </style>
</head>
<body>
    <div class="container">
        <!-- Screen 1: Welcome & Prize Selection -->
        <div class="screen active" id="prizeScreen">
            <!--<h1>🐚 The Magic Shell</h1>-->
            <div class="message">
                <p>Which school of magic do you wish to claim?</p>
                <p style="margin-top: 15px;">Choose one of the following challenges.</p>
                <p style="margin-top: 15px;">If you complete the challenge, you will get a prize.</p>
            </div>
            <div class="prize-list" id="prizeList"></div>
            <div class="button-group">
                <button class="btn-primary" onclick="startChallenge()">Claim Challenge</button>
            </div>
        </div>
        
        <!-- Screen 2: Question Screen -->
        <div class="screen" id="questionScreen">
            <div class="progress" id="progress"></div>
            <div class="question" id="questionText"></div>
            <div class="options" id="optionsList"></div>
            <div class="button-group">
                <button class="btn-primary" onclick="submitAnswer()">Submit Answer</button>
            </div>
        </div>
        
        <!-- Screen 3: Results Screen -->
        <div class="screen" id="resultScreen">
            <h1 id="resultTitle"></h1>
            <!--<div class="result-emoji" id="resultEmoji"></div>-->
            <div class="result-message" id="resultMessage"></div>
            <div class="button-group">
                <button class="btn-secondary" onclick="location.reload()">Play Again</button>
            </div>
        </div>


    </div>

    <script>
        // Game Data
        const prizes = [
            { id: 1, name: "Divination: Locate and identify the brightest planet in the sky.", emoji: "✨", 
            message: "Go to the beach of Terranea. Though it is a domain of nobility, through an ancient pact they are bound to permit all to access the Beach. Take the path down to the sea. At the cliff face, climb over the rocks. If you visit when the tide is low and the surf is calm, you may enter the small cave." },
            { id: 2, name: "Alchemy: Get me a mezcal old fashioned.", emoji: "🧠", 
            message: "Go to Crescent Bay and travel to the southeastern end.  Walk upon the rocks and around the bend.  You will come upon a secluded area known as Shell Beach." },
            { id: 3, name: "Enchantment: tell a joke that makes me laugh.", emoji: "👑", 
            message: "If you seek a bridge of tranquility where you may watch a river run and hear the birds sing, travel along the LA River, 30 miles upstream from the sea.  There, near Griffith Park, you will find the bridge of Taylor Yard. It is a span of vermillion, with one side in the town of frogs and the other in the park of Cypress." },
            { id: 4, name: "Prestidigitation: Perform some sleight of hand.", emoji: "🌟", 
            message: "Go to Point Dume, and travel to the furthermost point from the entrance.  There, at the cliff face, at the southernmost point, scramble over the rocks.  You will find yourself at Pirate's Cove Beach." },
            { id: 5, name: "Warding: Donate to the Immigrant Defenders Law Center.", emoji: "🌟", 
            message: "If you wish to see a living kelp forest without diving below the waves, go to the California Science Center. All are admitted without paying a single copper. There, they have created a small pocket of ocean, complete with living kelp. A mechanical contraption moves every second of every day to recreate the tides, without which the kelp could not live." },
            { id: 6, name: "Illusion: Take a good photo of me.", emoji: "🌟", 
            message: "Up the hill from Bruce's Beach is a village schoolhouse called Grandview Elementary. On weekends, any and all may park there without paying a coin." }
        ];


        const questionPool = [
            {
                question: "Abalone Cove is falling into the sea at a rate of 1 foot per month. The local landowners want to stop it. What can they do?",
                options: ["Make an offering to the god of storms.", 
                "Construct vast pumps to remove groundwater in the hopes it drains the lubricant beneath the land.", 
                "Find somewhere else to live- you cannot defy the gods."],
                correct: 2
            },
            {
                question: "The seas are poisoned by fertilizer from croplands flowing into rivers and out to the ocean. What ritual can remedy this?",
                options: ["Add twice the fertilizer half as often, and only when the Moon is full.", 
                "Hire the dark wizard Monsanto to blend a new elixir for the field.", 
                "Plant a grove beside the rivers, 50 feet wide on each side. The spirits of the forest will cleanse the rivers."],
                correct: 2
            },
            {
                question: "A villager tells you the nearby waters are full of sharks.  How should you protect yourself?",
                options: ["Don a suit of dazzling patterns. This will keep them from eating you.", 
                "Grab a spear and assemble a hunting party.  Kill them until none remain.", 
                "Ignore them. There are always sharks nearby, and they simply keep to themselves."],
                correct: 2
            },
            {
                question: "You are stung by a stingray. How should you treat the wound?",
                options: ["Seek a life guardian. They will soak the foot in nearly scorching water, then bandage the wound.", 
                "Suck the venom out with your mouth. This is the only way to save the foot.", 
                "Do nothing and wait for the pain to pass."],
                correct: 0
            },
            {
                question: "You are told there are stingrays in the water. How may you you protect yourself?",
                options: ["Carry a spear and stab one to intimidate the others.",
                "Stomp your feet as you walk to hurt them before they can hurt you.",
                "Show them respect as you enter their domain.  Shuffle your feet so you do not tread upon them."],
                correct: 2
            },
            {
                question: "You're caught in a riptide and its carrying you out to sea.  What should you do?",
                options: ["Swim as quickly as possible to the shore in a single burst of speed.", 
                "To conserve your strength, swim against it slowly for as long as it takes.",
                "Swim crosswise to the rip current. It is narrow, and you will quickly exit to calmer waters."],
                correct: 2
            },
            {
                question: "The wells have run dry, and the town is desperate for water. What should be done?",
                options: ["Build a fortress of alchemy that can turn seawater into riverwater.  Beside it construct a furnace, for alchemy requires much power to work its magic.",
                "Confront the lords of the alfalfa croplands. They will try to deceive you, but they are taking far more than their share of the river."],
                correct: 1
            },
            {
                question: "William Mulholland was a man of great hubris and unquenchable thirst. What final disgrace brought him shame from which he never recovered?",
                options: ["He attempted to build an aquaduct to steal all of the water in the Owens Valley but was defeated by armed resistance in what came to be known as the California Water Wars.",
                "He started building a dam in Yosemite Valley but never finished.",
                "He built a dam in San Francisquito Canyon that collapsed and claimed hundreds of lives."],
                correct: 2
            },
            {
                question: "What cursed isle is held by the evil army of La Migra, from which they plot their attacks?",
                options: ["Saint Catalina",
                "Anacapa",
                "Terminal Island"],
                correct: 2
            },
            {
                question: "What is the only beach in the County of the Angels where travelers may kindle fires in pits upon the sand?",
                options: ["The beach of Dockweiler, beneath the iron dragons.",
                "Will Rogers Beach, in the shadow of the Palisades Hills.",
                "Playa Del Rey, just south of the breakwater."],
                correct: 0
            },
            {
                question: "Who was the princeling that fought for years to block commonfolk from visiting the beach by his Malibu palace, but was ultimately defeated by the California Coastal Commission?",
                options: ["David Geffen",
                "Eli Broad",
                "Frank McCourt"],
                correct: 0
            },


        ];

        // Helper Functions
        function shuffleArray(array) {
            const shuffled = [...array];
            for (let i = shuffled.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
            }
            return shuffled;
        }

        function getRandomQuestions(pool, count) {
            const shuffled = shuffleArray(pool);
            return shuffled.slice(0, count);
        }

        function shuffleQuestionOptions(question) {
            const optionsWithIndices = question.options.map((option, index) => ({
                option,
                originalIndex: index
            }));
            
            const shuffled = shuffleArray(optionsWithIndices);
            
            return {
                options: shuffled.map(item => item.option),
                correctIndex: shuffled.findIndex(item => item.originalIndex === question.correct)
            };
        }

        // Game State
        let gameState = {
            selectedPrize: null,
            selectedQuestions: [],
            currentQuestion: 0,
            correctAnswers: 5,
            selectedAnswer: null,
            correctAnswerIndex: 0,
            maxQuestions: 8,
            requiredCorrect: 4
        };

        // Initialize
        function init() {
            renderPrizes();
        }

        // Render Prize Selection
        function renderPrizes() {
            const prizeList = document.getElementById("prizeList");
            prizeList.innerHTML = prizes.map((prize, index) => `
                <button class="prize-btn" onclick="selectPrize(${index})" id="prize-${index}">
                    ${prize.name}
                </button>
            `).join("");
        }

        // Select Prize
        function selectPrize(index) {
            document.querySelectorAll(".prize-btn").forEach(btn => btn.classList.remove("selected"));
            document.getElementById(`prize-${index}`).classList.add("selected");
            gameState.selectedPrize = prizes[index];
        }


        // Start Challenge
        function startChallenge() {
            if (!gameState.selectedPrize) {
                alert("Please select a prize first!");
                return;
            }

            // Start Challenge
        function startChallenge() {
            if (!gameState.selectedPrize) {
                alert("Please select a challenge first!");
                return;
            }
            
            // Claim this challenge in Firebase
            claimChallenge(gameState.selectedPrize.id);
            
            // Skip directly to results
            showResults();
        }
            
            // Reset game state
            gameState.selectedQuestions = getRandomQuestions(questionPool, gameState.maxQuestions);
            gameState.currentQuestion = 0;
            gameState.correctAnswers = 0;
            gameState.selectedAnswer = null;
            
            showScreen("questionScreen");
            loadQuestion();
        }

        
        // Load Question
        function loadQuestion() {
            const question = gameState.selectedQuestions[gameState.currentQuestion];
            const shuffled = shuffleQuestionOptions(question);
            
            //document.getElementById("progress").textContent = `Question ${gameState.currentQuestion + 1}/8 - Correct: ${gameState.correctAnswers}`;
            document.getElementById("questionText").textContent = question.question;
            
            gameState.correctAnswerIndex = shuffled.correctIndex;
            
            const optionsList = document.getElementById("optionsList");
            optionsList.innerHTML = shuffled.options.map((option, index) => `
                <button class="option-btn" onclick="selectOption(${index})" id="option-${index}">
                    ${option}
                </button>
            `).join("");
            
            gameState.selectedAnswer = null;
        }

        // Select Answer Option
        function selectOption(index) {
            document.querySelectorAll(".option-btn").forEach(btn => btn.classList.remove("selected"));
            document.getElementById(`option-${index}`).classList.add("selected");
            gameState.selectedAnswer = index;
        }

        

        // Submit Answer
        function submitAnswer() {
            if (gameState.selectedAnswer === null) {
                alert("Please select an answer!");
                return;
            }

            // Hide the submit button
            const submitBtn = document.querySelector(".btn-primary");
            submitBtn.style.display = "none";

            // Disable all answer buttons so they can't change their answer
            document.querySelectorAll(".option-btn").forEach(btn => {
                btn.disabled = true;
                btn.style.opacity = "0.5";
            });

            // Show correct/incorrect feedback
            const selectedBtn = document.getElementById(`option-${gameState.selectedAnswer}`);
            
            if (selectedBtn) {
                if (gameState.selectedAnswer === gameState.correctAnswerIndex) {
                    selectedBtn.classList.add("correct");
                    gameState.correctAnswers++;
                } else {
                    selectedBtn.classList.add("incorrect");
                    const correctBtn = document.getElementById(`option-${gameState.correctAnswerIndex}`);
                    if (correctBtn) {
                        correctBtn.classList.add("correct");
                    }
                }
            }

            // Wait 1 second before moving to next question
            setTimeout(() => {
                gameState.currentQuestion++;

                if (gameState.correctAnswers >= gameState.requiredCorrect) {
                    showResults();
                } else if (gameState.currentQuestion >= gameState.selectedQuestions.length) {
                    showResults();
                } else {
                    loadQuestion();
                }
            }, 1000);
        }
        // Submit Answer
        function submitAnswer() {
            if (gameState.selectedAnswer === null) {
                alert("Please select an answer!");
                return;
            }

            // Hide the submit button
            const submitBtn = document.querySelector(".btn-primary");
            submitBtn.style.display = "none";

            // Disable all answer buttons so they can't change their answer
            document.querySelectorAll(".option-btn").forEach(btn => {
                btn.disabled = true;
                btn.style.opacity = "0.5";
            });

            // Show correct/incorrect feedback
            const selectedBtn = document.getElementById(`option-${gameState.selectedAnswer}`);
            
            if (selectedBtn) {
                if (gameState.selectedAnswer === gameState.correctAnswerIndex) {
                    selectedBtn.classList.add("correct");
                    gameState.correctAnswers++;
                } else {
                    selectedBtn.classList.add("incorrect");
                    const correctBtn = document.getElementById(`option-${gameState.correctAnswerIndex}`);
                    if (correctBtn) {
                        correctBtn.classList.add("correct");
                    }
                }
            }

            // Wait 1 second before moving to next question
            setTimeout(() => {
                gameState.currentQuestion++;

                // Check if they won
                if (gameState.correctAnswers >= gameState.requiredCorrect) {
                    showResults();
                } 
                // Check if they ran out of questions (failed)
                else if (gameState.currentQuestion >= gameState.selectedQuestions.length) {
                    showFailure();
                } 
                // Continue to next question
                else {
                    loadQuestion();
                }
            }, 1000);
        }

        // Show Failure Message
        function showFailure() {
            document.getElementById("resultTitle").textContent = "I am sorry, traveler...";
            // document.getElementById("resultEmoji").textContent = "🐚";
            document.getElementById("resultMessage").textContent = "but my wisdom may only be shared with those who respect the sea. When you are ready, I will be waiting.";
            showScreen("resultScreen");
        }

        // Show Results
        function showResults() {
            document.getElementById("resultTitle").textContent = "You are worthy. I shall bestow my knowledge.";
            // document.getElementById("resultEmoji").textContent = gameState.selectedPrize.emoji;
            document.getElementById("resultMessage").textContent = gameState.selectedPrize.message;
            showScreen("resultScreen");
        }

        // Screen Management
        function showScreen(screenId) {
            document.querySelectorAll(".screen").forEach(screen => screen.classList.remove("active"));
            document.getElementById(screenId).classList.add("active");
        }

        // Initialize Game
        init();
    </script>
    <div class="footer">
        <a href="https://www.instagram.com/jack_final_final_v2" target="_blank">@jack_final_final_v2</a>
    </div>
</body>
</html>
