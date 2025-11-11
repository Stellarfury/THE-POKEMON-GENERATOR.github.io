# THE-POKEMON-GENERATOR.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>TPG - The Pokémon Generator</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --primary-color: #ffcb05; /* Yellow */
            --secondary-color: #3d7dca; /* Blue */
            --dark-bg: #1a1a1a;
            --light-bg: #2c2c2c;
            --text-color: #f0f0f0;
            --grey-text: #888;
            --success-color: #4CAF50;
            --danger-color: #d9534f;
            --gold-accent: #FFD700;
            --secret-color: #8A2BE2; /* BlueViolet for Secret Rares */
        }

        /* --- Basic Setup --- */
        * { box-sizing: border-box; }
        html { scroll-behavior: smooth; }
        body { font-family: 'Roboto', sans-serif; background-color: var(--dark-bg); color: var(--text-color); margin: 0; -webkit-tap-highlight-color: transparent; overscroll-behavior-y: contain; }
        h1, h2, h3 { font-family: 'Press Start 2P', cursive; color: var(--primary-color); text-shadow: 2px 2px var(--secondary-color); margin-block-start: 0.5em; margin-block-end: 0.5em; }

        /* --- App Layout --- */
        #app-container { max-width: 800px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background-image: linear-gradient(to bottom, #232323, #121212); }
        #app { display: flex; flex-direction: column; height: 100%; opacity: 1; transition: opacity 0.5s; }
        #app.hidden { opacity: 0; pointer-events: none; }
        .app-header { text-align: center; padding: 15px; border-bottom: 2px solid #000; background-color: rgba(0,0,0,0.2); }
        .app-header h1 { font-size: 2rem; margin: 0; }
        #game-id-display { color: var(--grey-text); font-size: 0.8rem; }
        #main-content { flex-grow: 1; overflow-y: auto; padding: 20px; }
        .page { display: none; }
        .page.active { display: block; animation: fadeIn 0.4s; }
        .bottom-nav { display: flex; background-color: var(--light-bg); border-top: 2px solid #000; flex-shrink: 0; box-shadow: 0 -5px 15px rgba(0,0,0,0.3); }
        .nav-btn { flex-grow: 1; padding: 15px 5px; background: none; border: none; color: var(--grey-text); font-size: 0.8rem; font-family: 'Press Start 2P', cursive; cursor: pointer; transition: all 0.2s ease; }
        .nav-btn.active { color: var(--primary-color); background-color: #333; }

        /* --- Buttons & Inputs --- */
        button { font-family: 'Press Start 2P', cursive; background-color: var(--primary-color); color: #000; border: 2px solid var(--secondary-color); padding: 12px 20px; margin: 8px; cursor: pointer; font-size: 0.9rem; border-radius: 8px; transition: transform 0.2s, box-shadow 0.2s, filter 0.2s; text-shadow: 1px 1px #000; }
        button:hover { transform: translateY(-2px); box-shadow: 0 4px 8px rgba(0,0,0,0.4); filter: brightness(1.1); }
        button:disabled { background-color: #555; color: #888; border-color: #444; cursor: not-allowed; transform: none; box-shadow: none; text-shadow: none; }
        button.danger { background-color: var(--danger-color); color: white; border-color: #ac2925; text-shadow: 1px 1px #000; }
        input[type="text"], input[type="number"], input[type="password"], select { width: 100%; padding: 10px; margin: 8px 0; border-radius: 5px; border: 2px solid var(--secondary-color); background-color: #333; color: var(--text-color); font-size: 1rem; }

        /* --- Home & Register Page --- */
        #home-page, #register-page { text-align: center; }
        .game-management { margin: 20px 0; display: flex; flex-wrap: wrap; justify-content: center; }
        .rules { margin-top: 30px; text-align: left; display: inline-block; background: var(--light-bg); padding: 15px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.3); }
        .rules p { margin: 10px 0; }
        .register-form { margin: 20px 0; max-width: 400px; margin-left: auto; margin-right: auto;}
        #player-register-list { margin-top: 20px; }
        .register-item { display: flex; justify-content: space-between; align-items: center; background-color: var(--light-bg); padding: 10px 15px; border-radius: 8px; margin-bottom: 10px; text-align: left; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        .register-item-info { font-size: 0.8rem; }
        .register-item-info strong { font-size: 1.1rem; color: var(--primary-color); text-shadow: none; }
        .delete-player-btn { background: var(--danger-color); padding: 8px 12px; font-size: 0.7rem; margin: 0; }

        /* --- Roll Page --- */
        #roll-page { text-align: center; }
        #turn-info h2 { font-size: 1.5rem; }
        #dice-area { margin: 20px 0; min-height: 100px; }
        #dice-container { display: flex; gap: 15px; justify-content: center; perspective: 1000px; }
        .dice { width: 50px; height: 50px; position: relative; transform-style: preserve-3d; transition: transform 1s ease-out; }
        .dice-face { position: absolute; width: 50px; height: 50px; background-color: #fff; border: 2px solid #000; color: #000; font-size: 24px; font-weight: bold; display: flex; justify-content: center; align-items: center; }
        .d6 .face-1 { transform: rotateY(0deg) translateZ(25px); } .d6 .face-2 { transform: rotateY(90deg) translateZ(25px); } .d6 .face-3 { transform: rotateY(180deg) translateZ(25px); } .d6 .face-4 { transform: rotateY(-90deg) translateZ(25px); } .d6 .face-5 { transform: rotateX(90deg) translateZ(25px); } .d6 .face-6 { transform: rotateX(-90deg) translateZ(25px); }
        #bonus-dice-container { margin-top: 20px; }
        .bonus-dice-roll { animation: bonus-pop 0.5s ease; border: 2px solid var(--primary-color); padding: 10px; margin: 10px auto; border-radius: 8px; background: var(--light-bg); max-width: 300px; }
        #results-container { width: 100%; margin-top: 20px; }
        #results-title { opacity: 0; transition: opacity 0.5s; font-size: 1.2rem;}
        #pokemon-results { display: grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap: 15px; margin-top: 15px; }
        .pokemon-card { background-image: linear-gradient(to bottom, #3a3a3a, #2a2a2a); border-radius: 10px; padding: 10px; position: relative; border: 2px solid #444; animation: fadeIn 0.5s; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.3), inset 0 1px 2px rgba(255,255,255,0.1);}
        .pokemon-card:hover .pokemon-sprite { transform: scale(1.1); }
        .pokemon-card.shiny { border-color: var(--gold-accent); }
        .pokemon-card.secret { border-color: var(--secret-color); }
        .pokemon-card .pokemon-sprite { width: 96px; height: 96px; image-rendering: pixelated; filter: drop-shadow(0 4px 5px rgba(0,0,0,0.5)); transition: transform 0.3s ease; }
        .pokemon-card.rainbow .pokemon-sprite { animation: rainbow-cycle 4s linear infinite; }
        .pokemon-card p { margin: 5px 0 0 0; font-size: 0.9rem; text-transform: capitalize; font-weight: bold; color: var(--text-color); text-shadow: 1px 1px 2px #000; }
        .pokemon-card .rarity-badge { position: absolute; top: 8px; right: 8px; }

        /* --- Rarity Badges & Reveal Animations --- */
        .rarity-badge { padding: 4px 7px; font-size: 0.6rem; font-weight: bold; font-family: 'Roboto', sans-serif; text-shadow: none; border-radius: 5px; color: #fff; z-index: 2; text-transform: capitalize; }
        .rarity-badge.legendary { background-color: #a040a0; }
        .rarity-badge.gmax { background-color: #f08030; }
        .rarity-badge.shiny { background: linear-gradient(45deg, var(--gold-accent), #F0B400); color: #000; animation: shinyBadge 2s infinite; text-shadow: 0 0 2px rgba(255,255,255,0.5); }
        .rarity-badge.secret { background-color: var(--secret-color); border: 1px solid #c8a2c8; }
        .rarity-badge.rainbow { background-image: linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet); animation: shinyBadge 2s infinite; color: #000; text-shadow: 0 0 2px rgba(255,255,255,0.5); }
        .reveal-shiny { animation: shiny-reveal 1.5s ease-out; }
        .reveal-secret { animation: secret-reveal 1.5s ease-out; }
        .reveal-rainbow { animation: rainbow-reveal 2s ease-out; }

        /* --- Trophy Case --- */
        #trophy-case-page h2 { text-align: center; }
        #trophy-case-player-selector { margin-bottom: 20px; display: flex; flex-wrap: wrap; justify-content: center; }
        #trophy-collection { display: grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap: 15px; max-height: 70vh; overflow-y: auto; padding: 10px; }
        .trophy-entry { text-align: center; border-radius: 10px; overflow: hidden; position: relative; box-shadow: 0 5px 15px rgba(0,0,0,0.4); border: 2px solid #555; display: flex; flex-direction: column; justify-content: space-between; }
        .trophy-sprite-area { padding: 10px; }
        .trophy-sprite-area img { width: 96px; height: 96px; image-rendering: pixelated; filter: drop-shadow(0 4px 6px rgba(0,0,0,0.5)); }
        .trophy-info { background-color: rgba(0,0,0,0.5); padding: 5px; }
        .trophy-info p { margin: 0; text-transform: capitalize; font-weight: bold; }
        .trophy-info .trophy-type { font-size: 0.7rem; color: var(--grey-text); font-family: 'Press Start 2P'; }
        
        .trophy-entry.type-Shiny { background: radial-gradient(circle, #4a4a4a, #2a2a2a); border-color: var(--gold-accent); }
        .trophy-entry.type-Secret { background: radial-gradient(circle, #4a2a4a, #2a1a2a); border-color: var(--secret-color); }
        .trophy-entry.type-Rainbow { border-color: #fff; animation: rainbow-border 4s linear infinite; }
        .trophy-entry.type-Rainbow .trophy-sprite-area { background: linear-gradient(45deg, rgba(255,0,0,0.1), rgba(0,255,0,0.1), rgba(0,0,255,0.1)); }
        .trophy-entry .shiny-sprite { filter: hue-rotate(45deg) saturate(1.5) brightness(1.2) drop-shadow(0 4px 6px rgba(0,0,0,0.5)); }
        .trophy-entry .rainbow-sprite { animation: rainbow-cycle 4s linear infinite; }

        /* --- Scoreboard --- */
        #scoreboard-table { width: 100%; border-collapse: collapse; text-align: left; font-size: 0.9rem; }
        #scoreboard-table th, #scoreboard-table td { padding: 12px 8px; border-bottom: 1px solid var(--light-bg); text-align: center; }
        #scoreboard-table th { color: var(--primary-color); }
        #scoreboard-table tbody tr.active-player-row { background-color: #2a2a2a; border-left: 4px solid var(--primary-color); }
        #scoreboard-table td:nth-child(1) { text-align: left; font-weight: bold; }
        #scoreboard-table td:nth-child(2) { font-weight: bold; font-size: 1.2rem; }

        /* --- Modals & Animations --- */
        .modal { display: none; position: fixed; z-index: 100; left: 0; top: 0; width: 100%; height: 100%; background-color: rgba(0,0,0,0.8); align-items: center; justify-content: center; }
        .modal.active { display: flex; animation: fadeIn 0.3s; }
        .modal-content { background-color: var(--light-bg); padding: 25px; border: 2px solid var(--primary-color); border-radius: 15px; width: 90%; max-width: 500px; position: relative; text-align: center; box-shadow: 0 5px 25px rgba(0,0,0,0.5); }
        .close-btn { color: #aaa; position: absolute; top: 10px; right: 20px; font-size: 28px; font-weight: bold; cursor: pointer; }
        #new-game-player-list { text-align: left; max-height: 50vh; overflow-y: auto; }
        .player-checkbox-item { display: block; margin: 10px 0; }
        .player-checkbox-item label { font-size: 1.2rem; cursor: pointer; display: flex; align-items: center;}
        .player-checkbox-item input { width: 20px; height: 20px; margin-right: 15px; accent-color: var(--primary-color); }
        
        #test-panel-page .test-group { margin-bottom: 25px; padding: 15px; border: 1px solid var(--grey-text); border-radius: 8px; text-align: left; }
        #test-panel-page h3 { margin-top: 0; text-align: center; }
        #test-panel-page label { display: block; margin-bottom: 5px; color: var(--grey-text); }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes shinyGlow { from { box-shadow: 0 0 10px var(--gold-accent); } to { box-shadow: 0 0 25px #ffec8a; } }
        @keyframes shinyBadge { 0% { transform: scale(1); } 50% { transform: scale(1.08); } 100% { transform: scale(1); } }
        @keyframes bonus-pop { 0% { transform: scale(0.5); opacity: 0; } 80% { transform: scale(1.1); } 100% { transform: scale(1); opacity: 1; } }
        @keyframes rainbow-cycle { 0% { filter: hue-rotate(0deg) drop-shadow(0 4px 6px rgba(0,0,0,0.5)); } 100% { filter: hue-rotate(360deg) drop-shadow(0 4px 6px rgba(0,0,0,0.5)); } }
        @keyframes rainbow-border { 0% { border-color: #ff0000; } 14% { border-color: #ff7f00; } 28% { border-color: #ffff00; } 42% { border-color: #00ff00; } 57% { border-color: #0000ff; } 71% { border-color: #4b0082; } 85% { border-color: #8b00ff; } 100% { border-color: #ff0000; } }
        @keyframes shiny-reveal { 0% { transform: scale(0.5); box-shadow: 0 0 0px var(--gold-accent); } 50% { transform: scale(1.2); box-shadow: 0 0 50px var(--gold-accent); } 100% { transform: scale(1); box-shadow: none; } }
        @keyframes secret-reveal { 0% { transform: scale(0.8); filter: brightness(0); } 50% { transform: scale(1.1); filter: brightness(1); box-shadow: 0 0 40px var(--secret-color); } 100% { transform: scale(1); filter: brightness(1); box-shadow: none; } }
        @keyframes rainbow-reveal { 0% { transform: scale(0.5) rotate(-360deg); filter: saturate(0); } 70% { transform: scale(1.2) rotate(10deg); filter: saturate(2); box-shadow: 0 0 60px #fff; } 100% { transform: scale(1) rotate(0deg); filter: saturate(1.5); box-shadow: none; } }

        @media (max-width: 600px) {
            .app-header h1 { font-size: 1.5rem; }
            #main-content { padding: 10px; }
            button { padding: 10px 15px; font-size: 0.8rem; }
            .nav-btn { font-size: 0.6rem; }
            .dice { width: 40px; height: 40px; }
            .d6 .face-1 { transform: rotateY(0deg) translateZ(20px); } .d6 .face-2 { transform: rotateY(90deg) translateZ(20px); } .d6 .face-3 { transform: rotateY(180deg) translateZ(20px); } .d6 .face-4 { transform: rotateY(-90deg) translateZ(20px); } .d6 .face-5 { transform: rotateX(90deg) translateZ(20px); } .d6 .face-6 { transform: rotateX(-90deg) translateZ(20px); }
        }
    </style>
</head>
<body>

    <div id="app-container">
        <div id="app" class="hidden">
            <header class="app-header">
                <h1>TPG</h1>
                <div id="game-id-display">No Game Active</div>
            </header>

            <main id="main-content">
                <!-- Home Page -->
                <div id="home-page" class="page">
                    <h2>Home</h2>
                    <p>Start a new game or manage your data.</p>
                    <div class="game-management">
                        <button id="new-game-btn">Start New Game</button>
                        <button id="reset-all-btn" class="danger">Reset All Player Data</button>
                    </div>
                    <div class="rules">
                        <h3>How to Play</h3>
                        <p>• Go to the Register tab to create players.</p>
                        <p>• Start a new game and select registered players.</p>
                        <p>• First to <strong>exactly 100 points</strong> wins the round!</p>
                        <p>• After a win, scores reset and the game continues.</p>
                    </div>
                </div>
                
                <!-- Register Page -->
                <div id="register-page" class="page">
                    <h2>Player Registers</h2>
                    <div class="register-form">
                        <input type="text" id="register-name-input" placeholder="Enter new player name...">
                        <button id="register-player-btn">Register Player</button>
                    </div>
                    <div id="player-register-list"></div>
                </div>

                <!-- Roll Page -->
                <div id="roll-page" class="page">
                    <div id="turn-info"><h2 id="current-player-turn"></h2></div>
                    <div id="dice-area">
                        <div id="dice-container"></div>
                        <div id="bonus-dice-container"></div>
                    </div>
                    <button id="roll-dice-btn">Roll Dice</button>
                    <div id="results-container">
                        <h3 id="results-title"></h3>
                        <div id="pokemon-results"></div>
                    </div>
                </div>
                
                <!-- Trophy Case Page -->
                <div id="trophy-case-page" class="page">
                    <h2>Lifetime Trophy Case</h2>
                    <div id="trophy-case-player-selector"></div>
                    <div id="trophy-collection">
                        <p>Select a player to view their collection.</p>
                    </div>
                </div>

                <!-- Score Page -->
                <div id="score-page" class="page">
                    <h2>Scoreboard</h2>
                    <table id="scoreboard-table">
                        <thead>
                            <tr>
                                <th>Player</th>
                                <th>Score</th>
                                <th>Trophies</th>
                                <th>Wins</th>
                            </tr>
                        </thead>
                        <tbody></tbody>
                    </table>
                </div>
                
                <!-- Test Panel Page -->
                <div id="test-panel-page" class="page">
                    <h2>Developer Panel</h2>
                    <div id="dev-password-prompt">
                        <input type="password" id="dev-password-input" placeholder="Enter password...">
                        <button id="dev-password-submit">Access</button>
                    </div>
                    <div id="dev-controls" style="display: none;">
                        <div class="test-group">
                            <h3>Change Rarity Chances</h3>
                            <label>Shiny Chance (e.g., 0.1 for 10%)</label>
                            <input type="number" id="test-shiny-chance" step="0.0001">
                            <label>Secret Rare Chance (e.g., 0.1 for 10%)</label>
                            <input type="number" id="test-secret-chance" step="0.0001">
                            <label>Rainbow Rare Chance (e.g., 0.1 for 10%)</label>
                            <input type="number" id="test-rainbow-chance" step="0.00001">
                            <button id="save-test-chances-btn">Save Chances</button>
                        </div>
                        <div class="test-group">
                            <h3>Force Generate (on Roll screen)</h3>
                            <button id="force-shiny-btn">Generate Shiny</button>
                            <button id="force-secret-btn">Generate Secret Rare</button>
                            <button id="force-rainbow-btn">Generate Rainbow Rare</button>
                        </div>
                        <div class="test-group">
                            <h3>Modify Current Player's Score</h3>
                            <input type="number" id="test-points-value" placeholder="e.g., 10 or -10" style="width: auto;">
                            <button id="test-add-points-btn">Add/Sub Points</button>
                        </div>
                    </div>
