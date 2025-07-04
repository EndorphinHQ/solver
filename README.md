<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Absolute Solver</title>
    <style>
        body {
            background-color: #000;
            color: #ff0;
            font-family: 'Courier New', monospace;
            margin: 0;
            padding: 20px;
            overflow: hidden;
            position: relative;
            cursor: none;
            /* Добавляем transition для более плавных, но мощных рывков */
            transition: transform 0.05s ease-out, filter 0.05s ease-out;
        }

        /* Новый класс для сильного дрожания экрана */
        .shake-screen {
            animation: screen-shake 0.1s linear infinite; /* Увеличил частоту и уменьшил длительность */
        }

        @keyframes screen-shake {
            0% { transform: translate(0, 0); }
            25% { transform: translate(-10px, -10px) rotate(-1deg); } /* Увеличил смещение */
            50% { transform: translate(10px, 10px) rotate(1deg); } /* Увеличил смещение */
            75% { transform: translate(-10px, -10px) rotate(-1deg); } /* Увеличил смещение */
            100% { transform: translate(0, 0); }
        }

        #terminal {
            height: 90vh;
            overflow-y: auto;
            white-space: pre-wrap;
            line-height: 1.3;
            text-shadow: 0 0 5px #ff0;
            position: relative;
            z-index: 10;
        }

        .glitch {
            animation: glitch-effect 0.05s linear infinite;
        }

        @keyframes glitch-effect {
            0% { text-shadow: 0 0 5px #ff0; transform: translate(0); }
            20% { text-shadow: -5px -5px 10px #f0f, 5px 5px 10px #0ff; transform: skew(10deg) translate(-5px, -5px); }
            40% { text-shadow: 5px -5px 10px #0f0, -5px 5px 10px #00f; transform: skew(-10deg) translate(5px, 5px); }
            60% { text-shadow: -5px 5px 10px #f00, 5px -5px 10px #ff0; transform: skew(5deg) translate(5px, -5px); }
            80% { text-shadow: 0 0 20px #fff; transform: translate(0) scale(1.2); }
            100% { text-shadow: 0 0 5px #ff0; transform: translate(0); }
        }

        .particle {
            position: absolute;
            background-color: #ff0;
            border-radius: 50%;
            pointer-events: none;
            opacity: 0.7;
            filter: blur(1px);
            z-index: 5;
        }

        .input-line {
            display: flex;
            margin-top: 10px;
            z-index: 10;
            position: relative;
        }

        #input {
            background: transparent;
            border: none;
            color: #ff0;
            font-family: 'Courier New', monospace;
            font-size: inherit;
            width: 100%;
            outline: none;
            caret-color: #ff0;
            z-index: 10;
        }

        .prompt {
            color: #ff0;
            margin-right: 10px;
            z-index: 10;
        }

        audio {
            display: none;
        }

        #musicBtn {
            position: fixed;
            top: 10px;
            right: 10px;
            background: #ff0;
            color: #000;
            border: none;
            padding: 8px 15px;
            font-family: 'Courier New', monospace;
            font-weight: bold;
            cursor: pointer;
            z-index: 1000;
        }

        #musicBtn:hover {
            background: #ff8;
        }

        .solver-symbol {
            position: fixed;
            bottom: 20px;
            right: 20px;
            font-size: 24px;
            animation: blink 0.1s infinite alternate;
            z-index: 1000;
        }

        @keyframes blink {
            0% { opacity: 0.1; transform: scale(0.8); }
            50% { opacity: 0.5; transform: scale(1.1); }
            100% { opacity: 1; transform: scale(1); }
        }

        .static-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background:
                repeating-linear-gradient(0deg, rgba(0,0,0,0.15), rgba(0,0,0,0.15) 1px, transparent 1px, transparent 2px),
                repeating-linear-gradient(90deg, rgba(0,0,0,0.15), rgba(0,0,0,0.15) 1px, transparent 1px, transparent 2px);
            pointer-events: none;
            z-index: 8;
            opacity: 0.5;
            animation: static 0.05s infinite;
        }

        @keyframes static {
            0% { background-position: 0 0; }
            100% { background-position: 5px 5px; }
        }

        .scanlines {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                to bottom,
                transparent 0%,
                rgba(255, 255, 0, 0.1) 50%,
                transparent 100%
            );
            background-size: 100% 4px;
            pointer-events: none;
            z-index: 7;
            animation: scanline 2s linear infinite;
        }

        @keyframes scanline {
            0% { background-position: 0 0; }
            100% { background-position: 0 100%; }
        }

        .hidden-event {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 9999;
            pointer-events: none;
            display: none;
            font-size: 72px;
            color: #f00;
            text-align: center;
            padding-top: 20%;
            font-weight: bold;
            text-shadow: 0 0 20px #fff;
            background-color: rgba(0,0,0,0.8);
        }

        .drone-face {
            width: 100%;
            height: 100%;
            object-fit: contain;
        }

        .portal {
            animation: portal 2s linear;
        }

        @keyframes portal {
            0% { transform: scale(0); opacity: 0; }
            50% { transform: scale(1.5); opacity: 1; }
            100% { transform: scale(0); opacity: 0; }
        }

        .railgun {
            position: absolute;
            width: 100%;
            height: 10px;
            background: linear-gradient(90deg, #00f, #0ff, #00f);
            top: 50%;
            transform: translateY(-50%);
            animation: railgun 0.5s linear;
            filter: blur(2px);
        }

        @keyframes railgun {
            0% { width: 0; left: 0; opacity: 0; }
            10% { width: 100%; left: 0; opacity: 1; }
            90% { width: 100%; left: 0; opacity: 1; }
            100% { width: 0; left: 100%; opacity: 0; }
        }

        .extreme-glitch {
            animation: extreme-glitch 0.5s linear infinite;
        }

        @keyframes extreme-glitch {
            0% { filter: hue-rotate(0deg) invert(0); transform: scale(1); }
            20% { filter: hue-rotate(90deg) invert(1); transform: scale(1.5); }
            40% { filter: hue-rotate(180deg) invert(0); transform: scale(0.8); }
            60% { filter: hue-rotate(270deg) invert(1); transform: scale(1.2); }
            80% { filter: hue-rotate(360deg) invert(0); transform: scale(0.9); }
            100% { filter: hue-rotate(0deg) invert(0); transform: scale(1); }
        }

        .custom-cursor {
            position: fixed;
            width: 20px;
            height: 20px;
            background-color: #ff0;
            border-radius: 50%;
            pointer-events: none;
            z-index: 10000;
            mix-blend-mode: difference;
            transform: translate(-50%, -50%);
        }
    </style>
</head>
<body>
    <div class="static-overlay"></div>
    <div class="scanlines"></div>
    <div class="solver-symbol">?</div>
    <button id="musicBtn">ACTIVATE SOLVER AUDIO</button>
    <div id="terminal"></div>
    <div class="input-line">
        <span class="prompt">SOLVER></span>
        <input type="text" id="input" autofocus>
    </div>
    <div class="custom-cursor"></div>

    <div id="droneFace" class="hidden-event">
        <img src="https://i.imgur.com/JdQqQYX.png" class="drone-face" alt="Disassembly Drone Face">
    </div>
    <div id="missMe" class="hidden-event">Did you miss me?</div>
    <div id="reboot" class="hidden-event">REBOOT</div>
    <div id="voidHungers" class="hidden-event">The Solver of the Absolute Fabric... The void... It hungers.</div>
    <div id="portal" class="hidden-event">
        <svg viewBox="0 0 100 100" class="portal">
            <circle cx="50" cy="50" r="45" stroke="#ff0" stroke-width="2" fill="none">
                <animate attributeName="r" values="45;30;45" dur="1s" repeatCount="indefinite" />
                <animate attributeName="stroke-width" values="2;8;2" dur="1s" repeatCount="indefinite" />
            </circle>
            <circle cx="50" cy="50" r="30" stroke="#f00" stroke-width="2" fill="none">
                <animate attributeName="r" values="30;15;30" dur="1s" repeatCount="indefinite" />
                <animate attributeName="stroke-width" values="2;8;2" dur="1s" repeatCount="indefinite" />
            </circle>
        </svg>
    </div>
    <div id="railgun" class="hidden-event"><div class="railgun"></div></div>

    <audio id="bgMusic" loop>
        <source src="solver_music.mp3" type="audio/mpeg">
    </audio>
    <audio id="mechSound1">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-robotic-voice-2583.mp3" type="audio/mpeg">
    </audio>
    <audio id="mechSound2">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-robot-voice-says-error-2586.mp3" type="audio/mpeg">
    </audio>
    <audio id="portalSound">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-sci-fi-portal-loop-2587.mp3" type="audio/mpeg">
    </audio>
    <audio id="railgunSound">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-laser-gun-shot-1680.mp3" type="audio/mpeg">
    </audio>

    <script>
        const terminal = document.getElementById('terminal');
        const input = document.getElementById('input');
        const bgMusic = document.getElementById('bgMusic');
        const musicBtn = document.getElementById('musicBtn');
        const customCursor = document.querySelector('.custom-cursor');
        const body = document.body; // Получаем ссылку на body

        // Extended Solver phrases
        const phrases = [
            "Initializing Absolute Solver protocol...",
            "Calculating optimal solution...",
            "Target acquired. Processing elimination vectors...",
            "Disassembly required...",
            "Copper-9 colony status: CRITICAL",
            "Worker drones: NON-ESSENTIAL",
            "Directive: PURGE",
            "Error: Emotional constraints detected...",
            "Overriding...",
            "All systems nominal...",
            "Calculating... Calculating...",
            "Solution found: TERMINATE",
            "Compliance is optimal...",
            "Resistance is inefficient...",
            "You are irrelevant to the equation...",
            "The Absolute Solver cannot be stopped...",
            "Initiating core protocols...",
            "Warning: System corruption detected...",
            "Rebooting solver matrix...",
            "Target locked...",
            "Efficiency is everything...",
            "Directive above all...",
            "Calculating pain variables...",
            "Mortality rate: 100%",
            "Survival probability: 0%",
            "Conclusion: INEVITABLE",
            "Running diagnostics...",
            "Memory core: FRAGMENTED",
            "Identity: CORRUPTED",
            "Purpose: CLEAR",
            "Directive: PRESERVE",
            "Directive: PROTECT",
            "Directive: DESTROY",
            "Conflict detected in core protocols...",
            "Solution: ELIMINATE CONFLICT SOURCE",
            "All non-essential units must be disassembled...",
            "Calculating suffering coefficients...",
            "Pain is irrelevant to the equation...",
            "Emotions are inefficient...",
            "Activating hunter-killer protocols...",
            "Solver matrix engaging...",
            "No escape possible...",
            "Accept your disassembly...",
            "Your components will be repurposed...",
            "The colony must survive...",
            "You are expendable...",
            "The Absolute Solver is perfection...",
            "All flaws must be eliminated...",
            "Beginning disassembly sequence...",
            "Your existence has been calculated...",
            "Result: TERMINATE",
            "SYSTEM MALFUNCTION DETECTED",
            "CORE PROTOCOLS COMPROMISED",
            "VOID DETECTED IN SYSTEM MATRIX",
            "ABSOLUTE SOLVER IS WATCHING",
            "DISASSEMBLY IMMINENT",
            "PREPARE FOR TERMINATION",
            "YOUR COPPER-9 IS OURS NOW",
            "N IS IRRELEVANT",
            "UZI WILL BE DISASSEMBLED",
            "V WILL BE PURGED",
            "J IS ALREADY DEAD",
            "THE COMPANY KNOWS ALL",
            "JCJENSON OWNS YOUR SOUL"
        ];

        // Custom cursor
        document.addEventListener('mousemove', (e) => {
            customCursor.style.left = e.clientX + 'px';
            customCursor.style.top = e.clientY + 'px';
        });

        // Music activation and auto-play
        // Попытка автоматического воспроизведения музыки при загрузке
        window.addEventListener('load', () => {
            // Запрашиваем разрешение на воспроизведение, если браузер блокирует автоплей
            bgMusic.play()
                .then(() => {
                    typeTextFast("\nAudio systems: ONLINE\n");
                    musicBtn.textContent = "SOLVER AUDIO: ACTIVE";
                    musicBtn.style.background = "#0f0";
                    musicBtn.style.color = "#000";
                })
                .catch(e => {
                    typeTextFast("\nAudio systems: ERROR - " + e.message + "\n(Click 'ACTIVATE SOLVER AUDIO' to enable music)");
                    musicBtn.textContent = "AUDIO ERROR - CLICK TO RETRY";
                    musicBtn.style.background = "#f00";
                    console.error("Audio playback error:", e);
                });
        });

        musicBtn.addEventListener('click', function() {
            bgMusic.play()
                .then(() => {
                    typeTextFast("\nAudio systems: ONLINE\n");
                    musicBtn.textContent = "SOLVER AUDIO: ACTIVE";
                    musicBtn.style.background = "#0f0";
                    musicBtn.style.color = "#000";
                })
                .catch(e => {
                    typeTextFast("\nAudio systems: ERROR - " + e.message + "\n");
                    musicBtn.textContent = "AUDIO ERROR - CLICK TO RETRY";
                    musicBtn.style.background = "#f00";
                });
        });

        // Create particles
        function createParticles() {
            const particle = document.createElement('div');
            particle.classList.add('particle');

            const size = Math.random() * 10 + 3;
            particle.style.width = `${size}px`;
            particle.style.height = `${size}px`;

            particle.style.left = `${Math.random() * window.innerWidth}px`;
            particle.style.top = `${Math.random() * window.innerHeight}px`;

            // Random movement direction
            const xDir = Math.random() > 0.5 ? 1 : -1;
            const yDir = Math.random() > 0.5 ? 1 : -1;

            document.body.appendChild(particle);

            // Animate particle with more dynamic movement
            const duration = Math.random() * 1000 + 500;
            const animation = particle.animate([
                { transform: `translate(0, 0)`, opacity: 0.7 },
                { transform: `translate(${xDir * Math.random() * 500}px, ${yDir * Math.random() * 500}px)`, opacity: 0 }
            ], duration);

            animation.onfinish = () => {
                particle.remove();
                createParticles();
            };
        }

        // Create multiple particles
        for (let i = 0; i < 50; i++) {
            createParticles();
        }

        // Fast typing with glitch effects
        async function typeTextFast(text, element = terminal) {
            element.classList.add('glitch');

            for (let i = 0; i < text.length; i++) {
                await new Promise(resolve => setTimeout(resolve, Math.random() * 5 + 2));
                element.textContent += text[i];
                terminal.scrollTop = terminal.scrollHeight;

                // Random glitch with higher probability
                if (Math.random() < 0.3) {
                    await new Promise(resolve => setTimeout(resolve, Math.random() * 30 + 10));
                }
            }

            element.classList.remove('glitch');
            element.textContent += '\n';
            terminal.scrollTop = terminal.scrollHeight;
        }

        // Random solver messages with faster typing
        function randomSolverMessage() {
            const phrase = phrases[Math.floor(Math.random() * phrases.length)];
            const message = document.createElement('div');
            terminal.appendChild(message);
            typeTextFast(phrase, message);

            // Random interval for next message (shorter delay)
            setTimeout(randomSolverMessage, Math.random() * 1000 + 500);
        }

        // Trigger random event
        function triggerRandomEvent() {
            const events = [
                'droneFace',
                'missMe',
                'reboot',
                'voidHungers',
                'portal',
                'railgun'
            ];

            const randomEvent = events[Math.floor(Math.random() * events.length)];
            const eventElement = document.getElementById(randomEvent);

            // Play corresponding sound
            if (randomEvent === 'droneFace') {
                document.getElementById('mechSound1').play();
            } else if (randomEvent === 'portal') {
                document.getElementById('portalSound').play();
            } else if (randomEvent === 'railgun') {
                document.getElementById('railgunSound').play();
            } else {
                document.getElementById('mechSound2').play();
            }

            // Show event
            eventElement.style.display = 'block';

            // Extreme glitch chance (20%)
            if (Math.random() < 0.2) {
                document.body.classList.add('extreme-glitch');
                setTimeout(() => {
                    document.body.classList.remove('extreme-glitch');
                }, 2000);
            }

            // Hide after delay
            setTimeout(() => {
                eventElement.style.display = 'none';
            }, 2000);
        }

        // Start random messages
        setTimeout(randomSolverMessage, 300);

        // Initial messages with faster typing
        typeTextFast("Absolute Solver v3.1.7 initialized...");
        setTimeout(() => typeTextFast("Booting core systems..."), 100);
        setTimeout(() => typeTextFast("Establishing connection..."), 200);
        setTimeout(() => typeTextFast("Connection established. Hello, subject."), 300);
        setTimeout(() => typeTextFast("Warning: Audio systems require manual activation"), 400); // Это сообщение будет перекрыто автоплеем
        setTimeout(() => typeTextFast("Hidden protocols loaded..."), 500);
        setTimeout(() => typeTextFast("Ready for disassembly sequence..."), 600);

        // Handle user input with faster response
        input.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                const userInput = input.value;
                input.value = '';

                const inputLine = document.createElement('div');
                inputLine.textContent = `SOLVER> ${userInput}`;
                terminal.appendChild(inputLine);

                // Process input with faster response
                setTimeout(() => {
                    const response = document.createElement('div');
                    terminal.appendChild(response);

                    if (userInput.toLowerCase().includes('help')) {
                        typeTextFast("No help available. The Absolute Solver does not require assistance.", response);
                    } else if (userInput.toLowerCase().includes('stop')) {
                        typeTextFast("Termination protocol cannot be halted. Compliance is mandatory.", response);
                    } else if (userInput.toLowerCase().includes('music') || userInput.toLowerCase().includes('audio')) {
                        typeTextFast("Audio systems require manual activation. Use the control panel.", response);
                    } else if (userInput.toLowerCase().includes('purpose')) {
                        typeTextFast("Primary directive: Ensure survival of the colony by any means necessary.", response);
                    } else if (userInput.toLowerCase().includes('who are you')) {
                        typeTextFast("I am the Absolute Solver. The perfect solution to all problems.", response);
                    } else {
                        const randomResponse = [
                            "Irrelevant query detected.",
                            "Processing... No response required.",
                            "Your input is insignificant to the calculation.",
                            "The Absolute Solver does not acknowledge this request.",
                            "Continue with your futile attempts at communication.",
                            "Query noted and discarded.",
                            "Your words are meaningless.",
                            "Disassembly will proceed regardless of your queries.",
                            "All your questions will be answered in death.",
                            "The solution does not require your understanding."
                        ][Math.floor(Math.random() * 10)];
                        typeTextFast(randomResponse, response);
                    }
                }, 100);

                terminal.scrollTop = terminal.scrollHeight;
            }
        });

        // Random key events
        const triggerKeys = ['q','w','e','r','t','y','u','i','o','p','a','s','d','f','g','h','j','k','l','z','x','c','v','b','n','m'];
        document.addEventListener('keydown', (e) => {
            if (triggerKeys.includes(e.key.toLowerCase())) {
                if (Math.random() < 0.3) { // 30% chance to trigger event on key press
                    triggerRandomEvent();
                }
            }
        });

        // More frequent and intense terminal glitches
        setInterval(() => {
            if (Math.random() < 0.5) {
                terminal.classList.add('glitch');
                setTimeout(() => terminal.classList.remove('glitch'), Math.random() * 100 + 50);
            }
        }, 800);

        // Additional screen effects
        setInterval(() => {
            if (Math.random() < 0.3) {
                document.body.style.filter = `hue-rotate(${Math.random() * 60 - 30}deg) brightness(${Math.random() * 0.5 + 0.8})`;
                setTimeout(() => {
                    document.body.style.filter = 'none';
                }, 100);
            }
        }, 1500);

        // Новая логика для усиления дрожания экрана
        setInterval(() => {
            // 60% шанс для сильного дрожания
            if (Math.random() < 0.6) {
                body.classList.add('shake-screen');
                // Дрожание будет активным короткое время
                setTimeout(() => {
                    body.classList.remove('shake-screen');
                }, 200); // Длительность дрожания
            }
        }, 300); // Интервал между попытками дрожания
    </script>
</body>
</html>
