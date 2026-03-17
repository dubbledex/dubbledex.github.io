# dubbledex.github.io
Test webpage for iiyama 10" screen lighting test v2
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>LED HTTP Test</title>
</head>
<body>
  <h1>LED HTTP Test</h1>

  <button onclick="setTabletLed('on')">On</button>
  <button onclick="setTabletLed('red')">Red</button>
  <button onclick="setTabletLed('green')">Green</button>
  <button onclick="setTabletLed('yellow')">Yellow</button>
  <button onclick="setTabletLed('off')">Off</button>

  <pre id="out"></pre>

  <script>
    async function setTabletLed(color) {
      try {
        const res = await fetch("http://127.0.0.1:8765/led?color=" + encodeURIComponent(color));
        const data = await res.json();
        document.getElementById("out").textContent = JSON.stringify(data, null, 2);
      } catch (err) {
        document.getElementById("out").textContent = "ERROR: " + err;
      }
    }
  </script>
</body>
</html>
