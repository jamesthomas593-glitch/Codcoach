<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>COD VOD Coach</title>
    <style>
        body {
            background-color: #0e1117;
            color: #ffffff;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
        }
        h1 {
            text-align: center;
            margin-bottom: 20px;
        }
        .tabs {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 20px;
        }
        .tab-btn {
            background-color: #1a1c23;
            color: #ffffff;
            border: 1px solid #30363d;
            padding: 10px 20px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
        }
        .tab-btn.active {
            background-color: #ff4b4b;
            border-color: #ff4b4b;
        }
        .view {
            display: none;
        }
        .view.active {
            display: block;
        }
        input[type="text"] {
            width: 100%;
            background-color: #1a1c23;
            color: #ffffff;
            border: 1px solid #30363d;
            border-radius: 8px;
            padding: 12px;
            box-sizing: border-box;
            margin-bottom: 15px;
        }
        button.submit-btn {
            width: 100%;
            background-color: #ff4b4b;
            color: white;
            font-weight: bold;
            border-radius: 8px;
            padding: 12px;
            border: none;
            cursor: pointer;
        }
        button.submit-btn:hover {
            background-color: #ff2b2b;
        }
        .card {
            background-color: #161b22;
            border: 1px solid #30363d;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 15px;
        }
        .chat-box {
            height: 350px;
            overflow-y: auto;
            border: 1px solid #30363d;
            border-radius: 8px;
            padding: 15px;
            background-color: #161b22;
            margin-bottom: 15px;
        }
        .message {
            margin-bottom: 12px;
            padding: 10px;
            border-radius: 6px;
        }
        .message.assistant {
            background-color: #21262d;
        }
        .message.user {
            background-color: #1f6feb;
            text-align: right;
        }
        .layout-grid {
            display: grid;
            grid-template-columns: 2fr 1fr;
            gap: 20px;
        }
        @media(max-width: 600px) {
            .layout-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

<div class="container">
    <h1>🎯 COD VOD COACH</h1>

    <!-- HOME VIEW -->
    <div id="home-view" class="view active">
        <div class="tabs">
            <button class="tab-btn active" onclick="switchTab('analysis')">🚀 VOD Analysis</button>
            <button class="tab-btn" onclick="switchTab('targets')">📊 Gameplay Targets</button>
        </div>

        <div id="tab-analysis" class="card">
            <p style="color: #8b949e; text-align: center;">Drop your match footage URL below to begin coaching analysis.</p>
            <input type="text" id="video-url-input" placeholder="Paste YouTube URL or file path...">
            <button class="submit-btn" onclick="startAnalysis()">Enter Analysis Suite</button>
        </div>

        <div id="tab-targets" class="card" style="display:none;">
            <h3>Core Gameplay Benchmarks & Targets</h3>
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                <div>
                    <h4>🎯 Mechanical Targets</h4>
                    <ul>
                        <li>Keep crosshairs locked at head/upper chest height.</li>
                        <li>Pre-aim door frames before breaking cover.</li>
                        <li>Maintain >85% shot clustering.</li>
                    </ul>
                </div>
                <div>
                    <h4>🧠 Tactical Targets</h4>
                    <ul>
                        <li>Never take open-field gunfights without hard cover.</li>
                        <li>Target 60%+ win rate on initial 1v1s.</li>
                        <li>Initiate rotations 15 seconds before zone shifts.</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <!-- CHAT & COACHING VIEW -->
    <div id="chat-view" class="view">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;">
            <span id="active-vod-label" style="font-size: 0.9em; color: #8b949e;"></span>
            <button class="tab-btn" onclick="resetApp()" style="background-color: #30363d;">🔄 New Footage</button>
        </div>

        <div class="layout-grid">
            <div>
                <div class="chat-box" id="chat-history"></div>
                <div style="display: flex; gap: 10px;">
                    <input type="text" id="chat-input" placeholder="Ask about positioning, request gun builds..." style="margin-bottom: 0;" onkeypress="handleKeyPress(event)">
                    <button class="submit-btn" style="width: 120px;" onclick="sendMessage()">Send</button>
                </div>
            </div>
            <div>
                <div class="card" style="margin-bottom: 0;">
                    <h3 style="margin-top: 0;">🛠️ Saved Gun Blueprints</h3>
                    <div id="blueprint-list">
                        <div class="card" style="background-color: #0e1117; font-size: 0.85em;">
                            <strong>Holger 556 / Meta AR</strong>
                            <ul style="padding-left: 15px; margin: 5px 0 0 0; color: #c9d1d9;">
                                <li>VT-7 Spitfire Suppressor</li>
                                <li>Chrios Match-Grade</li>
                                <li>Bruen Heavy Support Grip</li>
                                <li>40 Round Mag</li>
                                <li>JAK Glassless Optic</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    function switchTab(tab) {
        if(tab === 'analysis') {
            document.getElementById('tab-analysis').style.display = 'block';
            document.getElementById('tab-targets').style.display = 'none';
            document.querySelectorAll('.tab-btn')[0].classList.add('active');
            document.querySelectorAll('.tab-btn')[1].classList.remove('active');
        } else {
            document.getElementById('tab-analysis').style.display = 'none';
            document.getElementById('tab-targets').style.display = 'block';
            document.querySelectorAll('.tab-btn')[1].classList.add('active');
            document.querySelectorAll('.tab-btn')[0].classList.remove('active');
        }
    }

    let chatHistory = [];

    function startAnalysis() {
        const url = document.getElementById('video-url-input').value.trim();
        if(!url) {
            alert('Please enter a valid URL or file path.');
            return;
        }
        document.getElementById('active-vod-label').innerText = 'Active VOD: ' + url;
        document.getElementById('home-view').classList.remove('active');
        document.getElementById('chat-view').classList.add('active');

        chatHistory = [{
            role: 'assistant',
            text: `Loaded footage (${url}). I'm ready to review your gameplay. Ask about positioning, centering, or request a meta gun build.`
        }];
        renderChat();
    }

    function resetApp() {
        document.getElementById('chat-view').classList.remove('active');
        document.getElementById('home-view').classList.add('active');
        document.getElementById('video-url-input').value = '';
    }

    function renderChat() {
        const box = document.getElementById('chat-history');
        box.innerHTML = '';
        chatHistory.forEach(msg => {
            const div = document.createElement('div');
            div.className = 'message ' + msg.role;
            div.innerHTML = `<strong>${msg.role === 'user' ? 'You' : 'Coach'}:</strong><br>${msg.text}`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    }

    function sendMessage() {
        const input = document.getElementById('chat-input');
        const text = input.value.trim();
        if(!text) return;

        chatHistory.push({ role: 'user', text: text });
        input.value = '';
        renderChat();

        setTimeout(() => {
            let reply = "Reviewing your VOD: Focus heavily on your corner-clearing technique. You are exposing your angle too wide.";
            const lower = text.toLowerCase();
            if(lower.includes('gun') || lower.includes('build') || lower.includes('loadout') || lower.includes('meta')) {
                reply = "Here is the recommended meta build. I've added it to your sidebar blueprint reference:";
                addBlueprint("MCW / Balanced Assault Rifle", [
                    "Muzzle: Casus Brake",
                    "Barrel: 16.5\" MCW Cyclone Long Barrel",
                    "Underbarrel: DR-6 Handstop",
                    "Rear Grip: RB Regal Grip",
                    "Stock: Precision Stock"
                ]);
            }
            chatHistory.push({ role: 'assistant', text: reply });
            renderChat();
        }, 500);
    }

    function handleKeyPress(e) {
        if(e.key === 'Enter') sendMessage();
    }

    function addBlueprint(name, attachments) {
        const list = document.getElementById('blueprint-list');
        let attHtml = '';
        attachments.forEach(a => attHtml += `<li>${a}</li>`);
        list.innerHTML += `
            <div class="card" style="background-color: #0e1117; font-size: 0.85em; margin-top: 10px;">
                <strong>${name}</strong>
                <ul style="padding-left: 15px; margin: 5px 0 0 0; color: #c9d1d9;">${attHtml}</ul>
            </div>
        `;
    }
</script>

</body>
</html>
