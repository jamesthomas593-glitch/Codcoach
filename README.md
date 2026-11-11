<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Call of Duty // Tactical AI Coach</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        tactical: {
                            bg: '#07090c',
                            card: '#0f131a',
                            border: '#1e2634',
                            accent: '#ffb703',
                            goldGlow: 'rgba(255, 183, 3, 0.15)',
                            textBright: '#ffffff',
                            muted: '#8b9bb4'
                        }
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #07090c; color: #f3f4f6; overflow-x: hidden; }
        h1, h2, h3, .tactical-font { font-family: 'Rajdhani', sans-serif; letter-spacing: 0.08em; }
        
        .hud-panel {
            background: linear-gradient(135deg, rgba(15, 19, 26, 0.95) 0%, rgba(10, 13, 18, 0.98) 100%);
            border: 1px solid #1e2634;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.6), inset 0 1px 0 rgba(255, 255, 255, 0.05);
            position: relative;
        }
        .hud-panel::before {
            content: ''; position: absolute; top: 0; left: 0; width: 8px; height: 8px; border-top: 2px solid #ffb703; border-left: 2px solid #ffb703;
        }
        .hud-panel::after {
            content: ''; position: absolute; bottom: 0; right: 0; width: 8px; height: 8px; border-bottom: 2px solid #ffb703; border-right: 2px solid #ffb703;
        }
        
        .glow-gold { box-shadow: 0 0 20px rgba(255, 183, 3, 0.25); }
        .scanlines {
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.03), rgba(0, 255, 0, 0.01), rgba(0, 0, 255, 0.03));
            background-size: 100% 4px, 6px 100%;
            pointer-events: none;
        }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: #07090c; }
        ::-webkit-scrollbar-thumb { background: #1e2634; border-radius: 2px; }
        ::-webkit-scrollbar-thumb:hover { background: #ffb703; }
    </style>
</head>
<body class="min-h-screen flex flex-col antialiased selection:bg-[#ffb703] selection:text-black relative">

    <div class="absolute inset-0 scanlines z-40 fixed"></div>

    <header class="bg-[#0f131a] border-b border-[#1e2634] sticky top-0 z-50 backdrop-blur-md bg-opacity-95">
        <div class="max-w-7xl mx-auto px-4 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="w-3 h-3 bg-[#ffb703] animate-ping"></div>
                <h1 class="text-xl font-bold tracking-wider text-white">COD <span class="text-[#ffb703]">//</span> TACTICAL AI COACH</h1>
            </div>
            <div class="flex items-center space-x-4">
                <div class="hidden sm:flex items-center space-x-2 text-xs font-mono bg-[#07090c] px-3 py-1.5 rounded border border-[#1e2634]">
                    <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span class="text-gray-300">TELEMETRY: SECURE</span>
                </div>
                <button onclick="triggerNotification('AI Core', 'Scanning live weapon telemetry and match packets...')" class="bg-[#ffb703]/10 hover:bg-[#ffb703]/20 text-[#ffb703] border border-[#ffb703]/30 px-3 py-1.5 text-xs font-bold uppercase tracking-wider transition">
                    System Diagnostic
                </button>
            </div>
        </div>
    </header>

    <div id="toast-container" class="fixed bottom-5 right-5 z-50 space-y-3 max-w-sm w-full"></div>

    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 grid grid-cols-1 lg:grid-cols-4 gap-6 relative z-10">

        <aside class="lg:col-span-1 space-y-4">
            <div class="hud-panel p-4">
                <h3 class="text-xs font-bold text-gray-400 uppercase tracking-widest mb-3 font-mono">COMMAND DIRECTORY</h3>
                <nav class="space-y-1.5">
                    <button onclick="switchTab('dashboard')" id="nav-dashboard" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide bg-[#ffb703]/10 text-[#ffb703] border-l-2 border-[#ffb703] transition flex items-center justify-between">
                        <span>01. Tactical Dashboard</span>
                        <span class="text-[10px] font-mono opacity-70">MAIN</span>
                    </button>
                    <button onclick="switchTab('matches')" id="nav-matches" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide text-gray-400 hover:text-white hover:bg-[#151c28] transition flex items-center justify-between">
                        <span>02. Match Logs & Analytics</span>
                        <span class="text-[10px] font-mono opacity-70">AAR</span>
                    </button>
                    <button onclick="switchTab('youtube')" id="nav-youtube" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide text-gray-400 hover:text-white hover:bg-[#151c28] transition flex items-center justify-between">
                        <span>03. YouTube & Video Feed</span>
                        <span class="text-[10px] font-mono opacity-70">SYNC</span>
                    </button>
                    <button onclick="switchTab('controller')" id="nav-controller" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide text-gray-400 hover:text-white hover:bg-[#151c28] transition flex items-center justify-between">
                        <span>04. Controller Tuning</span>
                        <span class="text-[10px] font-mono opacity-70">SENS</span>
                    </button>
                    <button onclick="switchTab('loadouts')" id="nav-loadouts" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide text-gray-400 hover:text-white hover:bg-[#151c28] transition flex items-center justify-between">
                        <span>05. Gunsmith Visualizer</span>
                        <span class="text-[10px] font-mono opacity-70">ARMORY</span>
                    </button>
                    <button onclick="switchTab('settings')" id="nav-settings" class="w-full text-left px-3.5 py-3 text-sm font-semibold tracking-wide text-gray-400 hover:text-white hover:bg-[#151c28] transition flex items-center justify-between">
                        <span>06. API Configuration</span>
                        <span class="text-[10px] font-mono opacity-70">CONFIG</span>
                    </button>
                </nav>
            </div>

            <div class="hud-panel p-4">
                <h3 class="text-xs font-bold text-gray-400 uppercase tracking-widest mb-2 font-mono">SYSTEM SPEC</h3>
                <div class="space-y-2 text-xs font-mono text-gray-300">
                    <div class="flex justify-between"><span>PLATFORM:</span> <span class="text-[#ffb703]">PLAYSTATION 5</span></div>
                    <div class="flex justify-between"><span>ENGINE:</span> <span class="text-emerald-400">GEMINI 2.5 FLASH</span></div>
                    <div class="flex justify-between"><span>LATENCY:</span> <span class="text-emerald-400">14MS</span></div>
                    <div class="flex justify-between"><span>STATUS:</span> <span class="text-emerald-400">CONNECTED</span></div>
                </div>
            </div>
        </aside>

        <section class="lg:col-span-3 space-y-6">

            <!-- TAB 1: DASHBOARD -->
            <div id="tab-dashboard" class="space-y-6">
                <div class="hud-panel p-6 bg-gradient-to-r from-[#0f131a] to-[#151c28]">
                    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                        <div>
                            <span class="text-xs font-mono text-[#ffb703] tracking-widest uppercase">AFTER-ACTION REPORT // SECURE CHANNEL</span>
                            <h2 class="text-2xl sm:text-3xl font-bold tracking-wide mt-1 text-white">OPERATOR COMMAND CENTER</h2>
                            <p class="text-sm text-gray-400 mt-1">AI coaching neural network is actively monitoring your gameplay loop.</p>
                        </div>
                        <button onclick="switchTab('matches')" class="bg-[#ffb703] hover:bg-[#e0a102] text-black px-5 py-2.5 font-bold text-xs uppercase tracking-wider transition glow-gold">
                            + Log New Match
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div class="hud-panel p-4">
                        <span class="text-xs text-gray-400 uppercase font-mono">Overall K/D Ratio</span>
                        <div class="text-3xl font-bold text-white mt-1 tactical-font" id="stat-kd">1.42</div>
                        <div class="text-xs text-emerald-400 mt-1 flex items-center">▲ Optimal performance zone</div>
                    </div>
                    <div class="hud-panel p-4">
                        <span class="text-xs text-gray-400 uppercase font-mono">Total Kills Logged</span>
                        <div class="text-3xl font-bold text-[#ffb703] mt-1 tactical-font" id="stat-kills-total">44</div>
                        <div class="text-xs text-gray-400 mt-1">Sum of recorded matches</div>
                    </div>
                    <div class="hud-panel p-4">
                        <span class="text-xs text-gray-400 uppercase font-mono">Win Rate</span>
                        <div class="text-3xl font-bold text-white mt-1 tactical-font" id="stat-winrate">68.5%</div>
                        <div class="text-xs text-emerald-400 mt-1">Squad sync stable</div>
                    </div>
                </div>

                <div class="hud-panel p-6">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-lg font-bold tracking-wide flex items-center space-x-2">
                            <span class="w-2.5 h-2.5 bg-[#ffb703]"></span>
                            <span>GEMINI TACTICAL BRIEFING</span>
                        </h3>
                        <span class="text-xs font-mono bg-[#ffb703]/10 text-[#ffb703] px-2.5 py-1 border border-[#ffb703]/30">LIVE DEEP-SCAN</span>
                    </div>
                    <div class="bg-[#07090c] p-4 border border-[#1e2634] space-y-3 text-sm text-gray-300">
                        <p id="ai-summary-text">"Telemetry indicates peak gunfight efficiency during mid-range engagements on Terminal. Recommendation: Equip the MCW Tactical Stability build for your upcoming Hardpoint queue to mitigate recoil spread during continuous firing."</p>
                        <div class="border-t border-[#1e2634] pt-3 flex flex-wrap gap-2 text-xs font-mono">
                            <span class="bg-[#0f131a] px-2.5 py-1 text-[#ffb703] border border-[#1e2634]">FOCUS: RECOIL CONTROL</span>
                            <span class="bg-[#0f131a] px-2.5 py-1 text-emerald-400 border border-[#1e2634]">LOADOUT: MCW META</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- TAB 2: MATCH LOGS -->
            <div id="tab-matches" class="space-y-6 hidden">
                <div class="hud-panel p-6">
                    <h2 class="text-xl font-bold mb-4">LOG A NEW MATCH</h2>
                    <form id="match-form" onsubmit="handleMatchSubmit(event)" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-mono text-gray-400 mb-1">MAP NAME</label>
                            <input type="text" id="match-map" required placeholder="e.g. Terminal, Highrise, Rust" class="w-full bg-[#07090c] border border-[#1e2634] p-2.5 text-sm text-white focus:border-[#ffb703] focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-mono text-gray-400 mb-1">GAME MODE</label>
                            <select id="match-mode" class="w-full bg-[#07090c] border border-[#1e2634] p-2.5 text-sm text-white focus:border-[#ffb703] focus:outline-none">
                                <option>Hardpoint</option>
                                <option>Search & Destroy</option>
                                <option>Control</option>
                                <option>Team Deathmatch</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-mono text-gray-400 mb-1">KILLS</label>
                            <input type="number" id="match-kills" required min="0" placeholder="28" class="w-full bg-[#07090c] border border-[#1e2634] p-2.5 text-sm text-white focus:border-[#ffb703] focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-mono text-gray-400 mb-1">DEATHS</label>
                            <input type="number" id="match-deaths" required min="0" placeholder="14" class="w-full bg-[#07090c] border border-[#1e2634] p-2.5 text-sm text-white focus:border-[#ffb703] focus:outline-none">
                        </div>
                        <div class="sm:col-span-2">
                            <button type="submit" class="w-full bg-[#ffb703] hover:bg-[#e0a102] text-black font-bold py-2.5 text-xs uppercase tracking-wider transition glow-gold">
                                Save Match & Trigger AI Analysis
                            </button>
                        </div>
                    </form>
                </div>

                <div class="hud-panel p-6">
                    <h3 class="text-lg font-bold mb-4">MATCH HISTORY LOG</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm">
                            <thead class="bg-[#07090c] text-gray-400 font-mono text-xs">
                                <tr>
                                    <th class="p-3">MAP / MODE</th>
                                    <th class="p-3">K / D</th>
                                    <th class="p-3">RATIO</th>
                                    <th class="p-3">STATUS</th>
                                </tr>
                            </thead>
                            <tbody id="match-table-body" class="divide-y divide-[#1e2634]">
                                <tr class="hover:bg-[#151c28]">
                                    <td class="p-3 font-semibold">Terminal <span class="text-xs text-gray-400 block font-normal">Hardpoint</span></td>
                                    <td class="p-3">32 / 18</td>
                                    <td class="p-3 text-emerald-400 font-mono font-bold">1.77</td>
                                    <td class="p-3"><span class="bg-emerald-500/10 text-emerald-400 px-2 py-0.5 text-xs border border-emerald-500/30">WIN</span></td>
                                </tr>
                                <tr class="hover:bg-[#151c28]">
                                    <td class="p-3 font-semibold">Sub Base <span class="text-xs text-gray-400 block font-normal">Search & Destroy</span></td>
                                    <td class="p-3">12 / 10</td>
                                    <td class="p-3 text-[#ffb703] font-mono font-bold">1.20</td>
                                    <td class="p-3"><span class="bg-emerald-500/10 text-emerald-400 px-2 py-0.5 text-xs border border-emerald-500/30">WIN</span></td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- TAB 3: YOUTUBE & VIDEO FEED -->
            <div id="tab-youtube" class="space-y-6 hidden">
                <div class="hud-panel p-6 space-y-4">
                    <div class="flex justify-between items-center">
                        <div>
                            <h2 class="text-xl font-bold">YOUTUBE PLAYLIST TELEMETRY</h2>
                            <p class="text-sm text-gray-400">Automated queue syncing with your PlayStation clips playlist.</p>
                        </div>
                        <button onclick="triggerNotification('YouTube Sync', 'Playlist scanned: No unanalyzed clips found.')" class="bg-[#ffb703] hover:bg-[#e0a102] text-black px-4 py-2 font-bold text-xs uppercase tracking-wider transition">
                            Sync Playlist
                        </button>
                    </div>

                    <div class="bg-[#07090c] p-4 border border-[#1e2634] space-y-3">
                        <div class="flex items-center justify-between text-xs font-mono text-gray-400 border-b border-[#1e2634] pb-2">
                            <span>CONNECTED PLAYLIST:</span>
                            <span class="text-white">Call of Duty AI Coach (ID: PL_COD_9921)</span>
                        </div>
                        <div class="space-y-2" id="youtube-clip-list">
                            <div class="flex items-center justify-between p-3 bg-[#0f131a] border border-[#1e2634] text-sm">
                                <div class="flex items-center space-x-3">
                                    <span class="text-[#ffb703] font-mono">▶</span>
                                    <span>Match_Highlight_Terminal_Hardpoint_04.mp4</span>
                                </div>
                                <span class="text-xs font-mono text-emerald-400 bg-emerald-500/10 px-2 py-0.5 border border-emerald-500/30">PROCESSED</span>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="hud-panel p-6 space-y-4">
                    <h2 class="text-xl font-bold">MANUAL CLIP LINKING</h2>
                    <p class="text-sm text-gray-400">Paste your YouTube gameplay clip URL below for immediate AI frame analysis.</p>
                    <div class="flex gap-2">
                        <input type="text" id="manual-clip-url" placeholder="https://www.youtube.com/watch?v=..." class="flex-1 bg-[#07090c] border border-[#1e2634] p-2.5 text-sm text-white focus:border-[#ffb703] focus:outline-none">
                        <button onclick="submitManualClip()" class="bg-[#ffb703] hover:bg-[#e0a102] text-black px-5 py-2.5 font-bold text-xs uppercase tracking-wider transition glow-gold">
                            Analyze Clip
                        </button>
                    </div>
                </div>
            </div>

            <!-- TAB 4: CONTROLLER TUNING -->
            <div id="tab-controller" class="space-y-6 hidden">
                <div class="hud-panel p-6 space-y-4">
                    <h2 class="text-xl font-bold">CONTROLLER PRECISION TUNING</h2>
                    <p class="text-sm text-gray-400">AI-optimized curves for PlayStation DualSense controllers.</p>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div class="bg-[#07090c] p-4 border border-[#1e2634]">
                            <span class="text-xs font-mono text-gray-400">LEFT STICK DEADZONE</span>
                            <div class="text-2xl 
