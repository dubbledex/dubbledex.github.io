<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Web Teleprompter</title>
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

    <!-- HIDDEN INPUT TO FORCE VIRTUAL KEYBOARD POPULATION ON MOBILE -->
    <input type="text" id="hidden-key-binder" class="absolute opacity-0 pointer-events-none w-px h-px shadow-none border-none outline-none" aria-hidden="true" autocomplete="off text">

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
            
            <button onclick="launchPrompter()" class="w-full md:w-auto px-8 py-4 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-bold rounded-xl shadow-lg shadow-indigo-500/20 hover:shadow-indigo-500/35 transition-all flex items-center justify-center gap-3 transform hover:-translate-y-0.5 active:translate-y-0 animate-pulse">
                <i class="fa-solid fa-play"></i> Start Prompting (Ref/Space)
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
                            <div class="flex justify-between items-center mb-3">
                                <span class="block text-sm font-medium text-slate-400">Configure Key Bindings</span>
                                <button onclick="resetDefaultKeybindings()" class="text-xs text-rose-450 hover:text-rose-405 hover:underline font-semibold">Reset to Defaults</button>
                            </div>
                            <p class="text-[11px] text-slate-400 mb-3 bg-slate-900/50 p-2.5 rounded-lg border border-slate-700/50">
                                <strong>Tip:</strong> Tap a button below, then immediately press any key on your keyboard <strong>or your Bluetooth Presenter remote</strong> to map it. We block all browser page moves automatically.
                            </p>
                            <div class="space-y-2 text-xs">
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Accelerate / Speed Up</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="captureKey('speedUp')" id="bind-speedUp">ArrowUp</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Decelerate / Reverse</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="captureKey('speedDown')" id="bind-speedDown">ArrowDown</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Start / Stop Toggle</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="captureKey('toggle')" id="bind-toggle">Space</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Reset to Head</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="captureKey('reset')" id="bind-reset">KeyR</button>
                                </div>
                                <div class="flex justify-between items-center py-2 px-3 bg-slate-900/50 rounded-lg border border-slate-700">
                                    <span class="text-slate-350 font-medium">Exit Teleprompter Mode</span>
                                    <button class="keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]" onclick="captureKey('exit')" id="bind-exit">Escape</button>
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
    <!-- overflow is styled as hidden dynamically to lock out any manual page jumping -->
    <div id="prompter-viewport" class="fixed inset-0 z-50 hidden select-none overflow-hidden cursor-none flex flex-col">
        <!-- Visual guide color-accented helper bar overlay -->
        <div id="prompter-focus-guide" class="absolute left-0 right-0 pointer-events-none z-10 transition-all border-y border-transparent"></div>

        <!-- Scrollable content frame is locked from native manual scrolling to strictly prevent presenter jumping -->
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
                <span class="text-slate-400">Speed:</span>
                <span id="hud-speed-val" class="text-blue-400 font-bold text-sm">0.0</span>
            </div>
            <div class="h-4 w-[1px] bg-slate-700"></div>
            <div class="flex items-center gap-2">
                <span class="text-slate-400 font-semibold">Active controls: </span>
                <span class="text-slate-300" id="hud-controls-display">Keys</span>
            </div>
            <div class="h-4 w-[1px] bg-slate-700"></div>
            <button onclick="exitPrompter()" class="text-rose-450 hover:text-rose-400 font-bold flex items-center gap-1 transition">
                <i class="fa-solid fa-rectangle-xmark"></i> Close (Esc)
            </button>
        </div>
    </div>


    <!-- System Interactive Toast Message Dialog and Modals -->
    <div id="cloud-dialog" class="fixed inset-0 bg-black/75 z-55 flex items-center justify-center p-4 opacity-0 pointer-events-none transition-all duration-300">
        <div class="bg-slate-800 border border-slate-700 max-w-md w-full rounded-2xl p-6 shadow-2xl relative">
            <button onclick="closeCloudDialog()" class="absolute top-4 right-4 text-slate-400 hover:text-white transition">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <div id="cloud-dialog-icon" class="text-4xl text-blue-500 mb-4 text-center"></div>
            <h3 id="cloud-dialog-title" class="text-xl font-bold text-white text-center mb-2">Cloud File Integration</h3>
            <p id="cloud-dialog-body" class="text-sm text-slate-350 leading-relaxed mb-6 text-center"></p>
            <div class="flex justify-center flex-wrap gap-2">
                <button onclick="document.getElementById('native-file-loader').click(); closeCloudDialog();" class="bg-blue-600 hover:bg-blue-500 text-white text-xs font-bold px-4 py-2.5 rounded-lg transition flex items-center gap-1.5">
                    <i class="fa-solid fa-file-lines"></i> Browse Synced Local Folder
                </button>
                <button onclick="closeCloudDialog()" class="bg-slate-700 hover:bg-slate-600 text-slate-300 text-xs font-bold px-4 py-2.5 rounded-lg transition">
                    Close Details
                </button>
            </div>
        </div>
    </div>


    <!-- JAVASCRIPT LOGIC CONTROLLING CONFIGS, SCROLL ENGINE, & FILE EXPORTS -->
    <script>
        // Default state configuration objects
        const defaultBindings = {
            speedUp: "ArrowUp",
            speedDown: "ArrowDown",
            toggle: " ", // Space bar representation
            reset: "KeyR",
            exit: "Escape"
        };

        const state = {
            text: "Welcome to Professional Web Teleprompter!\n\nThis application is a complete presentation engine that runs entirely within your browser.\n\nYou can configure display variables instantly on the setup dashboard:\n- Use Horizontal flip or vertical mirroring if writing scripts into beams using optical beamsplitter glass.\n- Set margins or side indents down to lock text lines directly in the center to reduce reader visual movement tracker fatigue.\n- Personalize spacing configurations, focus guideline tracking systems, background transparencies and customizable default keybindings.\n\nTo begin running the scrolling script, click 'Start Prompting' at top right or simply strike your designated Start/Stop toggle (by default, Spacebar).\n\nYou can map scroll speed actions dynamically to speed up (default Up Arrow) or speed down (default Down Arrow). Scrolling below speed 0 smoothly transitions the rendering flow into inverse backward motion to assist rapid backtracks!\n\nExport your script files into TXT formatted files, or standard RTF documents anytime. Import documents dynamically using the input interface.\n\nNow, edit this display layout, configure text sizes, trigger the play mode, look directly into the camera lens, and deliver a clean presentation!",
            fileName: "teleprompter-script.txt",
            flipHorizontal: false,
            flipVertical: false,
            textSize: 44,
            textColor: "#ffffff",
            bgColor: "#000000",
            bgOpacity: 100,
            indent: 14,
            startSpeed: 4,
            currentSpeed: 0,
            isScrolling: false,
            scrollPosition: 0,
            focusBarEnabled: true,
            focusBarHeight: 90,
            focusOffset: 35,
            focusBarColor: "#10b981", 
            focusBarOpacity: 25,
            bindings: { ...defaultBindings }
        };

        // Variable capture states for registering custom keybindings
        let captureTarget = null;
        let animationFrameId = null;
        let lastTimestamp = 0;

        // On document initialization
        window.addEventListener('load', () => {
            loadFromLocalStorage();
            initializeUIValues();
            applyInteractiveHighlights();
        });

        // Local Storage persistent handlers
        function saveToLocalStorage() {
            localStorage.setItem('tp_state', JSON.stringify({
                text: state.text,
                fileName: state.fileName,
                flipHorizontal: state.flipHorizontal,
                flipVertical: state.flipVertical,
                textSize: state.textSize,
                textColor: state.textColor,
                bgColor: state.bgColor,
                bgOpacity: state.bgOpacity,
                indent: state.indent,
                startSpeed: state.startSpeed,
                focusBarEnabled: state.focusBarEnabled,
                focusBarHeight: state.focusBarHeight,
                focusOffset: state.focusOffset,
                focusBarColor: state.focusBarColor,
                focusBarOpacity: state.focusBarOpacity,
                bindings: state.bindings
            }));
        }

        function loadFromLocalStorage() {
            const raw = localStorage.getItem('tp_state');
            if (raw) {
                try {
                    const parsed = JSON.parse(raw);
                    // Standard recovery safety check for individual bindings
                    if (!parsed.bindings) parsed.bindings = { ...defaultBindings };
                    Object.assign(state, parsed);
                } catch (e) {
                    console.error("Local storage sync error", e);
                }
            }
        }

        // Initialize form elements with variables
        function initializeUIValues() {
            document.getElementById('main-script-editor').value = state.text;
            document.getElementById('input-file-name').value = state.fileName;
            
            document.getElementById('input-text-size').value = state.textSize;
            document.getElementById('label-text-size').innerText = state.textSize + 'px';
            
            document.getElementById('input-indent').value = state.indent;
            document.getElementById('label-indent').innerText = state.indent + '%';
            
            document.getElementById('input-text-color').value = state.textColor;
            document.getElementById('input-bg-color').value = state.bgColor;
            document.getElementById('input-bg-opacity').value = state.bgOpacity;
            
            document.getElementById('input-start-speed').value = state.startSpeed;
            document.getElementById('label-start-speed').innerText = state.startSpeed;
            
            document.getElementById('input-focus-enabled').checked = state.focusBarEnabled;
            document.getElementById('input-focus-height').value = state.focusBarHeight;
            document.getElementById('label-focus-height').innerText = state.focusBarHeight + 'px';
            
            document.getElementById('input-focus-offset').value = state.focusOffset;
            document.getElementById('label-focus-offset').innerText = state.focusOffset + '%';
            
            document.getElementById('input-focus-color').value = state.focusBarColor;
            document.getElementById('input-focus-opacity').value = state.focusBarOpacity;

            configureFocusBarSubElements();
            updateBindingsUI();
        }

        // Dynamic element value sync setters
        function updateSetting(key, val) {
            if (key === 'focusBarEnabled') {
                state[key] = Boolean(val);
                configureFocusBarSubElements();
            } else if (key === 'bgOpacity' || key === 'focusBarOpacity') {
                state[key] = Math.max(0, Math.min(100, parseInt(val) || 0));
            } else if (key === 'textSize' || key === 'indent' || key === 'startSpeed' || key === 'focusBarHeight' || key === 'focusOffset') {
                state[key] = parseInt(val);
            } else {
                state[key] = val;
            }

            // Update interactive element visual badges
            if (key === 'textSize') document.getElementById('label-text-size').innerText = val + 'px';
            if (key === 'indent') document.getElementById('label-indent').innerText = val + '%';
            if (key === 'startSpeed') document.getElementById('label-start-speed').innerText = val;
            if (key === 'focusBarHeight') document.getElementById('label-focus-height').innerText = val + 'px';
            if (key === 'focusOffset') document.getElementById('label-focus-offset').innerText = val + '%';

            saveToLocalStorage();
            applyInteractiveHighlights();
        }

        function toggleSetting(key) {
            state[key] = !state[key];
            saveToLocalStorage();
            applyInteractiveHighlights();
        }

        function updateScriptText(txt) {
            state.text = txt;
            saveToLocalStorage();
        }

        function configureFocusBarSubElements() {
            const pane = document.getElementById('focus-bar-controls-container');
            if (state.focusBarEnabled) {
                pane.classList.remove('opacity-40', 'pointer-events-none');
            } else {
                pane.classList.add('opacity-40', 'pointer-events-none');
            }
        }

        // Highlight custom visual configuration control borders
        function applyInteractiveHighlights() {
            const hBtn = document.getElementById('btn-mirror-horiz');
            if (state.flipHorizontal) {
                hBtn.classList.add('bg-blue-600/20', 'border-blue-500', 'text-blue-400 font-bold');
            } else {
                hBtn.classList.remove('bg-blue-600/20', 'border-blue-500', 'text-blue-400 font-bold');
            }
            
            const vBtn = document.getElementById('btn-mirror-vert');
            if (state.flipVertical) {
                vBtn.classList.add('bg-blue-600/20', 'border-blue-500', 'text-blue-400 font-bold');
            } else {
                vBtn.classList.remove('bg-blue-600/20', 'border-blue-500', 'text-blue-400 font-bold');
            }
        }

        // Custom Key Binding UI Renderers
        function updateBindingsUI() {
            for (const [bindingName, keyString] of Object.entries(state.bindings)) {
                const btn = document.getElementById(`bind-${bindingName}`);
                if (btn) {
                    btn.innerText = getFriendlyName(keyString);
                }
            }
        }

        function getFriendlyName(keyString) {
            if (keyString === " ") return "Space";
            return keyString;
        }

        function resetDefaultKeybindings() {
            state.bindings = { ...defaultBindings };
            saveToLocalStorage();
            updateBindingsUI();
        }

        // Dynamic Capture Key supporting mobile on-screen keyboard trigger and Bluetooth Remote Input
        function captureKey(bindingName) {
            captureTarget = bindingName;
            
            // Adjust visual label states block
            const btn = document.getElementById(`bind-${bindingName}`);
            btn.innerText = "PRESS KEY...";
            btn.className = "keybind-btn border-2 border-rose-500 px-3 py-1.5 rounded bg-rose-950/60 text-rose-300 font-black tracking-wider animate-pulse min-w-[100px]";

            // Force mobile focus onto hidden element to programmatically prompt physical on-screen virtual keyboard
            const forceInput = document.getElementById('hidden-key-binder');
            forceInput.value = "";
            forceInput.focus();
        }

        // Handle assignments once a keystroke is received from standard keys or hardware bluetooth presenters
        function handleKeyCapture(pressedCode, pressedKey) {
            if (!captureTarget) return;

            // Priority: store e.code (e.g. PageDown, ArrowUp, KeyK) because code is immune to local keyboard layout variations.
            // Fall back to actual key value if code is empty
            let valueToSave = pressedCode || pressedKey;
            
            if (pressedKey === " " || pressedCode === "Space") {
                valueToSave = " ";
            }

            state.bindings[captureTarget] = valueToSave;

            const btn = document.getElementById(`bind-${captureTarget}`);
            // Reset button structure back to active styling classes
            btn.className = "keybind-btn border border-violet-500/30 px-3 py-1.5 rounded bg-violet-950/40 text-violet-300 font-bold tracking-wider hover:bg-violet-900/40 min-w-[100px]";

            // Reset systems
            captureTarget = null;
            document.getElementById('hidden-key-binder').blur();
            
            saveToLocalStorage();
            updateBindingsUI();
        }

        // Event listener hooks on physical virtual interface input
        document.getElementById('hidden-key-binder').addEventListener('keydown', (e) => {
            if (captureTarget) {
                e.preventDefault();
                e.stopPropagation();
                handleKeyCapture(e.code, e.key);
            }
        });

        // SINGLE COMPREHENSIVE WINDOW KEYDOWN LISTENER
        window.addEventListener('keydown', (e) => {
            const isPrompterActive = !document.getElementById('prompter-viewport').classList.contains('hidden');

            // If we are configuring keybind buttons:
            if (captureTarget) {
                e.preventDefault();
                e.stopPropagation();
                handleKeyCapture(e.code, e.key);
                return;
            }

            // If the full visual teleprompter is currently running:
            if (isPrompterActive) {
                // VERY IMPORTANT: Prevent standard browser default actions for keys when running 
                // teleprompter Mode. This blocks PageUp, PageDown, and Arrow keys from jumping the text.
                if (e.key !== 'Escape' && e.code !== 'Escape') {
                    e.preventDefault();
                }

                const receivedCode = e.code;
                const receivedKey = e.key;

                // Match against mapped actions
                if (matchesBind('toggle', receivedCode, receivedKey)) {
                    toggleScrollPlay();
                } else if (matchesBind('speedUp', receivedCode, receivedKey)) {
                    adjustPrompterSpeed(0.5);
                } else if (matchesBind('speedDown', receivedCode, receivedKey)) {
                    adjustPrompterSpeed(-0.5);
                } else if (matchesBind('reset', receivedCode, receivedKey)) {
                    resetPrompterToStart();
                } else if (matchesBind('exit', receivedCode, receivedKey)) {
                    exitPrompter();
                }
            }
        }, { passive: false }); // Explicitly register as active to ensure preventDefault() works without delays

        // Key match evaluator helper
        function matchesBind(action, code, key) {
            const target = state.bindings[action];
            if (!target) return false;
            
            // Check literal representation mappings
            if (target === " ") {
                return (code === "Space" || key === " ");
            }
            return (code === target || key === target);
        }

        // Launch & Style calculations for Prompter Mode Viewport
        function launchPrompter() {
            const viewport = document.getElementById('prompter-viewport');
            const transformLayer = document.getElementById('prompter-transform-layer');
            const content = document.getElementById('prompter-content');
            const guide = document.getElementById('prompter-focus-guide');

            const rVal = parseInt(state.bgColor.slice(1, 3), 16);
            const gVal = parseInt(state.bgColor.slice(3, 5), 16);
            const bVal = parseInt(state.bgColor.slice(5, 7), 16);
            viewport.style.backgroundColor = `rgba(${rVal}, ${gVal}, ${bVal}, ${state.bgOpacity / 100})`;

            content.style.fontSize = `${state.textSize}px`;
            content.style.color = state.textColor;
            content.style.lineHeight = "1.5";
            
            let scaleX = state.flipHorizontal ? -1 : 1;
            let scaleY = state.flipVertical ? -1 : 1;
            transformLayer.style.transform = `scale(${scaleX}, ${scaleY})`;

            content.style.paddingLeft = `${state.indent}%`;
            content.style.paddingRight = `${state.indent}%`;

            content.innerText = state.text;

            state.scrollPosition = 0;
            state.currentSpeed = 0; 
            state.isScrolling = false;
            updateHudDisplay();

            const scrollFrame = document.getElementById('prompter-scroll-frame');
            scrollFrame.scrollTop = 0;

            if (state.focusBarEnabled) {
                guide.classList.remove('hidden');
                guide.style.top = `${state.focusOffset}vh`;
                
                guide.style.height = `${state.focusBarHeight}px`;
                guide.style.marginTop = `${-(state.focusBarHeight / 2)}px`;
                
                const fR = parseInt(state.focusBarColor.slice(1,3), 16);
                const fG = parseInt(state.focusBarColor.slice(3,5), 16);
                const fB = parseInt(state.focusBarColor.slice(5,7), 16);
                guide.style.backgroundColor = `rgba(${fR}, ${fG}, ${fB}, ${state.focusBarOpacity / 100})`;
                guide.style.borderColor = `rgba(${fR}, ${fG}, ${fB}, ${(state.focusBarOpacity + 20) / 100})`;
            } else {
                guide.classList.add('hidden');
            }

            // Inject configured keybind indicators direct inside status help panel HUD bar
            document.getElementById('hud-controls-display').innerHTML = `
                <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded font-bold">${getFriendlyName(state.bindings.speedUp)}</span> Up | 
                <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded font-bold">${getFriendlyName(state.bindings.speedDown)}</span> Down | 
                <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded font-bold">${getFriendlyName(state.bindings.toggle)}</span> Pause
            `;

            viewport.classList.remove('hidden');
            document.body.classList.add('overflow-hidden');

            lastTimestamp = performance.now();
            animationFrameId = requestAnimationFrame(scrollingEngineTick);
        }

        function exitPrompter() {
            cancelAnimationFrame(animationFrameId);
            document.getElementById('prompter-viewport').classList.add('hidden');
            document.body.classList.remove('overflow-hidden');
            state.isScrolling = false;
        }

        // Scroll engine handling programmatic canvas shift values
        function scrollingEngineTick(timestamp) {
            const scrollFrame = document.getElementById('prompter-scroll-frame');
            const delta = (timestamp - lastTimestamp) / 1000; 
            lastTimestamp = timestamp;

            if (state.isScrolling && state.currentSpeed !== 0) {
                const step = state.currentSpeed * 25 * delta;
                state.scrollPosition += step;

                const maxScrollLimit = scrollFrame.scrollHeight - scrollFrame.clientHeight;
                if (state.scrollPosition > maxScrollLimit) {
                    state.scrollPosition = maxScrollLimit;
                    state.isScrolling = false;
                    state.currentSpeed = 0;
                    updateHudDisplay();
                } else if (state.scrollPosition < 0) {
                    state.scrollPosition = 0;
                    state.isScrolling = false;
                    state.currentSpeed = 0;
                    updateHudDisplay();
                }

                scrollFrame.scrollTop = state.scrollPosition;
            }

            animationFrameId = requestAnimationFrame(scrollingEngineTick);
        }

        function toggleScrollPlay() {
            state.isScrolling = !state.isScrolling;
            if (state.isScrolling && state.currentSpeed === 0) {
                state.currentSpeed = state.startSpeed;
            }
            updateHudDisplay();
        }

        function adjustPrompterSpeed(amount) {
            if (!state.isScrolling) {
                state.isScrolling = true;
            }
            state.currentSpeed = parseFloat((state.currentSpeed + amount).toFixed(1));
            updateHudDisplay();
        }

        function resetPrompterToStart() {
            state.scrollPosition = 0;
            const scrollFrame = document.getElementById('prompter-scroll-frame');
            scrollFrame.scrollTop = 0;
            state.isScrolling = false;
            state.currentSpeed = 0;
            updateHudDisplay();
        }

        function updateHudDisplay() {
            const hudVal = document.getElementById('hud-speed-val');
            hudVal.innerText = state.isScrolling ? state.currentSpeed.toFixed(1) : "PAUSED (" + state.currentSpeed.toFixed(1) + ")";
            
            if (state.currentSpeed < 0) {
                hudVal.classList.remove('text-blue-400');
                hudVal.classList.add('text-amber-400');
            } else {
                hudVal.classList.remove('text-amber-400');
                hudVal.classList.add('text-blue-400');
            }
        }

        // FILE EXPORT UTILITY HANDLERS
        function exportScriptText(format) {
            const textToSave = state.text;
            let mimeType = "text/plain";
            let filename = state.fileName;
            let outputContent = "";

            if (format === 'txt') {
                outputContent = textToSave;
                if (!filename.endsWith('.txt')) {
                    filename = filename.replace(/\.[^/.]+$/, "") + ".txt";
                }
            } else if (format === 'rtf') {
                mimeType = "application/rtf";
                if (!filename.endsWith('.rtf')) {
                    filename = filename.replace(/\.[^/.]+$/, "") + ".rtf";
                }
                
                const escapedText = textToSave
                    .replace(/\\/g, "\\\\")
                    .replace(/{/g, "\\{")
                    .replace(/}/g, "\\}")
                    .replace(/\n/g, "\\par\n");

                outputContent = `{\\rtf1\\ansi\\ansicpg1252\\deff0\\nouicompat{\\fonttbl{\\f0\\fnil\\fcharset0 Arial;}}\n{\\*\\generator TeleprompterPro;}\\viewkind4\\uc1\n\\pard\\f0\\fs28 ${escapedText}\\par\n}`;
            }

            const blob = new Blob([outputContent], { type: mimeType });
            const tempLink = document.createElement("a");
            tempLink.download = filename;
            tempLink.href = window.URL.createObjectURL(blob);
            tempLink.click();
            window.URL.revokeObjectURL(tempLink.href);
        }

        // LOCAL FILE IMPORT HANDLER
        function importLocalSystemFile(event) {
            const file = event.target.files[0];
            if (!file) return;

            state.fileName = file.name;
            document.getElementById('input-file-name').value = file.name;

            const reader = new FileReader();
            reader.onload = function(e) {
                let content = e.target.result;
                
                if (file.name.endsWith('.rtf')) {
                    content = stripRawRTF(content);
                }
                
                state.text = content;
                document.getElementById('main-script-editor').value = content;
                saveToLocalStorage();
            };
            
            reader.readAsText(file);
        }

        function stripRawRTF(rtfStr) {
            let clean = rtfStr;
            clean = clean.replace(/\\rtf1[\s\S]*?\\viewkind4\\uc1[\s\S]*?\\pard/g, "");
            clean = clean.replace(/\\[a-z0-9\-]+(\s|;)?/g, " ");
            clean = clean.replace(/\{[^\{\}]*\}/g, "");
            clean = clean.replace(/\}/g, "");
            clean = clean.replace(/\{/g, "");
            clean = clean.trim();
            return clean;
        }

        // Cloud Drive Dialog simulator explaining synchronization integration setup
        function cloudIntegratorDialog(serviceName) {
            const dialog = document.getElementById('cloud-dialog');
            const iconWrap = document.getElementById('cloud-dialog-icon');
            const title = document.getElementById('cloud-dialog-title');
            const body = document.getElementById('cloud-dialog-body');

            title.innerText = `${serviceName} Integration`;

            if (serviceName === "Google Drive") {
                iconWrap.innerHTML = `<i class="fa-brands fa-google-drive"></i>`;
                body.innerHTML = `To read directly from <strong>Google Drive</strong>, we highly recommend synced folders:<br><br>
                1. Save file directly inside your Google Drive Sync Folder locally on your Desktop.<br>
                2. Select <strong>Import File</strong> locally to browse.<br>
                3. Your changes sync to the cloud automatically when you edit or save directly using this interface!`;
            } else {
                iconWrap.innerHTML = `<i class="fa-brands fa-windows text-sky-400"></i>`;
                body.innerHTML = `To read directly from <strong>Microsoft OneDrive</strong> sync:<br><br>
                1. Simply save text formatting files directly in OneDrive folders.<br>
                2. Tap import, browse OneDrive's mounting directories.<br>
                3. Saving your export downloads will automatically upload updates into active MS files!`;
            }

            dialog.classList.remove('opacity-0', 'pointer-events-none');
        }

        function closeCloudDialog() {
            document.getElementById('cloud-dialog').classList.add('opacity-0', 'pointer-events-none');
        }
    </script>
</body>
</html>
