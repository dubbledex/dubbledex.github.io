<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <title>Canvas Web Teleprompter</title>

  <script src="https://cdn.tailwindcss.com"></script>

  <style>
    html,
    body {
      min-height: 100%;
    }

    body.prompt-lock {
      overflow: hidden !important;
      position: fixed !important;
      width: 100% !important;
      height: 100% !important;
      overscroll-behavior: none !important;
      touch-action: none !important;
    }

    input[type="color"] {
      -webkit-appearance: none;
      border: none;
      padding: 0;
      overflow: hidden;
    }

    #prompterPage,
    #canvasStage,
    #prompterCanvas {
      overscroll-behavior: none;
      touch-action: none;
    }

    #prompterCanvas {
      display: block;
      width: 100vw;
      height: 100vh;
    }

    .kbd-capturing {
      outline: 3px solid #fb7185;
      background: rgba(127, 29, 29, 0.7) !important;
      color: #fecdd3 !important;
    }
  </style>
</head>

<body class="bg-slate-950 text-slate-100">
  <!-- SETUP PAGE -->
  <main id="setupPage" class="max-w-7xl mx-auto p-4 md:p-8">
    <header class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 border-b border-slate-700 pb-5 mb-6">
      <div>
        <h1 class="text-3xl font-bold">Canvas Web Teleprompter</h1>
        <p class="text-slate-400 text-sm mt-1">
          Smooth canvas-rendered teleprompter with keyboard and Bluetooth presenter controls.
        </p>
      </div>

      <div class="flex flex-col sm:flex-row gap-2">
        <button
          id="clearSettingsBtn"
          type="button"
          class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold"
        >
          Clear Saved Settings
        </button>

        <button
          id="startPromptBtn"
          type="button"
          class="px-6 py-3 rounded-xl bg-blue-600 hover:bg-blue-500 font-bold shadow-lg"
        >
          Start Prompting
        </button>
      </div>
    </header>

    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
      <!-- SETTINGS COLUMN -->
      <section class="lg:col-span-5 space-y-6">
        <!-- DISPLAY SETTINGS -->
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <h2 class="text-xl font-bold mb-4">Display Settings</h2>

          <div class="grid grid-cols-2 gap-3 mb-5">
            <button
              id="flipHBtn"
              type="button"
              class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold"
            >
              Mirror horizontal
            </button>

            <button
              id="flipVBtn"
              type="button"
              class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold"
            >
              Flip vertical
            </button>
          </div>

          <div class="space-y-5">
            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Text size</span>
                <strong id="textSizeLabel" class="text-blue-400">48px</strong>
              </div>
              <input id="textSizeInput" type="range" min="18" max="140" value="48" class="w-full accent-blue-500" />
            </label>

            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Side indent / margins</span>
                <strong id="indentLabel" class="text-blue-400">15%</strong>
              </div>
              <input id="indentInput" type="range" min="0" max="45" value="15" class="w-full accent-blue-500" />
            </label>

            <div class="grid grid-cols-3 gap-3">
              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Text colour</span>
                <input id="textColorInput" type="color" value="#ffffff" class="w-full h-11 rounded-lg bg-slate-800" />
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Background</span>
                <input id="bgColorInput" type="color" value="#000000" class="w-full h-11 rounded-lg bg-slate-800" />
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Opacity %</span>
                <input
                  id="bgOpacityInput"
                  type="number"
                  min="0"
                  max="100"
                  value="100"
                  class="w-full h-11 rounded-lg bg-slate-800 border border-slate-700 px-3"
                />
              </label>
            </div>
          </div>
        </div>

        <!-- FOCUS BAR -->
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <div class="flex items-center justify-between mb-4">
            <h2 class="text-xl font-bold">Focus Bar</h2>

            <label class="flex items-center gap-2 text-sm">
              <input id="focusEnabledInput" type="checkbox" class="accent-emerald-500" checked />
              Enabled
            </label>
          </div>

          <div class="space-y-5">
            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Bar height</span>
                <strong id="focusHeightLabel" class="text-emerald-400">90px</strong>
              </div>
              <input id="focusHeightInput" type="range" min="30" max="320" value="90" class="w-full accent-emerald-500" />
            </label>

            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Vertical position</span>
                <strong id="focusOffsetLabel" class="text-emerald-400">35%</strong>
              </div>
              <input id="focusOffsetInput" type="range" min="5" max="95" value="35" class="w-full accent-emerald-500" />
            </label>

            <div class="grid grid-cols-2 gap-3">
              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Bar colour</span>
                <input id="focusColorInput" type="color" value="#10b981" class="w-full h-11 rounded-lg bg-slate-800" />
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Opacity %</span>
                <input
                  id="focusOpacityInput"
                  type="number"
                  min="0"
                  max="100"
                  value="25"
                  class="w-full h-11 rounded-lg bg-slate-800 border border-slate-700 px-3"
                />
              </label>
            </div>
          </div>
        </div>

        <!-- CONTROLS -->
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <h2 class="text-xl font-bold mb-4">Speed and Controls</h2>

          <label class="block mb-5">
            <div class="flex justify-between text-sm mb-1">
              <span>Default start speed</span>
              <strong id="startSpeedLabel" class="text-violet-400">4</strong>
            </div>
            <input id="startSpeedInput" type="range" min="-10" max="25" value="4" class="w-full accent-violet-500" />
          </label>

          <div class="space-y-3 mb-4">
            <div class="flex flex-wrap gap-2">
              <button
                id="presenterPresetBtn"
                type="button"
                class="px-3 py-2 rounded-lg bg-emerald-700 hover:bg-emerald-600 font-semibold text-sm"
              >
                Presenter preset: E2 Page/Arrow
              </button>

              <button
                id="resetBindingsBtn"
                type="button"
                class="px-3 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold text-sm"
              >
                Reset keybindings
              </button>
            </div>

            <label class="flex items-start gap-3 p-3 rounded-xl bg-slate-950 border border-slate-700 text-sm">
              <input id="singleButtonFallbackInput" type="checkbox" class="mt-1 accent-amber-500" />
              <span>
                <strong class="text-amber-300">Single-button fallback</strong><br />
                Use only if both E2 page buttons appear as Button 0. Single click speeds up, double click slows down, long press exits.
              </span>
            </label>
          </div>

          <p id="captureStatus" class="hidden mb-4 p-3 rounded-xl bg-rose-950 border border-rose-700 text-rose-100 text-sm">
            Capturing input. Press one keyboard or presenter button now. Mouse/touch Button 0 will not be saved because both E2 page buttons can appear identical.
          </p>

          <div class="space-y-2 text-sm">
            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Speed up</span>
              <button data-bind="speedUp" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">
                ArrowUp
              </button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Slow down / reverse</span>
              <button data-bind="speedDown" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">
                ArrowDown
              </button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Start / stop</span>
              <button data-bind="toggle" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">
                Space
              </button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Reset to start</span>
              <button data-bind="reset" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">
                KeyR
              </button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Exit prompter</span>
              <button data-bind="exit" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">
                Escape
              </button>
            </div>
          </div>

          <div class="mt-4 p-3 rounded-xl bg-slate-950 border border-slate-700 text-xs text-slate-400">
            <strong class="text-slate-200">E2 Control note:</strong>
            use PageUp/PageDown mode first. If that fails, try Left/Right mode.
            Volume mode is usually captured by Android itself and may not reach the browser.
          </div>
        </div>
      </section>

      <!-- SCRIPT EDITOR -->
      <section class="lg:col-span-7">
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5 h-full flex flex-col">
          <div class="flex flex-col sm:flex-row sm:items-end sm:justify-between gap-3 mb-4">
            <label class="block">
              <span class="block text-sm text-slate-400 mb-1">File name</span>
              <input
                id="fileNameInput"
                type="text"
                value="teleprompter-script.txt"
                class="w-full sm:w-80 rounded-lg bg-slate-950 border border-slate-700 px-3 py-2"
              />
            </label>

            <div class="flex flex-wrap gap-2">
              <button id="importBtn" type="button" class="px-3 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold text-sm">
                Import
              </button>
              <input id="fileInput" type="file" accept=".txt,.rtf" class="hidden" />

              <button id="exportTxtBtn" type="button" class="px-3 py-2 rounded-lg bg-blue-700 hover:bg-blue-600 font-semibold text-sm">
                Export TXT
              </button>

              <button id="exportRtfBtn" type="button" class="px-3 py-2 rounded-lg bg-blue-700 hover:bg-blue-600 font-semibold text-sm">
                Export RTF
              </button>
            </div>
          </div>

          <textarea
            id="scriptInput"
            class="flex-grow min-h-[520px] rounded-xl bg-slate-950 border border-slate-700 p-4 font-mono text-sm leading-relaxed focus:outline-none focus:ring-2 focus:ring-blue-500"
          ></textarea>
        </div>
      </section>
    </div>
  </main>

  <!-- CANVAS PROMPTER PAGE -->
  <section id="prompterPage" class="hidden fixed inset-0 z-50 overflow-hidden">
    <div id="canvasStage" tabindex="0" class="absolute inset-0 outline-none">
      <canvas id="prompterCanvas"></canvas>
    </div>

    <!-- Mobile-friendly visible controls -->
    <button
      id="mobileExitPromptBtn"
      type="button"
      class="fixed top-3 right-3 z-40 px-4 py-3 rounded-xl bg-red-600 hover:bg-red-500 text-white font-bold shadow-lg"
    >
      Exit
    </button>

    <button
      id="tapExitPromptBtn"
      type="button"
      class="fixed top-3 left-3 z-40 px-4 py-3 rounded-xl bg-slate-900/75 border border-slate-600 text-white font-semibold shadow-lg"
    >
      Back
    </button>

    <div
      id="hud"
      class="fixed left-1/2 bottom-4 -translate-x-1/2 bg-slate-950/90 border border-slate-700 rounded-2xl px-4 py-3 text-xs text-slate-200 opacity-100 md:opacity-0 md:hover:opacity-100 transition-opacity z-30 max-w-[95vw]"
    >
      <span>Speed: <strong id="speedHud" class="text-blue-400">0</strong></span>
      <span class="mx-2 text-slate-600">|</span>
      <span id="controlsHud"></span>
      <button id="closePromptBtn" type="button" class="ml-3 text-red-400 font-bold">
        Close
      </button>
    </div>
  </section>

  <script>
    "use strict";

    const STORAGE_KEY = "canvas_teleprompter_e2_v1";

    const defaultState = {
      text:
        "Welcome to the canvas web teleprompter.\n\n" +
        "This version renders text onto a canvas for smoother motion and less flicker at higher speeds.\n\n" +
        "Paste or type your script into the setup page.\n\n" +
        "Default controls:\n" +
        "- Speed up: ArrowUp\n" +
        "- Slow down / reverse: ArrowDown\n" +
        "- Start / stop: Space\n" +
        "- Reset: R\n" +
        "- Exit: Escape\n\n" +
        "E2 Control support:\n" +
        "- PageDown / ArrowRight / Enter = speed up\n" +
        "- PageUp / ArrowLeft = slow down / reverse\n" +
        "- Return / Escape / BrowserBack / Backspace = exit prompter\n",
      fileName: "teleprompter-script.txt",
      flipHorizontal: false,
      flipVertical: false,
      textSize: 48,
      textColor: "#ffffff",
      bgColor: "#000000",
      bgOpacity: 100,
      indent: 15,
      startSpeed: 4,
      focusEnabled: true,
      focusHeight: 90,
      focusOffset: 35,
      focusColor: "#10b981",
      focusOpacity: 25,
      presenterMode: true,
      singleButtonFallback: false,
      bindings: {
        speedUp: { code: "ArrowUp", key: "ArrowUp", keyCode: 38, which: 38 },
        speedDown: { code: "ArrowDown", key: "ArrowDown", keyCode: 40, which: 40 },
        toggle: { code: "Space", key: " ", keyCode: 32, which: 32 },
        reset: { code: "KeyR", key: "r", keyCode: 82, which: 82 },
        exit: { code: "Escape", key: "Escape", keyCode: 27, which: 27 }
      }
    };

    let state = structuredCloneSafe(defaultState);

    let currentSpeed = 0;
    let isScrolling = false;
    let scrollPos = 0;
    let maxScroll = 0;
    let rafId = null;
    let lastTime = 0;
    let lastHudUpdate = 0;

    let captureAction = null;
    let prompterHistoryActive = false;
    let savedBodyScrollY = 0;

    let lastPointerClickTime = 0;
    let pointerDownAt = 0;
    let pointerLongPressTimer = null;

    let canvas = null;
    let ctx = null;
    let canvasCssWidth = 0;
    let canvasCssHeight = 0;
    let canvasDpr = 1;

    let renderLines = [];
    let lineHeight = 72;
    let contentHeight = 0;
    let topPadding = 0;
    let bottomPadding = 0;
    let textAreaLeft = 0;
    let textAreaWidth = 0;

    const pixelsPerSecondPerSpeedUnit = 24;

    const els = {};

    function structuredCloneSafe(obj) {
      return JSON.parse(JSON.stringify(obj));
    }

    function $(id) {
      return document.getElementById(id);
    }

    function init() {
      cacheEls();
      canvas = els.prompterCanvas;
      ctx = canvas.getContext("2d", { alpha: true });

      loadState();
      renderSetup();
      wireUI();
      updateBindingButtons();
    }

    function cacheEls() {
      [
        "startPromptBtn",
        "clearSettingsBtn",
        "flipHBtn",
        "flipVBtn",

        "textSizeInput",
        "textSizeLabel",
        "indentInput",
        "indentLabel",
        "textColorInput",
        "bgColorInput",
        "bgOpacityInput",

        "focusEnabledInput",
        "focusHeightInput",
        "focusHeightLabel",
        "focusOffsetInput",
        "focusOffsetLabel",
        "focusColorInput",
        "focusOpacityInput",

        "startSpeedInput",
        "startSpeedLabel",
        "presenterPresetBtn",
        "resetBindingsBtn",
        "singleButtonFallbackInput",
        "captureStatus",

        "fileNameInput",
        "scriptInput",
        "importBtn",
        "fileInput",
        "exportTxtBtn",
        "exportRtfBtn",

        "prompterPage",
        "canvasStage",
        "prompterCanvas",
        "speedHud",
        "controlsHud",
        "closePromptBtn",
        "mobileExitPromptBtn",
        "tapExitPromptBtn"
      ].forEach(id => {
        els[id] = $(id);
      });
    }

    function loadState() {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (!raw) return;

      try {
        const saved = JSON.parse(raw);
        state = mergeState(defaultState, saved);
      } catch (err) {
        console.warn("Saved settings were corrupted and have been reset.", err);
        localStorage.removeItem(STORAGE_KEY);
        state = structuredCloneSafe(defaultState);
      }
    }

    function mergeState(base, saved) {
      const out = structuredCloneSafe(base);

      if (!saved || typeof saved !== "object") return out;

      for (const key of Object.keys(out)) {
        if (key === "bindings") continue;
        if (Object.prototype.hasOwnProperty.call(saved, key)) {
          out[key] = saved[key];
        }
      }

      out.bindings = structuredCloneSafe(base.bindings);

      if (saved.bindings && typeof saved.bindings === "object") {
        for (const action of Object.keys(out.bindings)) {
          if (saved.bindings[action] && typeof saved.bindings[action] === "object") {
            const normalised = normaliseSignature(saved.bindings[action]);

            if (!isAmbiguousPointerButton0(normalised)) {
              out.bindings[action] = normalised;
            }
          }
        }
      }

      out.textSize = clampNumber(out.textSize, 18, 140, base.textSize);
      out.indent = clampNumber(out.indent, 0, 45, base.indent);
      out.bgOpacity = clampNumber(out.bgOpacity, 0, 100, base.bgOpacity);
      out.focusHeight = clampNumber(out.focusHeight, 30, 320, base.focusHeight);
      out.focusOffset = clampNumber(out.focusOffset, 5, 95, base.focusOffset);
      out.focusOpacity = clampNumber(out.focusOpacity, 0, 100, base.focusOpacity);
      out.startSpeed = clampNumber(out.startSpeed, -10, 25, base.startSpeed);
      out.singleButtonFallback = !!out.singleButtonFallback;

      return out;
    }

    function clampNumber(value, min, max, fallback) {
      const n = Number(value);
      if (!Number.isFinite(n)) return fallback;
      return Math.max(min, Math.min(max, n));
    }

    function saveState() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
    }

    function renderSetup() {
      els.scriptInput.value = state.text;
      els.fileNameInput.value = state.fileName;

      els.textSizeInput.value = state.textSize;
      els.textSizeLabel.textContent = state.textSize + "px";

      els.indentInput.value = state.indent;
      els.indentLabel.textContent = state.indent + "%";

      els.textColorInput.value = validHex(state.textColor, "#ffffff");
      els.bgColorInput.value = validHex(state.bgColor, "#000000");
      els.bgOpacityInput.value = state.bgOpacity;

      els.focusEnabledInput.checked = !!state.focusEnabled;
      els.focusHeightInput.value = state.focusHeight;
      els.focusHeightLabel.textContent = state.focusHeight + "px";
      els.focusOffsetInput.value = state.focusOffset;
      els.focusOffsetLabel.textContent = state.focusOffset + "%";
      els.focusColorInput.value = validHex(state.focusColor, "#10b981");
      els.focusOpacityInput.value = state.focusOpacity;

      els.startSpeedInput.value = state.startSpeed;
      els.startSpeedLabel.textContent = state.startSpeed;

      els.singleButtonFallbackInput.checked = !!state.singleButtonFallback;

      updateFlipButtons();
    }

    function wireUI() {
      els.startPromptBtn.addEventListener("click", launchPrompter);

      els.closePromptBtn.addEventListener("click", () => exitPrompter(true));
      els.mobileExitPromptBtn.addEventListener("click", () => exitPrompter(true));
      els.tapExitPromptBtn.addEventListener("click", () => exitPrompter(true));

      els.clearSettingsBtn.addEventListener("click", () => {
        localStorage.removeItem(STORAGE_KEY);
        state = structuredCloneSafe(defaultState);
        renderSetup();
        updateBindingButtons();
        alert("Saved settings cleared.");
      });

      els.flipHBtn.addEventListener("click", () => {
        state.flipHorizontal = !state.flipHorizontal;
        saveState();
        updateFlipButtons();
      });

      els.flipVBtn.addEventListener("click", () => {
        state.flipVertical = !state.flipVertical;
        saveState();
        updateFlipButtons();
      });

      bindRange(els.textSizeInput, "textSize", els.textSizeLabel, "px");
      bindRange(els.indentInput, "indent", els.indentLabel, "%");
      bindRange(els.focusHeightInput, "focusHeight", els.focusHeightLabel, "px");
      bindRange(els.focusOffsetInput, "focusOffset", els.focusOffsetLabel, "%");
      bindRange(els.startSpeedInput, "startSpeed", els.startSpeedLabel, "");

      els.textColorInput.addEventListener("input", () => setVal("textColor", els.textColorInput.value));
      els.bgColorInput.addEventListener("input", () => setVal("bgColor", els.bgColorInput.value));

      els.bgOpacityInput.addEventListener("input", () => {
        setVal("bgOpacity", clampNumber(els.bgOpacityInput.value, 0, 100, 100));
      });

      els.focusEnabledInput.addEventListener("change", () => setVal("focusEnabled", els.focusEnabledInput.checked));
      els.focusColorInput.addEventListener("input", () => setVal("focusColor", els.focusColorInput.value));

      els.focusOpacityInput.addEventListener("input", () => {
        setVal("focusOpacity", clampNumber(els.focusOpacityInput.value, 0, 100, 25));
      });

      els.singleButtonFallbackInput.addEventListener("change", () => {
        state.singleButtonFallback = els.singleButtonFallbackInput.checked;
        saveState();
      });

      els.fileNameInput.addEventListener("input", () => {
        setVal("fileName", els.fileNameInput.value || "teleprompter-script.txt");
      });

      els.scriptInput.addEventListener("input", () => {
        setVal("text", els.scriptInput.value);
      });

      els.presenterPresetBtn.addEventListener("click", () => {
        applyPresenterPreset();
        alert(
          "E2 presenter preset saved.\n\n" +
          "Use E2 PageUp/PageDown mode first.\n" +
          "If that fails, try Left/Right mode.\n\n" +
          "Return/Back should exit prompt mode if the browser exposes it.\n\n" +
          "If both page buttons appear as Button 0, enable Single-button fallback."
        );
      });

      els.resetBindingsBtn.addEventListener("click", () => {
        state.bindings = structuredCloneSafe(defaultState.bindings);
        state.presenterMode = false;
        saveState();
        updateBindingButtons();
      });

      document.querySelectorAll(".bindBtn").forEach(btn => {
        btn.addEventListener("click", () => beginCapture(btn.dataset.bind));
      });

      els.importBtn.addEventListener("click", () => els.fileInput.click());
      els.fileInput.addEventListener("change", importFile);

      els.exportTxtBtn.addEventListener("click", () => exportFile("txt"));
      els.exportRtfBtn.addEventListener("click", () => exportFile("rtf"));

      window.addEventListener("keydown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("keyup", handleInputEvent, { capture: true, passive: false });

      window.addEventListener("pointerdown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("pointerup", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("mousedown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("mouseup", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("click", handleInputEvent, { capture: true, passive: false });

      window.addEventListener("touchmove", handleTouchMoveLock, { capture: true, passive: false });

      window.addEventListener("resize", () => {
        if (!els.prompterPage.classList.contains("hidden")) {
          prepareCanvasPrompter();
          drawCanvasFrame();
        }
      });

      window.addEventListener("popstate", () => {
        const isPrompting = !els.prompterPage.classList.contains("hidden");
        if (isPrompting) {
          exitPrompter(false);
        }
      });
    }

    function bindRange(input, key, label, suffix) {
      input.addEventListener("input", () => {
        const n = Number(input.value);
        state[key] = n;
        label.textContent = n + suffix;
        saveState();
      });
    }

    function setVal(key, val) {
      state[key] = val;
      saveState();
    }

    function validHex(hex, fallback) {
      return /^#[0-9a-fA-F]{6}$/.test(hex) ? hex : fallback;
    }

    function updateFlipButtons() {
      toggleActiveButton(els.flipHBtn, state.flipHorizontal);
      toggleActiveButton(els.flipVBtn, state.flipVertical);
    }

    function toggleActiveButton(btn, active) {
      btn.classList.toggle("border-blue-400", active);
      btn.classList.toggle("bg-blue-950", active);
      btn.classList.toggle("text-blue-300", active);
    }

    function applyPresenterPreset() {
      state.presenterMode = true;

      state.bindings.speedUp = {
        code: "PageDown",
        key: "PageDown",
        keyCode: 34,
        which: 34,
        aliases: [
          "PageDown",
          "ArrowRight",
          "Enter",
          "NumpadEnter",
          "MediaTrackNext",
          "MediaNextTrack",
          "BrowserForward"
        ]
      };

      state.bindings.speedDown = {
        code: "PageUp",
        key: "PageUp",
        keyCode: 33,
        which: 33,
        aliases: [
          "PageUp",
          "ArrowLeft",
          "MediaTrackPrevious",
          "MediaPreviousTrack"
        ]
      };

      state.bindings.exit = {
        code: "Escape",
        key: "Escape",
        keyCode: 27,
        which: 27,
        aliases: [
          "Escape",
          "BrowserBack",
          "GoBack",
          "Backspace",
          "Back",
          "SoftLeft"
        ]
      };

      saveState();
      updateBindingButtons();
    }

    function beginCapture(action) {
      if (!action || !state.bindings[action]) return;

      captureAction = action;

      document.querySelectorAll(".bindBtn").forEach(btn => {
        btn.classList.remove("kbd-capturing");
      });

      const btn = document.querySelector(`.bindBtn[data-bind="${action}"]`);

      if (btn) {
        btn.classList.add("kbd-capturing");
        btn.textContent = "Press input...";
      }

      els.captureStatus.classList.remove("hidden");
    }

    function cancelCapture() {
      captureAction = null;
      els.captureStatus.classList.add("hidden");

      document.querySelectorAll(".bindBtn").forEach(btn => {
        btn.classList.remove("kbd-capturing");
      });

      updateBindingButtons();
    }

    function handleInputEvent(e) {
      const sig = eventToSignature(e);
      const isPrompting = !els.prompterPage.classList.contains("hidden");

      if (captureAction) {
        if (
          e.type === "keydown" &&
          (e.key === "Escape" || e.code === "Escape") &&
          captureAction !== "exit"
        ) {
          e.preventDefault();
          e.stopPropagation();
          cancelCapture();
          return;
        }

        if (isAmbiguousPointerEvent(sig)) {
          e.preventDefault();
          e.stopPropagation();
          alert(
            "This button is being reported only as Button 0, which is ambiguous because both E2 page buttons can send the same value.\n\n" +
            "Please switch the E2 remote to PageUp/PageDown mode or Left/Right mode, then try binding again.\n\n" +
            "Alternatively enable Single-button fallback."
          );
          cancelCapture();
          return;
        }

        if (isUsableSignature(sig)) {
          e.preventDefault();
          e.stopPropagation();

          const normalised = normaliseSignature(sig);

          if (isAmbiguousPointerButton0(normalised)) {
            alert("Button 0 was not saved because it cannot distinguish between the two page buttons.");
            cancelCapture();
            return;
          }

          state.bindings[captureAction] = normalised;

          if (captureAction === "speedUp" || captureAction === "speedDown") {
            state.presenterMode = false;
          }

          saveState();

          captureAction = null;

          els.captureStatus.classList.add("hidden");

          document.querySelectorAll(".bindBtn").forEach(btn => {
            btn.classList.remove("kbd-capturing");
          });

          updateBindingButtons();
        }

        return;
      }

      if (!isPrompting) return;

      if (shouldBlockDefaultInPrompter(e)) {
        e.preventDefault();
        e.stopPropagation();
      }

      if (state.singleButtonFallback && isPointerPrimaryDown(sig)) {
        e.preventDefault();
        e.stopPropagation();
        handleSingleButtonPointerDown();
        return;
      }

      if (state.singleButtonFallback && isPointerPrimaryUp(sig)) {
        e.preventDefault();
        e.stopPropagation();
        handleSingleButtonPointerUp();
        return;
      }

      if (e.type === "keyup" || e.type === "mouseup" || e.type === "pointerup" || e.type === "click") return;

      if (state.presenterMode && isPresenterExit(sig)) {
        exitPrompter(true);
        return;
      }

      if (state.presenterMode && isPresenterNext(sig)) {
        changeSpeed(0.5);
        return;
      }

      if (state.presenterMode && isPresenterPrevious(sig)) {
        changeSpeed(-0.5);
        return;
      }

      if (matchesSignature(sig, state.bindings.exit)) {
        exitPrompter(true);
        return;
      }

      if (matchesSignature(sig, state.bindings.toggle)) {
        togglePlay();
        return;
      }

      if (matchesSignature(sig, state.bindings.reset)) {
        resetPrompt();
        return;
      }

      if (matchesSignature(sig, state.bindings.speedUp)) {
        changeSpeed(0.5);
        return;
      }

      if (matchesSignature(sig, state.bindings.speedDown)) {
        changeSpeed(-0.5);
      }
    }

    function handleTouchMoveLock(e) {
      const isPrompting = !els.prompterPage.classList.contains("hidden");
      if (isPrompting) {
        e.preventDefault();
        e.stopPropagation();
      }
    }

    function handleSingleButtonPointerDown() {
      pointerDownAt = performance.now();

      clearTimeout(pointerLongPressTimer);
      pointerLongPressTimer = setTimeout(() => {
        pointerLongPressTimer = null;
        exitPrompter(true);
      }, 850);
    }

    function handleSingleButtonPointerUp() {
      const now = performance.now();
      const heldFor = now - pointerDownAt;

      if (pointerLongPressTimer) {
        clearTimeout(pointerLongPressTimer);
        pointerLongPressTimer = null;
      } else {
        return;
      }

      if (heldFor >= 800) return;

      const sinceLast = now - lastPointerClickTime;
      lastPointerClickTime = now;

      if (sinceLast < 420) {
        changeSpeed(-0.5);
      } else {
        changeSpeed(0.5);
      }
    }

    function eventToSignature(e) {
      return {
        type: e.type || "",
        code: e.code || "",
        key: e.key || "",
        keyCode: Number.isFinite(e.keyCode) ? e.keyCode : null,
        which: Number.isFinite(e.which) ? e.which : null,
        button: Number.isFinite(e.button) ? e.button : null,
        buttons: Number.isFinite(e.buttons) ? e.buttons : null,
        pointerType: e.pointerType || "",
        detail: Number.isFinite(e.detail) ? e.detail : null
      };
    }

    function isPointerEventType(type) {
      return (
        type === "click" ||
        type === "mousedown" ||
        type === "mouseup" ||
        type === "pointerdown" ||
        type === "pointerup"
      );
    }

    function isPointerPrimaryDown(sig) {
      return (
        (sig.type === "pointerdown" || sig.type === "mousedown") &&
        (sig.button === 0 || sig.button === null)
      );
    }

    function isPointerPrimaryUp(sig) {
      return (
        (sig.type === "pointerup" || sig.type === "mouseup") &&
        (sig.button === 0 || sig.button === null)
      );
    }

    function isAmbiguousPointerEvent(sig) {
      return isPointerEventType(sig.type) && (sig.button === 0 || sig.button === null) && !sig.code && !sig.key;
    }

    function isAmbiguousPointerButton0(sig) {
      return sig && sig.button === 0 && !sig.code && !sig.key && !sig.keyCode && !sig.which;
    }

    function isUsableSignature(sig) {
      if (isAmbiguousPointerEvent(sig)) return false;

      return !!(
        sig.code ||
        sig.key ||
        sig.keyCode ||
        sig.which ||
        (sig.button !== null && sig.button !== 0)
      );
    }

    function normaliseSignature(sig) {
      const out = {};

      if (sig.code) out.code = sig.code;
      if (sig.key) out.key = sig.key;
      if (sig.keyCode) out.keyCode = sig.keyCode;
      if (sig.which) out.which = sig.which;

      if (sig.button !== null && sig.button !== undefined && sig.button !== 0) {
        out.button = sig.button;
      }

      if (sig.pointerType && sig.button !== 0) {
        out.pointerType = sig.pointerType;
      }

      if (out.code === "Space" || out.key === " ") {
        out.code = "Space";
        out.key = " ";
        out.keyCode = 32;
        out.which = 32;
      }

      return out;
    }

    function matchesSignature(inputSig, bindingSig) {
      if (!bindingSig) return false;

      const checks = ["code", "key", "keyCode", "which", "button", "pointerType"];

      const directMatch = checks.some(k => {
        return (
          bindingSig[k] !== undefined &&
          bindingSig[k] !== null &&
          bindingSig[k] !== "" &&
          inputSig[k] === bindingSig[k]
        );
      });

      if (directMatch) return true;

      if (Array.isArray(bindingSig.aliases)) {
        return bindingSig.aliases.some(alias => {
          return (
            inputSig.code === alias ||
            inputSig.key === alias ||
            String(inputSig.keyCode) === String(alias) ||
            String(inputSig.which) === String(alias)
          );
        });
      }

      return false;
    }

    function isPresenterNext(sig) {
      return (
        sig.code === "PageDown" ||
        sig.key === "PageDown" ||
        sig.keyCode === 34 ||
        sig.which === 34 ||

        sig.code === "ArrowRight" ||
        sig.key === "ArrowRight" ||
        sig.keyCode === 39 ||
        sig.which === 39 ||

        sig.code === "Enter" ||
        sig.key === "Enter" ||
        sig.code === "NumpadEnter" ||
        sig.keyCode === 13 ||
        sig.which === 13 ||

        sig.code === "MediaTrackNext" ||
        sig.key === "MediaTrackNext" ||
        sig.code === "MediaNextTrack" ||
        sig.key === "MediaNextTrack" ||

        sig.code === "BrowserForward" ||
        sig.key === "BrowserForward"
      );
    }

    function isPresenterPrevious(sig) {
      return (
        sig.code === "PageUp" ||
        sig.key === "PageUp" ||
        sig.keyCode === 33 ||
        sig.which === 33 ||

        sig.code === "ArrowLeft" ||
        sig.key === "ArrowLeft" ||
        sig.keyCode === 37 ||
        sig.which === 37 ||

        sig.code === "MediaTrackPrevious" ||
        sig.key === "MediaTrackPrevious" ||
        sig.code === "MediaPreviousTrack" ||
        sig.key === "MediaPreviousTrack"
      );
    }

    function isPresenterExit(sig) {
      return (
        sig.code === "Escape" ||
        sig.key === "Escape" ||
        sig.keyCode === 27 ||
        sig.which === 27 ||

        sig.code === "BrowserBack" ||
        sig.key === "BrowserBack" ||

        sig.code === "Backspace" ||
        sig.key === "Backspace" ||
        sig.keyCode === 8 ||
        sig.which === 8 ||

        sig.key === "Back" ||
        sig.code === "GoBack" ||
        sig.key === "GoBack" ||
        sig.key === "SoftLeft"
      );
    }

    function shouldBlockDefaultInPrompter(e) {
      const navKeys = new Set([
        "PageUp",
        "PageDown",
        "ArrowUp",
        "ArrowDown",
        "ArrowLeft",
        "ArrowRight",
        " ",
        "Spacebar",
        "Space",
        "Home",
        "End",
        "Enter",
        "NumpadEnter",
        "Escape",
        "BrowserBack",
        "BrowserForward",
        "Backspace",
        "Back",
        "GoBack",
        "MediaTrackNext",
        "MediaNextTrack",
        "MediaTrackPrevious",
        "MediaPreviousTrack"
      ]);

      return (
        navKeys.has(e.key) ||
        navKeys.has(e.code) ||
        e.keyCode === 33 ||
        e.keyCode === 34 ||
        e.keyCode === 32 ||
        e.keyCode === 38 ||
        e.keyCode === 40 ||
        e.keyCode === 37 ||
        e.keyCode === 39 ||
        e.keyCode === 13 ||
        e.keyCode === 27 ||
        e.keyCode === 8
      );
    }

    function bindingLabel(sig) {
      if (!sig) return "Unbound";
      if (sig.code === "Space" || sig.key === " ") return "Space";
      if (sig.code) return sig.code;
      if (sig.key) return sig.key;
      if (sig.keyCode) return "KeyCode " + sig.keyCode;
      if (sig.button !== undefined && sig.button !== null) return "Button " + sig.button;
      return "Custom";
    }

    function updateBindingButtons() {
      document.querySelectorAll(".bindBtn").forEach(btn => {
        const action = btn.dataset.bind;
        btn.textContent = bindingLabel(state.bindings[action]);
      });
    }

    function lockBodyScroll() {
      savedBodyScrollY = window.scrollY || document.documentElement.scrollTop || 0;
      document.body.style.top = `-${savedBodyScrollY}px`;
      document.body.classList.add("prompt-lock");
    }

    function unlockBodyScroll() {
      document.body.classList.remove("prompt-lock");
      document.body.style.top = "";
      window.scrollTo(0, savedBodyScrollY);
    }

    function launchPrompter() {
      lockBodyScroll();

      currentSpeed = 0;
      isScrolling = false;
      scrollPos = 0;

      els.prompterPage.classList.remove("hidden");

      prepareCanvasPrompter();
      drawCanvasFrame();
      updateHud(true);

      els.controlsHud.textContent =
        `${bindingLabel(state.bindings.speedUp)} up | ` +
        `${bindingLabel(state.bindings.speedDown)} down | ` +
        `${bindingLabel(state.bindings.toggle)} pause`;

      if (!history.state || !history.state.prompterOpen) {
        history.pushState({ prompterOpen: true }, "");
        prompterHistoryActive = true;
      }

      setTimeout(() => {
        try {
          els.canvasStage.focus({ preventScroll: true });
        } catch (err) {
          els.canvasStage.focus();
        }
      }, 50);

      if (rafId) cancelAnimationFrame(rafId);
      lastTime = performance.now();
      lastHudUpdate = 0;
      rafId = requestAnimationFrame(canvasTick);
    }

    function exitPrompter(adjustHistory = true) {
      if (rafId) cancelAnimationFrame(rafId);

      rafId = null;
      isScrolling = false;
      currentSpeed = 0;
      els.prompterPage.classList.add("hidden");

      unlockBodyScroll();

      if (adjustHistory && prompterHistoryActive && history.state && history.state.prompterOpen) {
        prompterHistoryActive = false;
        history.back();
      } else {
        prompterHistoryActive = false;
      }
    }

    function prepareCanvasPrompter() {
      canvasCssWidth = window.innerWidth;
      canvasCssHeight = window.innerHeight;
      canvasDpr = Math.max(1, Math.min(2, window.devicePixelRatio || 1));

      canvas.width = Math.round(canvasCssWidth * canvasDpr);
      canvas.height = Math.round(canvasCssHeight * canvasDpr);
      canvas.style.width = canvasCssWidth + "px";
      canvas.style.height = canvasCssHeight + "px";

      ctx.setTransform(canvasDpr, 0, 0, canvasDpr, 0, 0);
      ctx.imageSmoothingEnabled = false;

      topPadding = canvasCssHeight * 0.65;
      bottomPadding = canvasCssHeight * 0.65;

      textAreaLeft = canvasCssWidth * (state.indent / 100);
      textAreaWidth = Math.max(100, canvasCssWidth - textAreaLeft * 2);

      lineHeight = Math.round(state.textSize * 1.5);

      ctx.font = `${state.textSize}px Arial, Helvetica, sans-serif`;
      ctx.textBaseline = "top";

      renderLines = wrapTextForCanvas(state.text || "", textAreaWidth);
      contentHeight = topPadding + renderLines.length * lineHeight + bottomPadding;
      maxScroll = Math.max(0, contentHeight - canvasCssHeight);
    }

    function wrapTextForCanvas(text, maxWidth) {
      const output = [];
      const paragraphs = String(text).replace(/\r\n/g, "\n").replace(/\r/g, "\n").split("\n");

      for (const paragraph of paragraphs) {
        if (paragraph.trim() === "") {
          output.push("");
          continue;
        }

        const words = paragraph.split(/\s+/);
        let line = "";

        for (const word of words) {
          const test = line ? line + " " + word : word;
          const width = ctx.measureText(test).width;

          if (width <= maxWidth || !line) {
            line = test;
          } else {
            output.push(line);
            line = word;
          }
        }

        if (line) output.push(line);
      }

      return output;
    }

    function canvasTick(now) {
      const dt = Math.min((now - lastTime) / 1000, 0.05);
      lastTime = now;

      if (isScrolling && currentSpeed !== 0) {
        scrollPos += currentSpeed * pixelsPerSecondPerSpeedUnit * dt;

        if (scrollPos < 0) {
          scrollPos = 0;
          isScrolling = false;
          currentSpeed = 0;
        }

        if (scrollPos > maxScroll) {
          scrollPos = maxScroll;
          isScrolling = false;
          currentSpeed = 0;
        }
      }

      drawCanvasFrame();

      if (now - lastHudUpdate > 120) {
        updateHud(true);
        lastHudUpdate = now;
      }

      rafId = requestAnimationFrame(canvasTick);
    }

    function drawCanvasFrame() {
      ctx.save();

      // Reset to CSS pixel coordinate space.
      ctx.setTransform(canvasDpr, 0, 0, canvasDpr, 0, 0);

      const bg = hexToRgb(state.bgColor);
      ctx.clearRect(0, 0, canvasCssWidth, canvasCssHeight);
      ctx.fillStyle = `rgba(${bg.r}, ${bg.g}, ${bg.b}, ${state.bgOpacity / 100})`;
      ctx.fillRect(0, 0, canvasCssWidth, canvasCssHeight);

      // Apply mirror/flip around screen centre.
      ctx.translate(canvasCssWidth / 2, canvasCssHeight / 2);
      ctx.scale(state.flipHorizontal ? -1 : 1, state.flipVertical ? -1 : 1);
      ctx.translate(-canvasCssWidth / 2, -canvasCssHeight / 2);

      ctx.font = `${state.textSize}px Arial, Helvetica, sans-serif`;
      ctx.textBaseline = "top";
      ctx.fillStyle = state.textColor;

      const firstLineIndex = Math.max(0, Math.floor((scrollPos - topPadding) / lineHeight) - 2);
      const lastLineIndex = Math.min(
        renderLines.length - 1,
        Math.ceil((scrollPos + canvasCssHeight - topPadding) / lineHeight) + 2
      );

      for (let i = firstLineIndex; i <= lastLineIndex; i++) {
        const y = topPadding + i * lineHeight - scrollPos;
        ctx.fillText(renderLines[i], textAreaLeft, y);
      }

      ctx.restore();

      if (state.focusEnabled) {
        drawFocusBar();
      }
    }

    function drawFocusBar() {
      const fc = hexToRgb(state.focusColor);
      const barH = state.focusHeight;
      const y = canvasCssHeight * (state.focusOffset / 100) - barH / 2;

      ctx.save();
      ctx.setTransform(canvasDpr, 0, 0, canvasDpr, 0, 0);
      ctx.fillStyle = `rgba(${fc.r}, ${fc.g}, ${fc.b}, ${state.focusOpacity / 100})`;
      ctx.fillRect(0, y, canvasCssWidth, barH);

      ctx.strokeStyle = `rgba(${fc.r}, ${fc.g}, ${fc.b}, ${Math.min(1, (state.focusOpacity + 20) / 100)})`;
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(0, y);
      ctx.lineTo(canvasCssWidth, y);
      ctx.moveTo(0, y + barH);
      ctx.lineTo(canvasCssWidth, y + barH);
      ctx.stroke();

      ctx.restore();
    }

    function togglePlay() {
      isScrolling = !isScrolling;

      if (isScrolling && currentSpeed === 0) {
        currentSpeed = Number(state.startSpeed) || 1;
      }

      updateHud(true);
    }

    function changeSpeed(delta) {
      if (!isScrolling) isScrolling = true;

      currentSpeed = Math.round((currentSpeed + delta) * 10) / 10;
      currentSpeed = Math.max(-30, Math.min(60, currentSpeed));

      updateHud(true);
    }

    function resetPrompt() {
      scrollPos = 0;
      isScrolling = false;
      currentSpeed = 0;
      drawCanvasFrame();
      updateHud(true);
    }

    function updateHud(force = false) {
      if (!force && performance.now() - lastHudUpdate < 120) return;

      els.speedHud.textContent = isScrolling
        ? currentSpeed.toFixed(1)
        : `PAUSED ${currentSpeed.toFixed(1)}`;

      els.speedHud.classList.toggle("text-amber-400", currentSpeed < 0);
      els.speedHud.classList.toggle("text-blue-400", currentSpeed >= 0);

      lastHudUpdate = performance.now();
    }

    function hexToRgb(hex) {
      const safe = validHex(hex, "#000000");

      return {
        r: parseInt(safe.slice(1, 3), 16),
        g: parseInt(safe.slice(3, 5), 16),
        b: parseInt(safe.slice(5, 7), 16)
      };
    }

    function importFile() {
      const file = els.fileInput.files && els.fileInput.files[0];

      if (!file) return;

      const reader = new FileReader();

      reader.onload = () => {
        let text = String(reader.result || "");

        if (file.name.toLowerCase().endsWith(".rtf")) {
          text = basicRtfToText(text);
        }

        state.text = text;
        state.fileName = file.name;

        saveState();

        els.scriptInput.value = state.text;
        els.fileNameInput.value = state.fileName;
      };

      reader.readAsText(file);
    }

    function exportFile(type) {
      const baseName = (state.fileName || "teleprompter-script").replace(/\.[^/.]+$/, "");

      let fileName = baseName + "." + type;
      let content = state.text || "";
      let mime = "text/plain";

      if (type === "rtf") {
        mime = "application/rtf";
        content = textToRtf(content);
      }

      const blob = new Blob([content], { type: mime });
      const url = URL.createObjectURL(blob);

      const a = document.createElement("a");
      a.href = url;
      a.download = fileName;

      document.body.appendChild(a);
      a.click();
      a.remove();

      URL.revokeObjectURL(url);
    }

    function textToRtf(text) {
      const escaped = String(text)
        .replace(/\\/g, "\\\\")
        .replace(/{/g, "\\{")
        .replace(/}/g, "\\}")
        .replace(/\n/g, "\\par\n");

      return "{\\rtf1\\ansi\\deff0{\\fonttbl{\\f0 Arial;}}\\f0\\fs28 " + escaped + "}";
    }

    function basicRtfToText(rtf) {
      return String(rtf)
        .replace(/\\par[d]?/g, "\n")
        .replace(/\\'[0-9a-fA-F]{2}/g, "")
        .replace(/\\[a-zA-Z]+-?\d* ?/g, "")
        .replace(/[{}]/g, "")
        .trim();
    }

    document.addEventListener("DOMContentLoaded", init);
  </script>
</body>
</html>
