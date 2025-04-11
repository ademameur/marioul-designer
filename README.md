<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Marioul Designer</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to right, #ece9e6, #ffffff);
      text-align: center;
      padding: 20px;
    }

    h1 {
      margin-bottom: 10px;
    }

    header img {
      max-width: 150px;
      height: auto;
      transition: transform 0.3s ease-in-out;
    }

    header img:hover {
      transform: scale(1.05);
    }

    .controls {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
      margin-bottom: 20px;
    }

    .controls input, .controls label, .controls button {
      font-size: 16px;
      padding: 8px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }

    .container {
      position: relative;
      display: inline-block;
      margin-top: 20px;
      max-width: 90vw;
    }

    .marioul-text {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%) rotate(0deg);
      font-weight: bold;
      color: white;
      font-size: 24px;
      white-space: nowrap;
      pointer-events: none;
      transition: all 0.3s ease-in-out;
    }

    img {
      max-width: 100%;
      height: auto;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }

    canvas {
      display: none;
    }

    button {
      background-color: #4CAF50;
      color: white;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #45a049;
    }

    @media (max-width: 600px) {
      .marioul-text {
        font-size: 18px !important;
      }
    }
  </style>
</head>
<body>

  <header>
    <img src="logo.png" alt="Marioul Designer Logo">
  </header>

  <h1>🎨 Marioul Designer</h1>
  <p>Crée ton propre t-shirt stylé en quelques clics !</p>

  <div class="controls">
    <input type="file" id="imageUploader" accept="image/*">
    <input type="text" id="textInput" placeholder="اكتب على المريول" oninput="updateText()">
    <label>🎨 Couleur: <input type="color" id="colorPicker" value="#ffffff" onchange="updateText()"></label>
    <label>🔠 Taille: <input type="range" id="sizeSlider" min="10" max="72" value="24" oninput="updateText()"></label>
    <label>🔄 Rotation: <input type="range" id="rotateSlider" min="-90" max="90" value="0" oninput="updateText()"></label>
    <button onclick="downloadImage()">📥 Télécharger</button>
  </div>

  <div class="container">
    <img id="marioulImage" src="https://i.imgur.com/L5f6gAq.png" alt="Marioul">
    <div class="marioul-text" id="textOverlay">Marioul</div>
  </div>

  <canvas id="canvas"></canvas>

  <script>
    const textOverlay = document.getElementById('textOverlay');
    const textInput = document.getElementById('textInput');
    const colorPicker = document.getElementById('colorPicker');
    const sizeSlider = document.getElementById('sizeSlider');
    const rotateSlider = document.getElementById('rotateSlider');
    const imageUploader = document.getElementById('imageUploader');
    const marioulImage = document.getElementById('marioulImage');

    function updateText() {
      textOverlay.textContent = textInput.value;
      textOverlay.style.color = colorPicker.value;
      textOverlay.style.fontSize = sizeSlider.value + 'px';
      textOverlay.style.transform = `translate(-50%, -50%) rotate(${rotateSlider.value}deg)`;
    }

    imageUploader.addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(event) {
          marioulImage.onload = function() {
            // Redimensionner le canvas pour s'adapter à l'image
            canvas.width = marioulImage.width;
            canvas.height = marioulImage.height;
          };
          marioulImage.src = event.target.result;
        };
        reader.readAsDataURL(file);
      }
    });

    function downloadImage() {
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');

      // Vérifier si l'image est chargée
      if (marioulImage.complete) {
        // Ajuster la taille du canvas à l'image
        canvas.width = marioulImage.width;
        canvas.height = marioulImage.height;

        // Dessiner l'image sur le canvas
        ctx.drawImage(marioulImage, 0, 0);

        // Dessiner le texte sur l'image
        ctx.save();
        ctx.translate(canvas.width / 2, canvas.height / 2);
        ctx.rotate((rotateSlider.value * Math.PI) / 180);
        ctx.font = `${sizeSlider.value}px Arial`;
        ctx.fillStyle = colorPicker.value;
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText(textInput.value, 0, 0);
        ctx.restore();

        // Télécharger l'image personnalisée
        const link = document.createElement('a');
        link.download = 'marioul_custom.png';
        link.href = canvas.toDataURL();
        link.click();
      } else {
        alert("L'image n'est pas encore chargée.");
      }
    }
  </script>

</body>
</html>

