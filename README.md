# dubbledex.github.io
Test webpage for iiyama 10" screen lighting test
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>LED Test</title>
</head>
<body>
  <button onclick="setTabletLed('on')">On</button>
  <button onclick="setTabletLed('red')">Red</button>
  <button onclick="setTabletLed('green')">Green</button>
  <button onclick="setTabletLed('yellow')">Yellow</button>
  <button onclick="setTabletLed('off')">Off</button>

  <script>
    function setTabletLed(color) {
      const iframe = document.createElement("iframe");
      iframe.style.display = "none";
      iframe.src = "roomled://set?color=" + encodeURIComponent(color);
      document.body.appendChild(iframe);

      setTimeout(() => iframe.remove(), 1000);
    }
  </script>
</body>
</html>

