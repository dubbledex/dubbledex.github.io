<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Web Teleprompter</title>

  <script src="https://cdn.tailwindcss.com"></script>

  <style>
    html, body {
      min-height: 100%;
    }

    input[type="color"] {
      -webkit-appearance: none;
      border: none;
      padding: 0;
      overflow: hidden;
    }

    #prompterFrame {
      overscroll-behavior: none;
      touch-action: none;
    }

    .kbd-capturing {
      outline: 3px solid #fb7185;
      background: rgba(127, 29, 29, 0.65) !important;
      color: #fecdd3 !important;
    }
  </style>
</head>

<body class="bg-slate-950 text-slate-100">
  <!-- Setup page -->
  <main id="setupPage" class="max-w-7xl mx-auto p-4 md:p-8">
    <header class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 border-b border-slate-700 pb-5 mb-6">
      <div>
        <h1 class="text-3xl font-bold">Web Teleprompter</h1>
        <p class="text-slate-400 text-sm mt-1">Browser-only teleprompter with custom keyboard and Bluetooth presenter controls.</p>
      </div>

      <div class="flex flex-col sm:flex-row gap-2">
        <button id="clearSettingsBtn" type="button" class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold">
          Clear Saved Settings
        </button>
        <button id="startPromptBtn" type="button" class="px-6 py-3 rounded-xl bg-blue-600 hover:bg-blue-500 font-bold shadow-lg">
          Start Prompting
        </button>
      </div>
    </header>

    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
      <!-- Settings -->
      <section class="lg:col-span-5 space-y-6">
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <h2 class="text-xl font-bold mb-4">Display Settings</h2>

          <div class="grid grid-cols-2 gap-3 mb-5">
            <button id="flipHBtn" type="button" class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold">
              Mirror horizontal
            </button>
            <button id="flipVBtn" type="button" class="px-4 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold">
              Flip vertical
            </button>
          </div>

          <div class="space-y-5">
            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Text size</span>
                <strong id="textSizeLabel" class="text-blue-400">48px</strong>
              </div>
              <input id="textSizeInput" type="range" min="18" max="140" value="48" class="w-full accent-blue-500">
            </label>

            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Side indent / margins</span>
                <strong id="indentLabel" class="text-blue-400">15%</strong>
              </div>
              <input id="indentInput" type="range" min="0" max="45" value="15" class="w-full accent-blue-500">
            </label>

            <div class="grid grid-cols-3 gap-3">
              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Text colour</span>
                <input id="textColorInput" type="color" value="#ffffff" class="w-full h-11 rounded-lg bg-slate-800">
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Background</span>
                <input id="bgColorInput" type="color" value="#000000" class="w-full h-11 rounded-lg bg-slate-800">
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Opacity %</span>
                <input id="bgOpacityInput" type="number" min="0" max="100" value="100" class="w-full h-11 rounded-lg bg-slate-800 border border-slate-700 px-3">
              </label>
            </div>
          </div>
        </div>

        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <div class="flex items-center justify-between mb-4">
            <h2 class="text-xl font-bold">Focus Bar</h2>
            <label class="flex items-center gap-2 text-sm">
              <input id="focusEnabledInput" type="checkbox" class="accent-emerald-500" checked>
              Enabled
            </label>
          </div>

          <div class="space-y-5">
            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Bar height</span>
                <strong id="focusHeightLabel" class="text-emerald-400">90px</strong>
              </div>
              <input id="focusHeightInput" type="range" min="30" max="320" value="90" class="w-full accent-emerald-500">
            </label>

            <label class="block">
              <div class="flex justify-between text-sm mb-1">
                <span>Vertical position</span>
                <strong id="focusOffsetLabel" class="text-emerald-400">35%</strong>
              </div>
              <input id="focusOffsetInput" type="range" min="5" max="95" value="35" class="w-full accent-emerald-500">
            </label>

            <div class="grid grid-cols-2 gap-3">
              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Bar colour</span>
                <input id="focusColorInput" type="color" value="#10b981" class="w-full h-11 rounded-lg bg-slate-800">
              </label>

              <label class="block text-sm">
                <span class="block mb-1 text-slate-400">Opacity %</span>
                <input id="focusOpacityInput" type="number" min="0" max="100" value="25" class="w-full h-11 rounded-lg bg-slate-800 border border-slate-700 px-3">
              </label>
            </div>
          </div>
        </div>

        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5">
          <h2 class="text-xl font-bold mb-4">Speed and Controls</h2>

          <label class="block mb-5">
            <div class="flex justify-between text-sm mb-1">
              <span>Default start speed</span>
              <strong id="startSpeedLabel" class="text-violet-400">4</strong>
            </div>
            <input id="startSpeedInput" type="range" min="-10" max="25" value="4" class="w-full accent-violet-500">
          </label>

          <div class="flex flex-wrap gap-2 mb-4">
            <button id="presenterPresetBtn" type="button" class="px-3 py-2 rounded-lg bg-emerald-700 hover:bg-emerald-600 font-semibold text-sm">
              Presenter preset: PageDown/PageUp
            </button>
            <button id="resetBindingsBtn" type="button" class="px-3 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold text-sm">
              Reset keybindings
            </button>
          </div>

          <p id="captureStatus" class="hidden mb-4 p-3 rounded-xl bg-rose-950 border border-rose-700 text-rose-100 text-sm">
            Capturing input. Press one keyboard or presenter button now. Press Escape to cancel.
          </p>

          <div class="space-y-2 text-sm">
            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Speed up</span>
              <button data-bind="speedUp" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">ArrowUp</button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Slow down / reverse</span>
              <button data-bind="speedDown" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">ArrowDown</button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Start / stop</span>
              <button data-bind="toggle" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">Space</button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Reset to start</span>
              <button data-bind="reset" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">KeyR</button>
            </div>

            <div class="flex items-center justify-between gap-3 bg-slate-950 border border-slate-700 rounded-xl p-3">
              <span>Exit prompter</span>
              <button data-bind="exit" type="button" class="bindBtn px-3 py-2 rounded-lg bg-violet-900 hover:bg-violet-800 font-mono min-w-32">Escape</button>
            </div>
          </div>

          <div class="mt-4 p-3 rounded-xl bg-slate-950 border border-slate-700 text-xs text-slate-400">
            <strong class="text-slate-200">Bluetooth troubleshooting:</strong>
            if your clicker still does not bind, try the preset first. Some Android browsers expose presenter buttons as PageUp/PageDown only during prompt mode, not setup mode.
          </div>
        </div>
      </section>

      <!-- Script editor -->
      <section class="lg:col-span-7">
        <div class="bg-slate-900 border border-slate-700 rounded-2xl p-5 h-full flex flex-col">
          <div class="flex flex-col sm:flex-row sm:items-end sm:justify-between gap-3 mb-4">
            <label class="block">
              <span class="block text-sm text-slate-400 mb-1">File name</span>
              <input id="fileNameInput" type="text" value="teleprompter-script.txt" class="w-full sm:w-80 rounded-lg bg-slate-950 border border-slate-700 px-3 py-2">
            </label>

            <div class="flex flex-wrap gap-2">
              <button id="importBtn" type="button" class="px-3 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 font-semibold text-sm">Import</button>
              <input id="fileInput" type="file" accept=".txt,.rtf" class="hidden">
              <button id="exportTxtBtn" type="button" class="px-3 py-2 rounded-lg bg-blue-700 hover:bg-blue-600 font-semibold text-sm">Export TXT</button>
              <button id="exportRtfBtn" type="button" class="px-3 py-2 rounded-lg bg-blue-700 hover:bg-blue-600 font-semibold text-sm">Export RTF</button>
            </div>
          </div>

          <textarea id="scriptInput" class="flex-grow min-h-[520px] rounded-xl bg-slate-950 border border-slate-700 p-4 font-mono text-sm leading-relaxed focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
        </div>
      </section>
    </div>
  </main>

  <!-- Prompter page -->
  <section id="prompterPage" class="hidden fixed inset-0 z-50 overflow-hidden">
    <div id="focusBar" class="hidden fixed left-0 right-0 pointer-events-none z-20 border-y border-white/20"></div>

    <div id="prompterFrame" class="absolute inset-0 overflow-hidden">
      <div id="prompterTransform" class="min-h-full w-full">
        <div id="prompterText" class="whitespace-pre-wrap break-words pt-[65vh] pb-[65vh]"></div>
      </div>
    </div>

    <div id="hud" class="fixed left-1/2 bottom-4 -translate-x-1/2 bg-slate-950/90 border border-slate-700 rounded-2xl px-4 py-3 text-xs text-slate-200 opacity-0 hover:opacity-100 transition-opacity z-30">
      <span>Speed: <strong id="speedHud" class="text-blue-400">0</strong></span>
      <span class="mx-2 text-slate-600">|</span>
      <span id="controlsHud"></span>
      <button id="closePromptBtn" type="button" class="ml-3 text-red-400 font-bold">Close</button>
    </div>
  </section>

  <script>
    "use strict";

    const STORAGE_KEY = "teleprompter_pro_safe_v1";

    const defaultState = {
      text: "Welcome to the web teleprompter.\n\nPaste or type your script into the setup page.\n\nUse your configured keyboard or Bluetooth presenter controls to speed up, slow down, pause, reset, or exit.\n\nDefault controls:\n- Speed up: ArrowUp\n- Slow down / reverse: ArrowDown\n- Start / stop: Space\n- Reset: R\n- Exit: Escape\n",
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
      bindings: {
        speedUp: { code: "ArrowUp" },
        speedDown: { code: "ArrowDown" },
        toggle: { code: "Space", key: " " },
        reset: { code: "KeyR", key: "r" },
        exit: { code: "Escape", key: "Escape" }
      }
    };

    let state = structuredCloneSafe(defaultState);

    let currentSpeed = 0;
    let isScrolling = false;
    let scrollPos = 0;
    let rafId = null;
    let lastTime = 0;

    let captureAction = null;

    const els = {};

    function structuredCloneSafe(obj) {
      return JSON.parse(JSON.stringify(obj));
    }

    function $(id) {
      return document.getElementById(id);
    }

    function init() {
      cacheEls();
      loadState();
      renderSetup();
      wireUI();
      updateBindingButtons();
    }

    function cacheEls() {
      [
        "startPromptBtn", "clearSettingsBtn", "flipHBtn", "flipVBtn",
        "textSizeInput", "textSizeLabel", "indentInput", "indentLabel",
        "textColorInput", "bgColorInput", "bgOpacityInput",
        "focusEnabledInput", "focusHeightInput", "focusHeightLabel",
        "focusOffsetInput", "focusOffsetLabel", "focusColorInput", "focusOpacityInput",
        "startSpeedInput", "startSpeedLabel",
        "presenterPresetBtn", "resetBindingsBtn", "captureStatus",
        "fileNameInput", "scriptInput", "importBtn", "fileInput", "exportTxtBtn", "exportRtfBtn",
        "prompterPage", "prompterFrame", "prompterTransform", "prompterText",
        "focusBar", "speedHud", "controlsHud", "closePromptBtn"
      ].forEach(id => els[id] = $(id));
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

      for (const key of Object.keys(out)) {
        if (key === "bindings") continue;
        if (saved && Object.prototype.hasOwnProperty.call(saved, key)) {
          out[key] = saved[key];
        }
      }

      out.bindings = structuredCloneSafe(base.bindings);
      if (saved && saved.bindings && typeof saved.bindings === "object") {
        for (const action of Object.keys(out.bindings)) {
          if (saved.bindings[action] && typeof saved.bindings[action] === "object") {
            out.bindings[action] = normaliseSignature(saved.bindings[action]);
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

      els.textColorInput.value = state.textColor;
      els.bgColorInput.value = state.bgColor;
      els.bgOpacityInput.value = state.bgOpacity;

      els.focusEnabledInput.checked = !!state.focusEnabled;
      els.focusHeightInput.value = state.focusHeight;
      els.focusHeightLabel.textContent = state.focusHeight + "px";
      els.focusOffsetInput.value = state.focusOffset;
      els.focusOffsetLabel.textContent = state.focusOffset + "%";
      els.focusColorInput.value = state.focusColor;
      els.focusOpacityInput.value = state.focusOpacity;

      els.startSpeedInput.value = state.startSpeed;
      els.startSpeedLabel.textContent = state.startSpeed;

      updateFlipButtons();
    }

    function wireUI() {
      els.startPromptBtn.addEventListener("click", launchPrompter);
      els.closePromptBtn.addEventListener("click", exitPrompter);

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
      els.bgOpacityInput.addEventListener("input", () => setVal("bgOpacity", clampNumber(els.bgOpacityInput.value, 0, 100, 100)));
      els.focusEnabledInput.addEventListener("change", () => setVal("focusEnabled", els.focusEnabledInput.checked));
      els.focusColorInput.addEventListener("input", () => setVal("focusColor", els.focusColorInput.value));
      els.focusOpacityInput.addEventListener("input", () => setVal("focusOpacity", clampNumber(els.focusOpacityInput.value, 0, 100, 25)));

      els.fileNameInput.addEventListener("input", () => setVal("fileName", els.fileNameInput.value || "teleprompter-script.txt"));
      els.scriptInput.addEventListener("input", () => setVal("text", els.scriptInput.value));

      els.presenterPresetBtn.addEventListener("click", () => {
        state.bindings.speedUp = { code: "PageDown", key: "PageDown", keyCode: 34, which: 34 };
        state.bindings.speedDown = { code: "PageUp", key: "PageUp", keyCode: 33, which: 33 };
        saveState();
        updateBindingButtons();
      });

      els.resetBindingsBtn.addEventListener("click", () => {
        state.bindings = structuredCloneSafe(defaultState.bindings);
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

      // Capture and control listeners.
      window.addEventListener("keydown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("keyup", handleInputEvent, { capture: true, passive: false });

      // Some Bluetooth clickers on Android appear more like pointer/mouse buttons.
      window.addEventListener("pointerdown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("mousedown", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("mouseup", handleInputEvent, { capture: true, passive: false });
      window.addEventListener("click", handleInputEvent, { capture: true, passive: false });
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

    function updateFlipButtons() {
      toggleActiveButton(els.flipHBtn, state.flipHorizontal);
      toggleActiveButton(els.flipVBtn, state.flipVertical);
    }

    function toggleActiveButton(btn, active) {
      btn.classList.toggle("border-blue-400", active);
      btn.classList.toggle("bg-blue-950", active);
      btn.classList.toggle("text-blue-300", active);
    }

    function beginCapture(action) {
      if (!action || !state.bindings[action]) return;

      // Only one capture at a time.
      captureAction = action;

      document.querySelectorAll(".bindBtn").forEach(btn => btn.classList.remove("kbd-capturing"));

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
      document.querySelectorAll(".bindBtn").forEach(btn => btn.classList.remove("kbd-capturing"));
      updateBindingButtons();
    }

    function handleInputEvent(e) {
      const sig = eventToSignature(e);
      const isPrompting = !els.prompterPage.classList.contains("hidden");

      // Key capture mode.
      if (captureAction) {
        // Allow Escape to cancel if configuring something other than exit.
        if (e.type === "keydown" && (e.key === "Escape" || e.code === "Escape") && captureAction !== "exit") {
          e.preventDefault();
          e.stopPropagation();
          cancelCapture();
          return;
        }

        if (isUsableSignature(sig)) {
          e.preventDefault();
          e.stopPropagation();

          state.bindings[captureAction] = normaliseSignature(sig);
          saveState();

          const finishedAction = captureAction;
          captureAction = null;
          els.captureStatus.classList.add("hidden");
          document.querySelectorAll(".bindBtn").forEach(btn => btn.classList.remove("kbd-capturing"));
          updateBindingButtons();

          console.log("Captured binding", finishedAction, state.bindings[finishedAction]);
        }
        return;
      }

      if (!isPrompting) return;

      // In prompter mode, block navigation keys that normally jump the page.
      if (shouldBlockDefaultInPrompter(e)) {
        e.preventDefault();
        e.stopPropagation();
      }

      // Only react once per physical key press.
      if (e.type === "keyup" || e.type === "mouseup" || e.type === "click") return;

      if (matchesSignature(sig, state.bindings.exit)) {
        exitPrompter();
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
        return;
      }

      // Extra safety for common presenter remotes, even if saved bindings are odd.
      if (sig.code === "PageDown" || sig.key === "PageDown" || sig.keyCode === 34 || sig.which === 34 || sig.code === "ArrowRight") {
        changeSpeed(0.5);
        return;
      }

      if (sig.code === "PageUp" || sig.key === "PageUp" || sig.keyCode === 33 || sig.which === 33 || sig.code === "ArrowLeft") {
        changeSpeed(-0.5);
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

    function isUsableSignature(sig) {
      // Avoid capturing ordinary setup button mouse clicks as the binding.
      if ((sig.type === "click" || sig.type === "mousedown" || sig.type === "mouseup" || sig.type === "pointerdown") &&
          sig.button === 0 &&
          !sig.pointerType) {
        return false;
      }

      return !!(
        sig.code ||
        sig.key ||
        sig.keyCode ||
        sig.which ||
        sig.button !== null ||
        sig.pointerType
      );
    }

    function normaliseSignature(sig) {
      const out = {};

      if (sig.code) out.code = sig.code;
      if (sig.key) out.key = sig.key;
      if (sig.keyCode) out.keyCode = sig.keyCode;
      if (sig.which) out.which = sig.which;
      if (sig.button !== null && sig.button !== undefined) out.button = sig.button;
      if (sig.pointerType) out.pointerType = sig.pointerType;

      // Space special case.
      if (out.code === "Space" || out.key === " ") {
        out.code = "Space";
        out.key = " ";
      }

      return out;
    }

    function matchesSignature(inputSig, bindingSig) {
      if (!bindingSig) return false;

      const checks = ["code", "key", "keyCode", "which", "button", "pointerType"];
      return checks.some(k => {
        return bindingSig[k] !== undefined &&
               bindingSig[k] !== null &&
               bindingSig[k] !== "" &&
               inputSig[k] === bindingSig[k];
      });
    }

    function shouldBlockDefaultInPrompter(e) {
      const navKeys = new Set([
        "PageUp", "PageDown", "ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight",
        " ", "Spacebar", "Home", "End"
      ]);

      return (
        navKeys.has(e.key) ||
        navKeys.has(e.code) ||
        e.keyCode === 33 ||
        e.keyCode === 34 ||
        e.keyCode === 32 ||
        e.keyCode === 38 ||
        e.keyCode === 40
      );
    }

    function bindingLabel(sig) {
      if (!sig) return "Unbound";
      if (sig.code === "Space" || sig.key === " ") return "Space";
      if (sig.code) return sig.code;
      if (sig.key) return sig.key;
      if (sig.keyCode) return "KeyCode " + sig.keyCode;
      if (sig.button !== undefined && sig.button !== null) return "Button " + sig.button;
      if (sig.pointerType) return sig.pointerType;
      return "Custom";
    }

    function updateBindingButtons() {
      document.querySelectorAll(".bindBtn").forEach(btn => {
        const action = btn.dataset.bind;
        btn.textContent = bindingLabel(state.bindings[action]);
      });
    }

    function launchPrompter() {
      console.log("Launching prompter");

      currentSpeed = 0;
      isScrolling = false;
      scrollPos = 0;

      els.prompterPage.classList.remove("hidden");

      const bg = hexToRgb(state.bgColor);
      els.prompterPage.style.backgroundColor = `rgba(${bg.r}, ${bg.g}, ${bg.b}, ${state.bgOpacity / 100})`;

      els.prompterText.textContent = state.text || "";
      els.prompterText.style.fontSize = state.textSize + "px";
      els.prompterText.style.lineHeight = "1.5";
      els.prompterText.style.color = state.textColor;
      els.prompterText.style.paddingLeft = state.indent + "%";
      els.prompterText.style.paddingRight = state.indent + "%";

      const sx = state.flipHorizontal ? -1 : 1;
      const sy = state.flipVertical ? -1 : 1;
      els.prompterTransform.style.transform = `scale(${sx}, ${sy})`;
      els.prompterTransform.style.transformOrigin = "center center";

      els.prompterFrame.scrollTop = 0;

      if (state.focusEnabled) {
        const fc = hexToRgb(state.focusColor);
        els.focusBar.classList.remove("hidden");
        els.focusBar.style.top = state.focusOffset + "vh";
        els.focusBar.style.height = state.focusHeight + "px";
        els.focusBar.style.marginTop = (-state.focusHeight / 2) + "px";
        els.focusBar.style.backgroundColor = `rgba(${fc.r}, ${fc.g}, ${fc.b}, ${state.focusOpacity / 100})`;
      } else {
        els.focusBar.classList.add("hidden");
      }

      els.controlsHud.textContent =
        `${bindingLabel(state.bindings.speedUp)} speed up | ${bindingLabel(state.bindings.speedDown)} slow/reverse | ${bindingLabel(state.bindings.toggle)} pause`;

      updateHud();

      if (rafId) cancelAnimationFrame(rafId);
      lastTime = performance.now();
      rafId = requestAnimationFrame(tick);
    }

    function exitPrompter() {
      if (rafId) cancelAnimationFrame(rafId);
      rafId = null;
      isScrolling = false;
      currentSpeed = 0;
      els.prompterPage.classList.add("hidden");
    }

    function tick(now) {
      const dt = (now - lastTime) / 1000;
      lastTime = now;

      if (isScrolling && currentSpeed !== 0) {
        scrollPos += currentSpeed * 25 * dt;

        const maxScroll = Math.max(0, els.prompterFrame.scrollHeight - els.prompterFrame.clientHeight);

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

        els.prompterFrame.scrollTop = scrollPos;
        updateHud();
      }

      rafId = requestAnimationFrame(tick);
    }

    function togglePlay() {
      isScrolling = !isScrolling;
      if (isScrolling && currentSpeed === 0) {
        currentSpeed = Number(state.startSpeed) || 1;
      }
      updateHud();
    }

    function changeSpeed(delta) {
      if (!isScrolling) isScrolling = true;
      currentSpeed = Math.round((currentSpeed + delta) * 10) / 10;
      updateHud();
    }

    function resetPrompt() {
      scrollPos = 0;
      els.prompterFrame.scrollTop = 0;
      isScrolling = false;
      currentSpeed = 0;
      updateHud();
    }

    function updateHud() {
      els.speedHud.textContent = isScrolling ? currentSpeed.toFixed(1) : `PAUSED ${currentSpeed.toFixed(1)}`;
      els.speedHud.classList.toggle("text-amber-400", currentSpeed < 0);
      els.speedHud.classList.toggle("text-blue-400", currentSpeed >= 0);
    }

    function hexToRgb(hex) {
      const safe = /^#[0-9a-fA-F]{6}$/.test(hex) ? hex : "#000000";
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
