# AVIF to JPG/PNG/WEBP Converter 
Here's a detailed AVIF to JPG/PNG/WEBP converter using HTML and JavaScript. This solution uses the browser's built-in image decoding capabilities and canvas for conversion:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AVIF Converter</title>
    <style>
        .container {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        .converter-box {
            border: 2px dashed #ccc;
            padding: 20px;
            text-align: center;
            margin-bottom: 20px;
        }
        #preview {
            max-width: 100%;
            margin-top: 20px;
        }
        #qualityContainer {
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>AVIF to JPG/PNG/WEBP Converter</h1>
        
        <div class="converter-box">
            <input type="file" id="avifInput" accept=".avif" />
            <select id="formatSelect">
                <option value="jpg">JPG</option>
                <option value="png">PNG</option>
                <option value="webp">WEBP</option>
            </select>
            <div id="qualityContainer">
                <label for="quality">Quality (0-1):</label>
                <input type="number" id="quality" min="0" max="1" step="0.1" value="0.92">
            </div>
            <button onclick="convertAvif()">Convert</button>
            <canvas id="previewCanvas" style="display: none;"></canvas>
        </div>

        <div id="result" style="display: none;">
            <h3>Converted Image:</h3>
            <img id="preview" />
            <a id="downloadLink" download>Download</a>
        </div>
    </div>

    <script>
        async function convertAvif() {
            const fileInput = document.getElementById('avifInput');
            const formatSelect = document.getElementById('formatSelect');
            const qualityInput = document.getElementById('quality');
            const preview = document.getElementById('preview');
            const downloadLink = document.getElementById('downloadLink');
            const canvas = document.getElementById('previewCanvas');
            const resultDiv = document.getElementById('result');

            if (!fileInput.files[0]) {
                alert('Please select an AVIF file first!');
                return;
            }

            try {
                // Check for AVIF support
                if (!(await ImageDecoder.isTypeSupported('image/avif'))) {
                    alert('AVIF format is not supported in this browser');
                    return;
                }

                const file = fileInput.files[0];
                const format = formatSelect.value;
                const quality = parseFloat(qualityInput.value);

                // Read AVIF file
                const buffer = await file.arrayBuffer();
                const blob = new Blob([buffer], { type: 'image/avif' });

                // Decode AVIF image
                const imageDecoder = new ImageDecoder({ data: blob, type: 'image/avif' });
                const { image } = await imageDecoder.decode();

                // Draw image to canvas
                canvas.width = image.displayWidth;
                canvas.height = image.displayHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(image, 0, 0);

                // Convert to selected format
                const mimeType = `image/${format === 'jpg' ? 'jpeg' : format}`;
                canvas.toBlob((blob) => {
                    const url = URL.createObjectURL(blob);
                    preview.src = url;
                    downloadLink.href = url;
                    downloadLink.download = `converted.${format}`;
                    downloadLink.textContent = `Download ${format.toUpperCase()}`;
                    resultDiv.style.display = 'block';
                }, mimeType, quality);

                image.close();
            } catch (error) {
                alert(`Error converting image: ${error.message}`);
            }
        }

        // Initial setup
        document.getElementById('formatSelect').addEventListener('change', (e) => {
            const qualityContainer = document.getElementById('qualityContainer');
            qualityContainer.style.display = e.target.value === 'png' ? 'none' : 'block';
        });
    </script>
</body>
</html>
```

Key features of this converter:

1. **File Input**: Accepts AVIF files through file selection
2. **Format Selection**: Dropdown to choose output format (JPG, PNG, WEBP)
3. **Quality Control**: Adjustable quality setting (0-1) for lossy formats
4. **Preview**: Displays the converted image
5. **Download**: Provides a download link for the converted image

How it works:

1. **File Reading**: Reads the AVIF file using FileReader
2. **AVIF Decoding**: Uses the browser's ImageDecoder API to decode AVIF
3. **Canvas Rendering**: Draws the decoded image onto a canvas element
4. **Format Conversion**: Converts the canvas content to the selected format
5. **Quality Adjustment**: Applies quality settings for lossy formats (JPG/WEBP)

Important considerations:

1. **Browser Support**: Requires a modern browser that supports:
   - AVIF decoding (Chrome 85+, Firefox 93+, Edge 93+)
   - ImageDecoder API (Chrome 94+, Edge 94+)

2. **Quality Settings**:
   - PNG uses lossless compression (quality setting ignored)
   - JPG/WEBP use quality parameter (0.0-1.0)

3. **Limitations**:
   - Large files may cause performance issues
   - Some browsers might have AVIF decoding limitations
   - EXIF data is not preserved in current implementation

To use this converter:

1. Select an AVIF file
2. Choose desired output format
3. Adjust quality (if applicable)
4. Click "Convert"
5. Preview and download the converted image

For broader browser support, you could add a third-party AVIF decoder library, but that would significantly increase the code complexity and size.

Note: This code should be hosted on a secure (HTTPS) connection for proper functionality in modern browsers, and some browsers might require enabling experimental features for full AVIF support.
