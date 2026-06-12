# dubbledex.github.io
Test webpage for iiyama 10" screen lighting test v3 - test variables
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Android Room Bridge LED Test</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <style>
    html, body {
      background: #f5f5f5;
      color: #222222;
    }

    body {
      font-family: Arial, sans-serif;
      margin: 24px;
    }

    h1 {
      margin-top: 0;
      color: #222222;
    }

    h2 {
      color: #222222;
    }

    .card {
      background-color: #ffffff;
      color: #222222;
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
      background-color: #0066cc;
      color: #ffffff;
      cursor: pointer;
    }

    button.red {
      background-color: #cc2222;
      color: #ffffff;
    }

    button.green {
      background-color: #168a31;
      color: #ffffff;
    }

    button.yellow {
      background-color: #d19a00;
      color: #111111;
    }

    button.dark {
      background-color: #444444;
      color: #ffffff;
    }

    button.secondary {
      background-color: #666666;
      color: #ffffff;
    }

    label {
      display: block;
      margin: 10px 0;
      font-weight: bold;
      color: #222222;
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
      color: #000000;
      background-color: #ffffff;
      border: 1px solid #888888;
    }

    code {
      background-color: #eeeeee;
      color: #111111;
      padding: 2px 4px;
      border-radius: 3px;
    }

    .outputBox {
      box-sizing: border-box;
      width: 100%;
      min-height: 52px;
      padding: 14px;
      margin-top: 8px;

      background-color: #ffffff !important;
      color: #000000 !important;

      border: 4px solid #555555;
      border-radius: 6px;

      font-family: Arial, sans-serif;
      font-size: 18px;
      line-height: 1.45;
      white-space: pre-wrap;
      overflow-wrap: anywhere;
      word-break: break-word;
    }

    .outputBox.idle {
      border-color: #555555;
      background-color: #ffffff !important;
      color: #000000 !important;
    }

    .outputBox.success {
      border-color: #168a31;
      background-color: #ffffff !important;
      color: #000000 !important;
    }

    .outputBox.error {
      border-color: #cc2222;
      background-color: #ffffff !important;
      color: #000000 !important;
    }

    .small {
      font-size: 14px;
      color: #555555;
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
    <div id="lastRequest" class="outputBox idle">No request yet.</div>
  </div>

  <div class="card">
    <h2>Response</h2>
    <div id="output" class="outputBox idle">No response yet.</div>
  </div>

  <script>
    var BASE_URL = "http://127.0.0.1:8765";

    function getToken() {
      return document.getElementById("tokenInput").value.trim();
    }

    function isSecureMode() {
      return document.getElementById("secureToggle").checked;
    }

    function buildUrl(path, params) {
      var url = BASE_URL + path;
      var queryParts = [];

      if (params) {
        for (var key in params) {
          if (params.hasOwnProperty(key)) {
            if (params[key] !== null && params[key] !== undefined) {
              queryParts.push(
                encodeURIComponent(key) + "=" + encodeURIComponent(params[key])
              );
            }
          }
        }
      }

      if (isSecureMode()) {
        queryParts.push(
          "token=" + encodeURIComponent(getToken())
        );
      }

      if (queryParts.length > 0) {
        url += "?" + queryParts.join("&");
      }

      return url;
    }

    function setBoxText(id, text, state) {
      var box = document.getElementById(id);

      box.className = "outputBox " + state;
      box.style.backgroundColor = "#ffffff";
      box.style.color = "#000000";
      box.style.display = "block";
      box.innerText = text;
      box.textContent = text;
    }

    function setLastRequest(url) {
      setBoxText("lastRequest", url, "success");
    }

    function setOutput(text, isError) {
      setBoxText("output", text, isError ? "error" : "success");
    }

    function callBridge(url) {
      setLastRequest(url);
      setOutput("Waiting for response...", false);

      fetch(url, {
        method: "GET",
        cache: "no-store"
      })
      .then(function(response) {
        return response.text().then(function(text) {
          var display = "HTTP " + response.status + " " + response.statusText + "\n\n";

          try {
            var json = JSON.parse(text);
            display += JSON.stringify(json, null, 2);
          } catch (e) {
            display += text;
          }

          setOutput(display, !response.ok);
        });
      })
      .catch(function(err) {
        setOutput("Request failed:\n\n" + err.message, true);
      });
    }

    function ping() {
      callBridge(BASE_URL + "/ping");
    }

    function authPing() {
      callBridge(buildUrl("/auth/ping", {}));
    }

    function showModel() {
      callBridge(buildUrl("/model", {}));
    }

    function setLed(color) {
      callBridge(buildUrl("/led", {
        color: color
      }));
    }

    document.getElementById("baseUrlText").innerText = BASE_URL;
    document.getElementById("baseUrlText").textContent = BASE_URL;
  </script>
</body>
</html>


