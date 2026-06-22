<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Web Teleprompter V3</title>
    <!-- Tailwind CSS for rich dashboard styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for dashboard icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* Custom scrollbars for presentation views */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.1);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(156, 163, 175, 0.5);
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(156, 163, 175, 0.8);
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 font-sans min-h-screen flex flex-col transition-all duration-300">

    <!-- SETUP VIEW -->
    <div id="setup-view" class="container mx-auto px-4 py-8 flex-grow max-w-7xl">
        <!-- Header -->
        <header class="flex flex-col md:flex-row justify-between items-center pb-6 mb-8 border-b border-slate-700 gap-4">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 p-2.5 rounded-lg text-white shadow-lg shadow-blue-500/30">
                    <i class="fa-solid fa-rectangle-list text-2xl"></i>
                </div>
                <div>
                    <h1 class="text-2xl font-bold tracking-tight text-white">Teleprompter Pro</h1>
                    <p class="text-xs text-slate-400">Professional customizable web teleprompter</p>
                </div>
            </div>
            
            <button onclick="launchPrompter()" class="w-full md:w-auto px-8 py-4 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-bold rounded-xl shadow-lg shadow-indigo-500/20 hover:shadow-indigo-500/35 transition-all flex items-center justify-center gap-3 transform hover:-translate-y-0.5 active:translate-y-0">
                <i class="fa-solid fa-play"></i> Start Prompting
            </button>
        </header>

        <!-- Main Dashboard Split Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            <!-- Left Panel: Settings Controls -->
            <div class="lg:col-span-5 space-y-6">
                <!-- Visual Config Panel -->
                <div class="bg-slate-800/80 backdrop-blur-md rounded-2xl p-6 border border-slate-700/60 shadow-xl">
                    <h2 class="text-lg font-bold text-white mb-5 flex items-center gap-2">
                        <i class="fa-solid fa-sliders text-blue-400"></i> Visual Configurations
                    </h2>
                    
                    <div class="space-y-5">
                        <!-- Mirror Options -->
                        <div>
                            <span class="block text-sm font-medium text-slate-400 mb-2">Display Flip / Mirroring</span>
                            <div class="grid grid-cols-2 gap-3">
                                <button id="btn-mirror-horiz" onclick="toggleSetting('flipHorizontal')" class="py-2 px-3 border border-slate-700 bg-slate-850 hover:bg-slate-700 rounded-lg text-sm font-semibold flex items-center justify-center gap-2 transition">
                                    <i class="fa-solid fa-arrows-left-right"></i> Horizontal
                                </button>
                                <button id="btn-mirror-vert" onclick="toggleSetting('flipVertical')" class="py-2 px-3 border border-slate-700 bg-slate-850 hover:bg-slate-700 rounded-lg text-sm font-semibold flex items-center justify-center gap-2 transition">
                                    <i class="fa-solid fa-arrows-up-down"></i> Vertical
                                </button>
                            </div>
                        </div>

                        <!-- Font Size and Margin Indent Range sliders -->
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="flex justify-between text-sm font-medium text-slate-400 mb-1">
                                    <span>Text Size</span>
                                    <span id="label-text-size" class="text-blue-400 font-semibold mb-1">48px</span>
                                </label>
                                <input type="range" id="input-text-size" min="20" max="120" value="48" oninput="updateSetting('textSize', this.value)" class="w-full h-2 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-blue-500">
                            </div>
                            <div>
                                <label class="flex justify-between text-sm font-medium text-slate-400 mb-1">
                                    <span>Side Margins</span>
                                    <span id="label-indent" class="text-blue-400 font-semibold">15%</span>
                                </label>
                                <input type="range" id="input-indent" min="0" max="45" value="15" oninput="updateSetting('indent', this.value)" class="w-full h-2 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-blue-500">
                            </div>
                        </div>

                        <!-- Stylings and Colors -->
                        <div class="grid grid-cols-3 gap-3">
                            <div>
                                <span class="block text-xs font-medium text-slate-400 mb-1">Text Color</span>
                                <input type="color" id="input-text-color" value="#ffffff" oninput="updateSetting('textColor', this.value)" class="w-full h-10 border border-slate-700 bg-slate-900 rounded cursor-pointer p-1">
                            </div>
                            <div>
                                <span class="block text-xs font-medium text-slate-400 mb-1">Bg Color</span>
                                <input type="color" id="input-bg-color" value="#000000" oninput="updateSetting('bgColor', this.value)" class="w-full h-10 border border-slate-700 bg-slate-900 rounded cursor-pointer p-1">
                            </div>
                            <div>
                                <span class="block text-xs font-medium text-slate-400 mb-1">Bg Opacity</span>
                                <div class="flex items-center bg-slate-900 border border-slate-700 rounded h-10 px-2">
                                    <input type="number" id="input-bg-opacity" value="100" min="0" max="100" oninput="updateSetting('bgOpacity', this.value)" class="w-full bg-transparent text-white text-sm focus:outline-none">
                                    <span class="text-slate-500 text-xs">%</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Focus Guide Bar Settings -->
                <div class="bg-slate-800/80 backdrop-blur-md rounded-2xl p-6 border border-slate-700/60 shadow-xl">
                    <div class="flex justify-between items-center mb-5">
                        <h2 class="text-lg font-bold text-white flex items-center gap-2">
                            <i class="fa-solid fa-eye text-emerald-400"></i> Reading Focus Line
                        </h2>
                        <label class="relative inline-flex items-center cursor-pointer">
                            <input type="checkbox" id="input-focus-enabled" checked onchange="updateSetting('focusBarEnabled', this.checked)" class="sr-only peer">
                            <div class="w-11 h-6 bg-slate-755 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-emerald-500"></div>
                        </label>
                    </div>

                    <div id="focus-bar-controls-container" class="space-y-5">
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="flex justify-between text-sm font-medium text-slate-400 mb-1">
                                    <span>Focus Height Offset</span>
                                    <span id="label-focus-height" class="text-emerald-400 font-semibold">100px</span>
                                </label>
                                <input type="range" id="input-focus-height" min="40" max="300" value="100" oninput="updateSetting('focusBarHeight', this.value)" class="w-full h-2 bg-slate-755 rounded-lg appearance-none cursor-pointer accent-emerald-500">
                            </div>
                            <div>
                                <label class="flex justify-between text-sm font-medium text-slate-400 mb-1">
                                    <span>Screen Position</span>
                                    <span id="label-focus-offset" class="text-emerald-400 font-semibold">35%</span>
                                </label>
                                <input type="range" id="input-focus-offset" min="10" max="90" value="35" oninput="updateSetting('focusOffset', this.value)" class="w-full h-2 bg-slate-755 rounded-lg appearance-none cursor-pointer accent-emerald-500">
                            </div>
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <span class="block text-xs font-medium text-slate-400 mb-1">Focus Color</span>
                                <input type="color" id="input-focus-color" value="#10b981" oninput="updateSetting('focusBarColor', this.value)" class="w-full h-10 border border-slate-700 bg-slate-900 rounded cursor-pointer p-1">
                            </div>
                            <div>
                                <span class="block text-xs font-medium text-slate-400 mb-1">Focus Opacity</span>
                                <div class="flex items-center bg-slate-900 border border-slate-700 rounded h-10 px-2">
                                    <input type="number" id="input-focus-opacity" value="25" min="5" max="95" oninput="updateSetting('focusBarOpacity', this.value)" class="w-full bg-transparent text-white text-sm focus:outline-none">
                                    <span class="text-slate-500 text-xs">%</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Input Speed & Interactive Key Bindings Panel -->
                <div class="bg-slate-800/80 backdrop-blur-md rounded-2xl p-6 border border-slate-700/60 shadow-xl">
                    <h2 class="text-lg font-bold text-white mb-5 flex items-center gap-2">
                        <i class="fa-solid fa-keyboard text-violet-400"></i> Speed & Keyboard Controls
                    </h2>

                    <div class="space-y-4">
                        <div>
                            <label class="flex justify-between text-sm font-medium text-slate-400 mb-1">
                                <span>Default Start Speed</span>
                                <span id="label-start-speed" class="text-violet-400 font-semibold">4</span>
                            </label>
                            <input type="range" id="input-start-speed" min="-10" max="25" value="4" oninput="updateSetting('startSpeed', this.value)" class="w-full h-2 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-violet-500">
                        </div>

                        <!-- Keybindings Visual Configurator -->
                        <div>
                            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 mb-3">
                                <span class="block text-sm font-medium text-slate-400">Configure Key Bindings</span>
                                <div class="flex items-center gap-2">
                                    <button onclick="applyPresenterPresets()" class="text-xs bg-emerald-600/20 text-emerald-400 border border-emerald-500/30 px-2 py-1 rounded font-bold hover:bg-emerald-600/35 transition">
                                        <i class="fa-solid fa-wand-magic-sparkles"></i> Preset Remote
                                    </button>
                                    <button onclick="resetDefaultKeybindings()" class="text-xs text-rose-450 hover:text-rose-405 hover:underline font-semibold">Reset</button>
                                </div>
                            </div>
                            <p class="text-[11px] text-slate-450 leading-relaxed mb-4 bg-slate-900/50 p-2.5 rounded-lg border border-slate-700/50">
                                <strong>Tip:</strong> Tap a button below, then immediately press any key on your keyboard <strong>or your Bluetooth Presenter remote</strong> to map it. We block all browser page moves automatically.
                            </p>
                            <div class="space-y-2 text-xs">
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Accelerate / Speed Up</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="openCaptureModal('speedUp')" id="bind-speedUp">ArrowUp</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Decelerate / Reverse</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="openCaptureModal('speedDown')" id="bind-speedDown">ArrowDown</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Start / Stop Toggle</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="openCaptureModal('toggle')" id="bind-toggle">Space</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Reset to Head</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="openCaptureModal('reset')" id="bind-reset">KeyR</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Exit Teleprompter Mode</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="openCaptureModal('exit')" id="bind-exit">Escape</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Right Panel: Script Editor & Storage integrations -->
            <div class="lg:col-span-7 flex flex-col space-y-6">
                
                <div class="bg-slate-800/80 backdrop-blur-md rounded-2xl p-6 border border-slate-700/60 shadow-xl flex-grow flex flex-col">
                    
                    <!-- Title area and Integration toolbar -->
                    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-4">
                        <div class="w-full sm:w-auto">
                            <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wide mb-1">Active File Script Name</label>
                            <input type="text" id="input-file-name" value="untitled-script.txt" oninput="state.fileName = this.value; saveToLocalStorage();" class="bg-slate-900 border border-slate-700 text-white font-semibold rounded-lg px-3 py-1.5 w-full sm:w-64 focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm">
                        </div>
                        
                        <!-- Integration Action Suite -->
                        <div class="flex flex-wrap items-center gap-2">
                            <!-- Native imports -->
                            <button onclick="document.getElementById('native-file-loader').click()" class="bg-slate-700 hover:bg-slate-600 text-white text-xs px-3 py-2 rounded-lg font-bold flex items-center gap-1.5 pointer transition">
                                <i class="fa-solid fa-file-import text-blue-400"></i> Import
                            </button>
                            <input type="file" id="native-file-loader" onchange="importLocalSystemFile(event)" accept=".txt,.rtf" class="hidden">
                            
                            <!-- Cloud hooks -->
                            <button onclick="cloudIntegratorDialog('Google Drive')" class="bg-indigo-950/60 hover:bg-indigo-900/80 border border-indigo-700/50 text-indigo-300 text-xs px-3 py-2 rounded-lg font-bold flex items-center gap-1.5 transition">
                                <i class="fa-brands fa-google-drive text-emerald-400"></i> Google Drive
                            </button>
                            <button onclick="cloudIntegratorDialog('OneDrive')" class="bg-sky-950/60 hover:bg-sky-900/80 border border-sky-700/50 text-sky-300 text-xs px-3 py-2 rounded-lg font-bold flex items-center gap-1.5 transition">
                                <i class="fa-brands fa-windows text-blue-400"></i> OneDrive
                            </button>
                        </div>
                    </div>

                    <!-- Main Text-Area dynamic script editor -->
                    <div class="flex-grow flex flex-col min-h-[350px]">
                        <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wide mb-1">Editor Script Console</label>
                        <textarea id="main-script-editor" oninput="updateScriptText(this.value)" class="w-full flex-grow p-4 min-h-[350px] bg-slate-900 border border-slate-700 rounded-xl text-white font-mono text-sm leading-relaxed focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Type or paste your presentation text right here..."></textarea>
                    </div>

                    <!-- Bottom saving modules -->
                    <div class="flex flex-wrap justify-between items-center gap-3 mt-4 pt-4 border-t border-slate-700">
                        <span class="text-xs text-emerald-400 font-medium flex items-center gap-1.5">
                            <i class="fa-solid fa-circle-check"></i> Automatically compiled locally
                        </span>
                        
                        <div class="flex items-center gap-2">
                            <button onclick="exportScriptText('txt')" class="bg-blue-600/20 hover:bg-blue-600/35 text-blue-300 border border-blue-500/40 text-xs px-4 py-2.5 rounded-lg font-bold flex items-center gap-1.5 transition">
                                <i class="fa-solid fa-file-lines"></i> Export .TXT
                            </button>
                            <button onclick="exportScriptText('rtf')" class="bg-blue-600/20 hover:bg-blue-600/35 text-blue-300 border border-blue-500/40 text-xs px-4 py-2.5 rounded-lg font-bold flex items-center gap-1.5 transition">
                                <i class="fa-solid fa-file-word"></i> Export .RTF
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <!-- FULL SCREEN TELEPROMPTER ACTIVE VIEWPORT -->
    <!-- overflow layout strictly blocks any native page-manipulated jumping -->
    <div id="prompter-viewport" class="fixed inset-0 z-50 hidden select-none overflow-hidden cursor-none flex flex-col">
        <!-- Visual guide color-accented helper bar overlay -->
        <div id="prompter-focus-guide" class="absolute left-0 right-0 pointer-events-none z-10 transition-all border-y border-transparent"></div>

        <!-- Scrollable dynamic text content frame -->
        <div id="prompter-scroll-frame" class="w-full flex-grow overflow-hidden focus:outline-none">
            <!-- Outer wrapper supporting mirror properties scale transforms -->
            <div id="prompter-transform-layer" class="w-full min-h-full flex flex-col origin-center">
                <!-- Inner element establishing configured side indents -->
                <div id="prompter-content" class="w-full pt-[65vh] pb-[65vh] whitespace-pre-wrap breaks-word"></div>
            </div>
        </div>

        <!-- On-Screen Teleprompter Heads-up Navigation Overlay -->
        <div id="prompter-status-hud" class="absolute bottom-6 left-1/2 transform -translate-x-1/2 bg-slate-900/90 border border-slate-700 px-6 py-3 rounded-2xl flex items-center gap-6 text-white text-xs opacity-0 hover:opacity-100 transition-opacity duration-300 z-30 shadow-2xl">
            <div class="flex items-center gap-2 font-mono">
                <span class="text-slate-400 font-semibold text-xs">Speed:</span>
                <span id="hud-speed-val" class="text-blue-400 font-bold text-sm">0.0</span>
            </div>
            <div class="h-4 w-[1px] bg-slate-700"></div>
            <div class="flex items-center gap-2">
                <span class="text-slate-400 font-semibold">Active: </span>
                <span class="text-slate-300" id="hud-controls-display">Keys</span>
            </div>
            <div class="h-4 w-[1px] bg-slate-700"></div>
            <
