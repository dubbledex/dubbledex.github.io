# dubbledex.github.io
Test webpage for iiyama 10" screen lighting test v3 - test variables
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Android Room Bridge LED Test</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 24px;
      background: #f5f5f5;
      color: #222;
    }

    h1 {
      margin-top: 0;
    }

    .card {
      background: #fff;
      border-radius: 8px;
      padding: 18px;
      margin-bottom: 18px;
      box-shadow: 0 1px 4px rgba(0,0,0,0.15);
    }

    button {
      display: inline-block;
      margin: 6px 6px 6px 0;
      padding: 12px 16px;
      font-size: 16px;
      border: 0;
      border-radius: 5px;
      background: #0066cc;
      color: white;
      cursor: pointer;
    }

    button.red {
      background: #cc2222;
    }

    button.green {
      background: #168a31;
    }

    button.yellow {
      background: #d19a00;
      color: #111;
    }

    button.dark {
      background: #444;
    }

    button.secondary {
      background: #666;
    }

    button:active {
      transform: scale(0.98);
    }

    label {
      display: block;
      margin: 10px 0;
      font-weight: bold;
    }

    input[type="checkbox"] {
      transform: scale(1.25);
      margin-right: 8px;
    }

    input[type="text"] {
      width: 100%;
      max-width: 760px;
      padding: 8px;
      font-size: 14px;
      box-sizing: border-box;
      font-family: monospace;
    }

    code {
      background: #eee;
      color: #111;
      padding: 2px 4px;
      border-radius: 3px;
    }

    pre {
      background: #050505;
      color: #ffffff;
      padding: 14px;
      overflow-x: auto;
      border-radius: 6px;
      min-height: 44px;
      font-size: 15px;
      line-height: 1.45;
      font-family: Consolas, Monaco, "Courier New", monospace;
      white-space: pre-wrap;
      word-break: break-word;
      border: 3px solid #333;
    }

    pre.idle {
      border-color: #555;
    }

    pre.success {
      border-color: #168a31;
    }

    pre.error {
      border-color: #cc2222;
    }

    .ok {
      color: green;
      font-weight: bold;
    }

    .bad {
      color: #bb2222;
      font-weight: bold;
    }

    .small {
      font-size: 14px;
      color: #555;
    }
  </style>
</head>

<body>
  <h1>Android Room Bridge LED Test</h1>

  <div class="card">
    <h2>Connection</h2>

    <p>Bridge base URL:</p>
    <p><code id="baseUrlText">http://127.0.0.1:8765</code></p>

    <label>
      <input id="secureToggle" type="checkbox" checked>
      Use secured/tokenised mode
    </label>

    <p class="small">
      When enabled, requests include <code>token=...</code>.
      When disabled, requests are sent without a token.
    </p>

    <label for="tokenInput">Test token</label>
    <input
      id="tokenInput"
      type="text"
      value="5ed2dd01df168bad32c3ba2c20614e3be377abaee72ddeee9801a5403e88f014"
    >
  </div>

  <div class="card">
    <h2>Server Tests</h2>

    <button class="secondary" onclick="ping()">Ping</button>
    <button class="secondary" onclick="authPing()">Authenticated Ping</button>
    <button class="secondary" onclick="showModel()">Show Model</button>
  </div>

  <div class="card">
    <h2>LED Controls</h2>

    <button onclick="setLed('on')">On</button>
    <button class="green" onclick="setLed('green')">Green</button>
    <button class="red" onclick="setLed('red')">Red</button>
    <button class="yellow" onclick="setLed('yellow')">Yellow</button>
    <button class="dark" onclick="setLed('off')">Off</button>
  </div>

  <div class="card">
    <h2>Last Request</h2>
    <pre id="lastRequest" class="idle">No request yet.</pre>
  </div>

  <div class="card">
    <h2>Response</h2>
    <pre id="output" class="idle">No response yet.</pre>
  </div>

  <script>
    const BASE_URL = "http://127.0.0.1:8765";

    function getToken() {
      return document.getElementById("tokenInput").value.trim();
    }

    function isSecureMode() {
      return document.getElementById("secureToggle").checked;
    }

    function buildUrl(path, params) {
      const url = new URL(BASE_URL + path);

      if (params) {
        Object.keys(params).forEach(function (key) {
          if (params[key] !== null && params[key] !== undefined) {
            url.searchParams.set(key, params[key]);
          }
        });
      }

      if (isSecureMode()) {
        url.searchParams.set("token", getToken());
      }

      return url.toString();
    }

    function setLastRequest(url) {
      const box = document.getElementById("lastRequest");
      box.textContent = url;
      box.className = "success";
    }

    function setOutput(text, isError) {
      const output = document.getElementById("output");
      output.textContent = text;
      output.className = isError ? "error" : "success";
    }

    async function callBridge(url) {
      setLastRequest(url);
      setOutput("Waiting for response...", false);

      try {
        const response = await fetch(url, {
          method: "GET",
          cache: "no-store"
        });

        const text = await response.text();

        let display = "HTTP " + response.status + " " + response.statusText + "\n\n";

        try {
          const json = JSON.parse(text);
          display += JSON.stringify(json, null, 2);
        } catch (e) {
          display += text;
        }

        setOutput(display, !response.ok);
      } catch (err) {
        setOutput("Request failed:\n\n" + err.message, true);
      }
    }

    function ping() {
      const url = BASE_URL + "/ping";
      callBridge(url);
    }

    function authPing() {
      const url = buildUrl("/auth/ping", {});
      callBridge(url);
    }

    function showModel() {
      const url = buildUrl("/model", {});
      callBridge(url);
    }

    function setLed(color) {
      const url = buildUrl("/led", {
        color: color
      });

      callBridge(url);
    }

    document.getElementById("baseUrlText").textContent = BASE_URL;
  </script>
</body>
</html>

