<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>SK Tool - PDF to JPG Converter</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: #f4f4f9;
      text-align: center;
      padding: 50px;
    }
    h1 {
      font-size: 2.5em;
      background: linear-gradient(to right, #ff416c, #ff4b2b);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 20px;
    }
    input[type="file"] {
      padding: 10px;
      margin: 20px 0;
      border-radius: 8px;
      border: 2px solid #ff4b2b;
      cursor: pointer;
    }
    button {
      background: #ff416c;
      color: white;
      padding: 10px 25px;
      border: none;
      border-radius: 8px;
      font-size: 1em;
      cursor: pointer;
      margin-top: 15px;
    }
    button:hover {
      background: #ff4b2b;
    }
    canvas {
      margin-top: 20px;
      border: 2px solid #ccc;
      border-radius: 10px;
      max-width: 90%;
    }
  </style>
</head>
<body>
  <h1>SK Tool</h1>
  <p>Upload PDF to convert first page to JPG</p>
  <input type="file" id="upload" accept="application/pdf">
  <br>
  <button id="downloadBtn" style="display:none;">Download JPG</button>
  <br>
  <canvas id="canvas"></canvas>

  <script>
    const input = document.getElementById('upload');
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('downloadBtn');

    input.addEventListener('change', function(e) {
      const file = e.target.files[0];
      const reader = new FileReader();

      reader.onload = function() {
        const typedarray = new Uint8Array(this.result);
        pdfjsLib.getDocument(typedarray).promise.then(function(pdf) {
          pdf.getPage(1).then(function(page) {
            const viewport = page.getViewport({ scale: 2 });
            canvas.height = viewport.height;
            canvas.width = viewport.width;

            page.render({
              canvasContext: ctx,
              viewport: viewport
            }).promise.then(() => {
              downloadBtn.style.display = 'inline-block';
            });
          });
        });
      };
      reader.readAsArrayBuffer(file);
    });

    downloadBtn.addEventListener('click', function() {
      const link = document.createElement('a');
      link.download = 'page1.jpg';
      link.href = canvas.toDataURL('image/jpeg');
      link.click();
    });
  </script>
</body>
</html>
