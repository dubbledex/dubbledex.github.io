# dubbledex.github.io
Test webpage for iiyama 10" screen lighting test v3 - test variables
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Room LED State Test</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 24px;
    }

    button {
      display: block;
      width: 260px;
      margin: 12px 0;
      padding: 14px;
      font-size: 18px;
    }

    pre {
      background: #f3f3f3;
      padding: 12px;
      white-space: pre-wrap;
    }
  </style>
</head>
<body>
  <h1>Room LED State Test</h1>

  <button onclick="pingServer()">Ping Server</button>
  <button onclick="updateRoomState('available')">Available</button>
  <button onclick="updateRoomState('occupied')">Occupied</button>
  <button onclick="updateRoomState('warning')">Warning</button>
  <button onclick="updateRoomState('off')">Off / Screen Reset</button>

  <pre id="out">Waiting...</pre>

  <script>
    let ledServerReady = false;
    let pendingColor = null;

    function log(msg) {
      document.getElementById("out").textContent = msg;
      console.log(msg);
    }

    async function pingLedServer() {
      try {
        const res = await fetch("http://127.0.0.1:8765/ping", {
          cache: "no-store"
        });
        const data = await res.json();
        return data && data.ok === true;
      } catch (err) {
        return false;
      }
    }

    async function waitForLedServer(maxAttempts = 20, delayMs = 2000) {
      for (let i = 0; i < maxAttempts; i++) {
        log("Waiting for LED server... attempt " + (i + 1));

        const ok = await pingLedServer();
        if (ok) {
          ledServerReady = true;
          log("LED server is ready");
          return true;
        }

        await new Promise(resolve => setTimeout(resolve, delayMs));
      }

      log("LED server did not become ready");
      return false;
    }

    async function sendLedColor(color) {
      const res = await fetch(
        "http://127.0.0.1:8765/led?color=" + encodeURIComponent(color),
        { cache: "no-store" }
      );
      return await res.json();
    }

    async function setTabletLed(color) {
      pendingColor = color;

      if (!ledServerReady) {
        const ok = await waitForLedServer();
        if (!ok) {
          return;
        }
      }

      try {
        const result = await sendLedColor(pendingColor);
        log("LED result:\n" + JSON.stringify(result, null, 2));
      } catch (err) {
        ledServerReady = false;
        log("LED request failed: " + err);
      }
    }

    async function updateRoomState(state) {
      log("Requested room state: " + state);

      switch (state) {
        case "available":
          await setTabletLed("green");
          break;

        case "occupied":
          await setTabletLed("red");
          break;

        case "warning":
          await setTabletLed("yellow");
          break;

        case "off":
        case "screen-reset":
          await setTabletLed("off");
          break;

        default:
          log("Unknown room state: " + state);
      }
    }

    async function pingServer() {
      const ok = await pingLedServer();
      ledServerReady = ok;
      log(ok ? "Ping OK" : "Ping FAILED");
    }
  </script>
</body>
</html>

