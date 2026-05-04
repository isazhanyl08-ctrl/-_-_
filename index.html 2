<!DOCTYPE html>
<html lang="kk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ойлау тасбақасы</title>
    <style>
        :root { --primary: #4CAF50; --locked: #bdc3c7; }
        body { margin: 0; font-family: sans-serif; background: #f0f2f5; display: flex; flex-direction: column; align-items: center; height: 100vh; overflow: hidden; }
        .header { width: 100%; padding: 10px 20px; background: white; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 2px 10px rgba(0,0,0,0.1); box-sizing: border-box; }
        .score-board { font-size: 20px; font-weight: bold; color: #2c3e50; min-width: 120px; text-align: right; }
        #level-screen { position: fixed; inset: 0; background: white; z-index: 200; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 20px; }
        .level-btn { width: 250px; padding: 20px; font-size: 20px; border-radius: 15px; border: none; color: white; cursor: pointer; }
        .active-lvl { background: var(--primary); }
        .locked-lvl { background: var(--locked); cursor: not-allowed; }
        .wheel-container { position: relative; margin-top: 15px; }
        #wheel { width: 300px; height: 300px; border-radius: 50%; border: 8px solid #333; position: relative; transition: transform 3s cubic-bezier(0.1, 0, 0.2, 1); background: conic-gradient(#FF5252 0deg 72deg, #FFEB3B 72deg 144deg, #2196F3 144deg 216deg, #4CAF50 216deg 288deg, #FF9800 288deg 360deg); }
        .wheel-item { position: absolute; top: 50%; left: 50%; margin: -25px; width: 50px; height: 50px; font-size: 40px; display: flex; align-items: center; justify-content: center; }
        .pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent; border-top: 30px solid red; z-index: 10; }
        #congrats-modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 400; flex-direction: column; align-items: center; justify-content: center; color: white; }
        .modal-btn { background: var(--primary); color: white; border: none; padding: 15px 40px; border-radius: 10px; font-size: 22px; cursor: pointer; margin-top: 20px; }
        #fireworks-canvas { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 450; display: none; }
        .game-area { flex: 1; width: 100%; display: flex; flex-direction: column; align-items: center; }
        .card { background: white; padding: 20px; border-radius: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); margin-top: 15px; text-align: center; min-width: 240px; position: relative; }
        .slots { display: flex; gap: 10px; justify-content: center; margin-top: 15px; }
        .slot { width: 50px; height: 55px; border: 3px dashed #bbb; border-radius: 12px; font-size: 30px; font-weight: bold; display: flex; align-items: center; justify-content: center; }
        .tray { width: 100%; background: #dfe6e9; padding: 20px; display: flex; justify-content: center; gap: 12px; flex-wrap: wrap; border-radius: 30px 30px 0 0; position: absolute; bottom: 0; }
        .letter { width: 60px; height: 60px; background: white; border: 3px solid var(--primary); border-radius: 15px; font-size: 28px; font-weight: bold; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        #spin-btn { background: var(--primary); color: white; border: none; padding: 12px 45px; border-radius: 40px; font-size: 22px; font-weight: bold; margin: 15px; cursor: pointer; }
        .toast { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; padding: 30px; border-radius: 20px; border: 4px solid var(--primary); font-size: 30px; font-weight: bold; z-index: 300; }
    </style>
</head>
<body onload="welcomeVoice()">

    <canvas id="fireworks-canvas"></canvas>

    <div id="level-screen">
        <h1>Айналдыр да, сөзді жаз!</h1>
        <button class="level-btn active-lvl" id="lvl1-btn" onclick="startLevel(1)">1-Деңгей</button>
        <button class="level-btn locked-lvl" id="lvl2-btn">2-Деңгей</button>
        <button class="level-btn locked-lvl" id="lvl3-btn">3-Деңгей</button>
    </div>

    <div id="congrats-modal">
        <h1 id="congrats-text" style="font-size: 60px; margin: 0;"></h1>
        <button id="next-lvl-btn" class="modal-btn">Келесі кезең</button>
    </div>

    <div class="header">
        <button style="font-size:24px; border:none; background:none; cursor:pointer;" onclick="goToMenu()">🏠</button>
        <div class="score-board">Ұпай: <span id="score-val">0</span> / 30</div>
    </div>

    <div class="wheel-container">
        <div class="pointer"></div>
        <div id="wheel"></div>
    </div>
    <button id="spin-btn" onclick="spinWheel()">АЙНАЛДЫР!</button>

    <div id="toast" class="toast">Тағы айналдыр</div>

    <div class="game-area" id="game-area" style="display:none;">
        <div class="card" id="active-card"></div>
        <div class="tray" id="letter-tray"></div>
    </div>

    <script>
        const db = [
            {w:"АЮ", i:"🐻", l:1}, {w:"АТ", i:"🐎", l:1}, {w:"АЙ", i:"🌙", l:1}, {w:"ОТ", i:"🔥", l:1}, {w:"СУ", i:"💧", l:1},
            {w:"ҚАР", i:"❄️", l:2}, {w:"СҮТ", i:"🥛", l:2}, {w:"ТАС", i:"🪨", l:2}, {w:"ТАУ", i:"⛰️", l:2}, {w:"НАН", i:"🍞", l:2},
            {w:"АҚША", i:"💵", l:3}, {w:"КІТАП", i:"📚", l:3}, {w:"БАЛЫҚ", i:"🐟", l:3}, {w:"АЛМА", i:"🍎", l:3}, {w:"ШАНА", i:"🛷", l:3}
        ];

        let score = 0;
        let currentLvl = 1;
        let usedInLevel = [];
        let unlockedLevels = [1]; 
        let nextCharIndex = 0;
        const extraChars = "ӘІҢҒҮҰҚӨ";

        // ДАУЫС ФУНКЦИЯСЫ
        function speak(text) {
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'kk-KZ'; // Қазақ тілі
            window.speechSynthesis.speak(utterance);
        }

        function welcomeVoice() {
            // Ойын жүктелгенде сәлемдесу
            setTimeout(() => {
                speak("Айналдыр да, сөзді жаз ойынына қош келдің!");
            }, 1000);
        }

        function goToMenu() {
            document.getElementById('level-screen').style.display = 'flex';
            document.getElementById('game-area').style.display = 'none';
            document.getElementById('congrats-modal').style.display = 'none';
            unlockedLevels.forEach(lvl => unlockLvlUI(lvl));
        }

        function startLevel(lvl) {
            currentLvl = lvl;
            usedInLevel = [];
            document.getElementById('level-screen').style.display = 'none';
            document.getElementById('congrats-modal').style.display = 'none';
            document.getElementById('game-area').style.display = 'none';
            document.getElementById('spin-btn').disabled = false;
            buildWheel();
        }

        function buildWheel() {
            const wheel = document.getElementById('wheel');
            wheel.innerHTML = '';
            const lvlWords = db.filter(item => item.l === currentLvl);
            lvlWords.forEach((item, i) => {
                const el = document.createElement('div');
                el.className = 'wheel-item';
                el.innerText = item.i;
                const angle = (i * 72) + 36;
                el.style.transform = `rotate(${angle}deg) translateY(-110px) rotate(-${angle}deg)`;
                wheel.appendChild(el);
            });
        }

        function spinWheel() {
            document.getElementById('spin-btn').disabled = true;
            const lvlWords = db.filter(item => item.l === currentLvl);
            let available = lvlWords.filter(w => !usedInLevel.includes(w.w));
            let targetWord = available[Math.floor(Math.random() * available.length)];
            const targetIdx = lvlWords.indexOf(targetWord);
            const targetDeg = 360 - (targetIdx * 72 + 36) + (360 * 5);
            
            const wheel = document.getElementById('wheel');
            wheel.style.transform = `rotate(${targetDeg}deg)`;

            setTimeout(() => {
                usedInLevel.push(targetWord.w);
                nextCharIndex = 0;
                showGame(targetWord);
            }, 3100);
        }

        function showGame(wordObj) {
            document.getElementById('game-area').style.display = 'flex';
            const card = document.getElementById('active-card');
            card.innerHTML = `<div id="mark" style="position:absolute; top:-20px; right:-20px; font-size:45px;"></div><div style="font-size: 70px; margin-bottom:10px;">${wordObj.i}</div><div class="slots">${wordObj.w.split('').map(c => `<div class="slot" data-char="${c}"></div>`).join('')}</div>`;
            
            const tray = document.getElementById('letter-tray');
            tray.innerHTML = '';
            let letters = wordObj.w.split('');
            for(let i=0; i<3; i++) letters.push(extraChars[Math.floor(Math.random()*extraChars.length)]);
            letters.sort(() => Math.random() - 0.5).forEach(char => {
                const lEl = document.createElement('div');
                lEl.className = 'letter'; 
                lEl.innerText = char;
                lEl.onclick = () => handleLetterClick(lEl);
                tray.appendChild(lEl);
            });
        }

        function handleLetterClick(el) {
            const char = el.innerText;
            const slots = document.querySelectorAll('.slot');
            const targetSlot = slots[nextCharIndex];

            if (targetSlot && targetSlot.dataset.char === char) {
                targetSlot.innerText = char;
                targetSlot.style.borderStyle = 'solid';
                targetSlot.style.borderColor = 'var(--primary)';
                el.style.visibility = 'hidden'; 
                nextCharIndex++;
                checkWin();
            } else {
                document.getElementById('mark').innerText = '❌';
                setTimeout(() => document.getElementById('mark').innerText = '', 800);
            }
        }

        function checkWin() {
            const slots = document.querySelectorAll('.slot');
            if(nextCharIndex === slots.length) {
                document.getElementById('mark').innerText = '✅';
                score += 5; 
                document.getElementById('score-val').innerText = score;
                setTimeout(() => {
                    if(usedInLevel.length < 2) {
                        const t = document.getElementById('toast'); 
                        t.style.display = 'block';
                        speak("Тағы айналдыр!"); // ДАУЫС
                        setTimeout(() => { 
                            t.style.display = 'none'; 
                            document.getElementById('game-area').style.display = 'none'; 
                            document.getElementById('spin-btn').disabled = false; 
                        }, 2000);
                    } else { showCongrats(); }
                }, 1000);
            }
        }

        function showCongrats() {
            const modal = document.getElementById('congrats-modal');
            const text = document.getElementById('congrats-text');
            const btn = document.getElementById('next-lvl-btn');
            modal.style.display = 'flex';
            btn.style.display = "block";

            if(currentLvl === 1) {
                text.innerText = "Жарайсың🥳";
                speak("Жарайсың!"); // ДАУЫС
                btn.onclick = () => { if(!unlockedLevels.includes(2)) unlockedLevels.push(2); startLevel(2); };
            } else if(currentLvl === 2) {
                text.innerText = "Керемет👏🏻";
                speak("Керемет!"); // ДАУЫС
                btn.onclick = () => { if(!unlockedLevels.includes(3)) unlockedLevels.push(3); startLevel(3); };
            } else {
                text.innerText = "Жеңіс🏆";
                speak("Жеңіс!"); // ДАУЫС
                btn.style.display = "none";
                setTimeout(() => {
                    startFireworks();
                    setTimeout(() => { 
                        canvas.style.display = 'none';
                        goToMenu(); 
                    }, 7000);
                }, 2000);
            }
        }

        function unlockLvlUI(lvl) {
            const btn = document.getElementById('lvl'+lvl+'-btn');
            if(btn) { btn.className = 'level-btn active-lvl'; btn.onclick = () => startLevel(lvl); }
        }

        /* Салют */
        const canvas = document.getElementById('fireworks-canvas');
        const ctx = canvas.getContext('2d');
        let particles = [];
        function startFireworks() { canvas.style.display = 'block'; canvas.width = window.innerWidth; canvas.height = window.innerHeight; animateFireworks(); }
        class Particle {
            constructor(x, y, color) {
                this.x = x; this.y = y; this.color = color;
                this.velocity = { x: (Math.random() - 0.5) * 8, y: (Math.random() - 0.5) * 8 };
                this.alpha = 1;
            }
            draw() { ctx.globalAlpha = this.alpha; ctx.beginPath(); ctx.arc(this.x, this.y, 3, 0, Math.PI * 2); ctx.fillStyle = this.color; ctx.fill(); }
            update() { this.x += this.velocity.x; this.y += this.velocity.y; this.alpha -= 0.01; }
        }
        function animateFireworks() {
            if (canvas.style.display === 'none') return;
            requestAnimationFrame(animateFireworks);
            ctx.fillStyle = 'rgba(0, 0, 0, 0.1)'; ctx.fillRect(0, 0, canvas.width, canvas.height);
            if (Math.random() < 0.1) {
                const x = Math.random() * canvas.width, y = Math.random() * canvas.height;
                const color = `hsl(${Math.random() * 360}, 50%, 50%)`;
                for (let i = 0; i < 30; i++) particles.push(new Particle(x, y, color));
            }
            particles.forEach((p, i) => { if (p.alpha <= 0) particles.splice(i, 1); else { p.update(); p.draw(); } });
        }
    </script>
</body>
</html>
