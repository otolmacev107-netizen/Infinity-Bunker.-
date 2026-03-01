<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bunker Infinite • Постапокалипсис</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', 'Courier New', monospace;
        }

        body {
            min-height: 100vh;
            background: radial-gradient(circle at 20% 30%, #1a2a1f, #0a0f0a);
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
            position: relative;
            overflow-x: hidden;
        }

        /* Анимированный фон с частицами (радиация/пыль) */
        .dust-particles {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }

        .particle {
            position: absolute;
            background: rgba(180, 255, 120, 0.1);
            border-radius: 50%;
            filter: blur(2px);
            animation: float 20s infinite linear;
        }

        @keyframes float {
            0% { transform: translateY(0) translateX(0); opacity: 0; }
            10% { opacity: 0.5; }
            90% { opacity: 0.3; }
            100% { transform: translateY(-100vh) translateX(20px); opacity: 0; }
        }

        /* Ржавый контейнер с эффектом металла */
        .game-container {
            position: relative;
            z-index: 10;
            max-width: 1400px;
            width: 100%;
            background: rgba(20, 25, 18, 0.85);
            backdrop-filter: blur(12px);
            border: 3px solid #7c6e4f;
            border-radius: 32px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.8), 0 0 0 2px #4a3e2b inset, 0 0 20px #5f8b4b33;
            padding: 24px;
            transition: all 0.3s;
        }

        /* Верхняя панель с индикаторами */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 20px;
            margin-bottom: 24px;
            padding: 0 12px;
        }

        .logo {
            font-size: 2.5rem;
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 6px;
            background: linear-gradient(135deg, #b8ff9e, #d9b650);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 0 15px #8bc34a, 2px 2px 0 #2f4b2f;
        }

        .bunker-status {
            display: flex;
            gap: 20px;
            background: #1e261b;
            padding: 12px 24px;
            border-radius: 60px;
            border: 1px solid #6f8c5d;
            box-shadow: inset 0 2px 5px #00000066;
            color: #b9c9ae;
            font-weight: 600;
        }

        .status-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .led {
            width: 14px;
            height: 14px;
            border-radius: 50%;
            background: #4caf50;
            box-shadow: 0 0 10px #8bc34a;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }

        /* Панель управления */
        .control-panel {
            display: flex;
            gap: 16px;
            flex-wrap: wrap;
            justify-content: center;
            margin-bottom: 32px;
        }

        .btn {
            background: #2c3527;
            border: 2px solid #786e48;
            color: #efe6cc;
            font-size: 1.2rem;
            font-weight: bold;
            padding: 14px 30px;
            border-radius: 60px;
            cursor: pointer;
            box-shadow: 0 8px 0 #1a2417, 0 10px 20px black;
            transition: 0.1s linear;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .btn:active {
            transform: translateY(7px);
            box-shadow: 0 1px 0 #1a2417, 0 5px 12px black;
        }

        .btn:disabled {
            opacity: 0.4;
            transform: translateY(4px);
            box-shadow: 0 4px 0 #1a2417;
            pointer-events: none;
        }

        .btn-secondary {
            background: #3d3523;
            border-color: #ad9f78;
            box-shadow: 0 5px 0 #2a2419;
            font-size: 1rem;
            padding: 10px 20px;
        }

        /* Звуковой индикатор */
        .sound-toggle {
            background: none;
            border: 2px solid #7c9a6e;
            border-radius: 40px;
            width: 50px;
            height: 50px;
            font-size: 1.8rem;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            color: #d4dba5;
            transition: 0.2s;
        }

        .sound-toggle.off {
            opacity: 0.5;
            border-color: #774545;
        }

        /* Игровое поле — карточки */
        .game-board {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 24px;
            margin-bottom: 32px;
        }

        .player-card {
            background: #282e1f;
            border: 3px solid #635b3c;
            border-radius: 28px;
            padding: 16px;
            box-shadow: 0 10px 0 #1a1f13, inset 0 0 30px #2b3927;
            transition: transform 0.2s;
            color: #eedbba;
            position: relative;
            overflow: hidden;
        }

        .player-card::before {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 6px;
            background: linear-gradient(90deg, #a59f6e, #5d8042, #a59f6e);
        }

        .player-card.eliminated {
            opacity: 0.6;
            filter: grayscale(0.6);
            border-color: #5f4b4b;
        }

        .player-card.eliminated::before {
            background: #6b4f4f;
        }

        .player-name {
            font-size: 1.5rem;
            font-weight: 700;
            margin-bottom: 8px;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px dashed #788b5b;
            padding-bottom: 6px;
        }

        .badge {
            background: #50653b;
            padding: 4px 10px;
            border-radius: 30px;
            font-size: 0.8rem;
            color: #e4efcd;
        }

        .characteristic {
            background: #1e261bb0;
            border-left: 4px solid #9fbb7a;
            padding: 8px 12px;
            margin: 10px 0;
            border-radius: 12px;
            font-size: 0.95rem;
            box-shadow: inset 0 0 8px #0000005e;
        }

        .card-footer {
            display: flex;
            justify-content: space-between;
            margin-top: 14px;
            color: #93a57c;
            font-weight: 600;
        }

        .vote-btn {
            background: #3c5033;
            border: none;
            color: white;
            padding: 8px 18px;
            border-radius: 40px;
            font-weight: bold;
            cursor: pointer;
            border-bottom: 3px solid #1d3117;
            transition: 0.1s;
        }

        .vote-btn:active {
            border-bottom-width: 1px;
            transform: translateY(2px);
        }

        .vote-btn:disabled {
            opacity: 0.3;
            pointer-events: none;
        }

        /* Область действий и лога */
        .action-area {
            display: flex;
            flex-wrap: wrap;
            gap: 30px;
            margin-top: 20px;
            padding: 16px;
            background: #1a2017;
            border-radius: 60px;
            border: 1px solid #777a4e;
            align-items: center;
            justify-content: space-between;
        }

        .round-info {
            font-size: 1.3rem;
            background: #00000047;
            padding: 10px 28px;
            border-radius: 40px;
            border: 1px solid #6d854a;
            color: #e3eec3;
        }

        .log-messages {
            flex: 1;
            color: #bacc9b;
            font-size: 1rem;
            min-width: 200px;
            font-style: italic;
            padding: 8px 20px;
            background: #202618;
            border-radius: 30px;
        }

        /* Карточка бункера (особая) */
        .bunker-card {
            background: #1f2d1c;
            border: 4px double #dbbc6c;
            border-radius: 40px;
            padding: 12px 20px;
            text-align: center;
            max-width: 300px;
            margin: 20px auto;
            font-weight: bold;
            font-size: 1.2rem;
            box-shadow: 0 0 20px #93aa6e;
            color: #f7efcf;
        }

        /* Адаптация */
        @media (max-width: 700px) {
            .game-board {
                grid-template-columns: 1fr;
            }
            .header {
                flex-direction: column;
            }
        }

        /* Модалка голосования */
        .vote-modal {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #0c0f0ad9;
            backdrop-filter: blur(5px);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 200;
        }

        .modal-content {
            background: #22281d;
            border: 4px solid #9f914c;
            border-radius: 60px;
            padding: 30px;
            width: 400px;
            box-shadow: 0 20px 30px black;
        }

        .modal-content select, .modal-content button {
            width: 100%;
            padding: 15px;
            margin: 12px 0;
            border-radius: 40px;
            background: #2b3a25;
            color: white;
            border: 2px solid #8b7b4a;
            font-size: 1.2rem;
        }
    </style>
</head>
<body>
    <!-- Частицы пыли -->
    <div class="dust-particles" id="dust"></div>

    <div class="game-container">
        <!-- Шапка -->
        <div class="header">
            <div class="logo">БУНКЕР INFINITE</div>
            <div class="bunker-status">
                <span class="status-item"><span class="led"></span> Кислород 97%</span>
                <span class="status-item"><span class="led"></span> Герметичность OK</span>
            </div>
            <div class="sound-toggle" id="soundToggle">🔊</div>
        </div>

        <!-- Панель кнопок -->
        <div class="control-panel">
            <button class="btn" id="newGameBtn">🔄 Новая игра</button>
            <button class="btn btn-secondary" id="nextRoundBtn" disabled>⏩ Следующий раунд</button>
            <button class="btn btn-secondary" id="voteOpenBtn" disabled>🗳️ Голосовать</button>
        </div>

        <!-- Карточка текущего бункера -->
        <div class="bunker-card" id="bunkerCard">
            ⚠️ Бункер "Ковчег" | Запасы: 720 дней
        </div>

        <!-- Игровое поле с игроками -->
        <div class="game-board" id="gameBoard"></div>

        <!-- Нижняя панель с логом и раундом -->
        <div class="action-area">
            <div class="round-info" id="roundDisplay">Раунд 1 / Фаза: открытие</div>
            <div class="log-messages" id="logMessage">🔄 Нажми 'Новая игра', чтобы начать</div>
        </div>
    </div>

    <!-- Модалка голосования (скрыта изначально) -->
    <div id="voteModal" class="vote-modal" style="display: none;">
        <div class="modal-content">
            <h2 style="color:#e2dda2;">🔒 Анонимное голосование</h2>
            <p style="color:#b0bb8f;">Кого исключить?</p>
            <select id="voteSelect"></select>
            <button id="submitVote">Проголосовать</button>
            <button id="closeModal" style="background: #4f4233;">Отмена</button>
        </div>
    </div>

    <script>
        (function() {
            // ------------------------ Звуковые эффекты (Web Audio API) ------------------------
            const AudioContext = window.AudioContext || window.webkitAudioContext;
            let audioCtx = null;
            let soundEnabled = true;

            // Инициализация аудио только после первого взаимодействия (политика браузеров)
            function initAudio() {
                if (audioCtx) return;
                audioCtx = new AudioContext();
            }

            // Воспроизведение звука (короткий гудок/щелчок)
            function playSound(type) {
                if (!soundEnabled || !audioCtx || audioCtx.state === 'closed') return;
                if (audioCtx.state === 'suspended') {
                    audioCtx.resume().then(() => playSound(type));
                    return;
                }
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                
                if (type === 'click') {
                    osc.frequency.value = 600;
                    gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.1);
                } else if (type === 'vote') {
                    osc.frequency.value = 400;
                    gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.15);
                } else if (type === 'eliminate') {
                    osc.frequency.value = 200;
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.5);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.5);
                }
            }

            // ------------------------ Генерация частиц фона ------------------------
            function createParticles() {
                const dustDiv = document.getElementById('dust');
                for (let i = 0; i < 40; i++) {
                    const p = document.createElement('div');
                    p.className = 'particle';
                    const size = Math.random() * 5 + 2;
                    p.style.width = size + 'px';
                    p.style.height = size + 'px';
                    p.style.left = Math.random() * 100 + '%';
                    p.style.top = Math.random() * 100 + '%';
                    p.style.animationDelay = Math.random() * 10 + 's';
                    p.style.animationDuration = 15 + Math.random() * 20 + 's';
                    dustDiv.appendChild(p);
                }
            }
            createParticles();

            // ------------------------ Данные игры ------------------------
            let players = [];
            let currentRound = 1;
            const maxRounds = 5;
            let gameActive = false;
            let voteModalActive = false;
            let roundPhase = 'open'; // open / vote / end

            // Список возможных карт (профессии и т.д. - сокращенный, но имитация >1000 комбинаций)
            const professions = [
                "Инженер-ядерщик", "Агроном", "Хирург", "Сварщик", "Учитель", "Солдат", "Шахтер",
                "Биолог-генетик", "Повар", "Электрик", "Слесарь", "Пилот", "Капитан дальнего плавания",
                "Психолог", "Фармацевт", "Ветеринар", "Архитектор", "Программист", "Химик-технолог",
                "Водолаз", "Геолог", "Метеоролог", "Стоматолог", "Фельдшер", "Охранник", "Пожарный",
                "Лесник", "Охотник", "Рыбак", "Садовод", "Пчеловод", "Кузнец", "Столяр", "Гончар"
            ];
            const bios = ["Мужчина, 24", "Женщина, 31", "Мужчина, 45", "Женщина, 19", "Мужчина, 57", "Женщина, 62"];
            const healths = ["Здоров", "Диабет", "Астма", "Гипертония", "Аллергия", "Иммунитет к вирусу", "Хронический бронхит"];
            const hobbys = ["Стрельба из лука", "Выживание", "Медицина", "Садоводство", "Рыбалка", "Шахматы", "Книги по инженерии"];
            const bagages = ["Набор инструментов", "Аптечка", "Семена", "Топор", "Генератор", "Рация", "Фильтр для воды", "Палатка"];
            const facts = ["Служил в спецназе", "Выжил в лавине", "Имеет тайную связь с учеными", "Паникует в замкнутом пространстве", "Готовил убежище 5 лет"];
            const phobias = ["Клаустрофобия", "Боязнь темноты", "Акрофобия", "Нет фобий", "Боязнь крови"];

            // Генерация случайного игрока
            function generatePlayer(id) {
                return {
                    id: id,
                    name: `Выживший ${id}`,
                    profession: professions[Math.floor(Math.random() * professions.length)] + " " + (Math.floor(Math.random()*50)+1),
                    bio: bios[Math.floor(Math.random() * bios.length)] + (Math.random()>0.5 ? " атлетический" : " хрупкий"),
                    health: healths[Math.floor(Math.random() * healths.length)] + (Math.random()>0.7 ? " (скрыто)" : ""),
                    hobby: hobbys[Math.floor(Math.random() * hobbys.length)] + " ур. " + (Math.floor(Math.random()*5)+1),
                    baggage: bagages[Math.floor(Math.random() * bagages.length)] + " +" + (Math.floor(Math.random()*3)),
                    fact: facts[Math.floor(Math.random() * facts.length)],
                    phobia: phobias[Math.floor(Math.random() * phobias.length)],
                    eliminated: false,
                    voteCount: 0
                };
            }

            // Функция обновления интерфейса
            function renderBoard() {
                const board = document.getElementById('gameBoard');
                board.innerHTML = '';
                players.forEach(p => {
                    const card = document.createElement('div');
                    card.className = `player-card ${p.eliminated ? 'eliminated' : ''}`;
                    card.innerHTML = `
                        <div class="player-name">
                            ${p.name}
                            <span class="badge">#${p.id}</span>
                        </div>
                        <div class="characteristic">⚙️ ${p.profession}</div>
                        <div class="characteristic">🧬 ${p.bio}</div>
                        <div class="characteristic">❤️ ${p.health}</div>
                        <div class="characteristic">🎨 ${p.hobby}</div>
                        <div class="characteristic">🎒 ${p.baggage}</div>
                        <div class="characteristic">📜 ${p.fact}</div>
                        <div class="characteristic">👁️ ${p.phobia}</div>
                        <div class="card-footer">
                            <span>💀 ${p.eliminated ? 'Изгнан' : 'В бункере'}</span>
                            ${!p.eliminated && gameActive && roundPhase === 'vote' ? `<button class="vote-btn" data-id="${p.id}">🗳️ Выбрать</button>` : ''}
                        </div>
                    `;
                    board.appendChild(card);
                });

                // Навесить события на кнопки голосования
                document.querySelectorAll('.vote-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        if (!gameActive || roundPhase !== 'vote') return;
                        const targetId = parseInt(btn.dataset.id);
                        openVoteModal(targetId);
                    });
                });
            }

            // Обновить лог
            function setLog(text) {
                document.getElementById('logMessage').innerText = '📟 ' + text;
            }

            // Обновить раунд
            function updateRoundInfo() {
                const phaseText = roundPhase === 'open' ? 'открытие' : (roundPhase === 'vote' ? 'голосование' : 'завершение');
                document.getElementById('roundDisplay').innerText = `Раунд ${currentRound} / ${phaseText}`;
            }

            // Функция начала игры
            function startNewGame() {
                initAudio(); // первая попытка создать контекст
                playSound('click');
                players = [];
                const playerCount = 5; // для демо 5 игроков
                for (let i = 1; i <= playerCount; i++) {
                    players.push(generatePlayer(i));
                }
                currentRound = 1;
                roundPhase = 'open';
                gameActive = true;
                document.getElementById('newGameBtn').disabled = false;
                document.getElementById('nextRoundBtn').disabled = false;
                document.getElementById('voteOpenBtn').disabled = false;
                setLog('Новая игра. Открывайте карты характеристик!');
                // Случайная карта бункера
                const bunkerVariants = ["Убежище 'Заря'", "Бункер 'Скала'", "Подземный город 'Надежда'", "Старая шахта", "Бункер 'Ковчег'"];
                document.getElementById('bunkerCard').innerHTML = `⚠️ ${bunkerVariants[Math.floor(Math.random()*bunkerVariants.length)]} | Запас: ${Math.floor(Math.random()*900+300)} дней`;
                renderBoard();
                updateRoundInfo();
            }

            // Открыть модалку голосования
            function openVoteModal(exceptId = null) {
                if (!gameActive || roundPhase !== 'vote') return;
                const modal = document.getElementById('voteModal');
                const select = document.getElementById('voteSelect');
                select.innerHTML = '';
                players.filter(p => !p.eliminated && p.id !== exceptId).forEach(p => {
                    const opt = document.createElement('option');
                    opt.value = p.id;
                    opt.textContent = `${p.name} — ${p.profession.substring(0,20)}`;
                    select.appendChild(opt);
                });
                if (select.options.length === 0) {
                    setLog('Некого исключать!');
                    return;
                }
                modal.style.display = 'flex';
                voteModalActive = true;

                // Сохраняем выбранный ID для логики (можем учесть при желании)
                window.currentVoteExclude = exceptId;
            }

            // Закрыть модалку
            function closeVoteModal() {
                document.getElementById('voteModal').style.display = 'none';
                voteModalActive = false;
            }

            // Обработка голоса
            function submitVote() {
                if (!gameActive || roundPhase !== 'vote') return;
                const select = document.getElementById('voteSelect');
                const selectedId = parseInt(select.value);
                if (!selectedId) return;

                playSound('vote');
                // Исключаем игрока (упрощённо: сразу исключаем, анонимность не моделируем для демо)
                const target = players.find(p => p.id === selectedId);
                if (target && !target.eliminated) {
                    target.eliminated = true;
                    setLog(`🗳️ Игрок ${target.name} исключён большинством голосов.`);
                    playSound('eliminate');
                }

                closeVoteModal();
                // Переход к следующему раунду или финал
                if (players.filter(p => !p.eliminated).length <= 1 || currentRound >= maxRounds) {
                    // Финал игры
                    gameActive = false;
                    document.getElementById('nextRoundBtn').disabled = true;
                    document.getElementById('voteOpenBtn').disabled = true;
                    const survivors = players.filter(p => !p.eliminated).map(p => p.name).join(', ');
                    setLog(`🏁 Апокалипсис пережили: ${survivors || 'никто...'}`);
                } else {
                    currentRound++;
                    roundPhase = 'open';
                }
                renderBoard();
                updateRoundInfo();
            }

            // Обработка кнопки "Следующий раунд" (переключение фазы открытия)
            function nextRound() {
                if (!gameActive) return;
                playSound('click');
                if (roundPhase === 'open') {
                    roundPhase = 'vote';
                    setLog('Раунд открыт! Теперь можно голосовать.');
                } else if (roundPhase === 'vote') {
                    setLog('Голосование уже активно!');
                }
                updateRoundInfo();
                renderBoard();
            }

            // Обработка кнопки голосования (просто открывает модалку с любым доступным игроком)
            function openVoteGeneral() {
                if (!gameActive || roundPhase !== 'vote') return;
                openVoteModal(null);
            }

            // Звук вкл/выкл
            document.getElementById('soundToggle').addEventListener('click', () => {
                soundEnabled = !soundEnabled;
                document.getElementById('soundToggle').innerText = soundEnabled ? '🔊' : '🔇';
                if (soundEnabled && !audioCtx) initAudio();
                if (!soundEnabled && audioCtx) audioCtx.suspend();
                else if (soundEnabled && audioCtx) audioCtx.resume();
            });

            // Кнопки
            document.getElementById('newGameBtn').addEventListener('click', startNewGame);
            document.getElementById('nextRoundBtn').addEventListener('click', nextRound);
            document.getElementById('voteOpenBtn').addEventListener('click', openVoteGeneral);

            // Модалка
            document.getElementById('submitVote').addEventListener('click', submitVote);
            document.getElementById('closeModal').addEventListener('click', closeVoteModal);
            window.addEventListener('click', (e) => {
                if (e.target.classList.contains('vote-modal')) closeVoteModal();
            });

            // Инициализация аудио при первом клике где-либо
            document.body.addEventListener('click', function initOnce() {
                initAudio();
                document.body.removeEventListener('click', initOnce);
            }, { once: true });

            // Рендер первоначальный (пустой)
            renderBoard();
        })();
    </script>
</body>
</html>
