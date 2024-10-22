# Building-color
Building color recommendation 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Building Color Suggestion</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
        }
        canvas {
            display: none;
        }
        .color-box {
            width: 100px;
            height: 100px;
            display: inline-block;
            margin: 10px;
            border: 2px solid #000;
        }
        .color-text {
            margin-top: 5px;
            font-weight: bold;
        }
    </style>
</head>
<body>

<h1>Upload Building Photo to Suggest Complementary Colors</h1>

<input type="file" id="imageInput" accept="image/*">
<br><br>
<img id="uploadedImage" width="400" alt="Building Image">
<br><br>
<h2>Suggested Complementary Colors:</h2>
<div id="colorSuggestions"></div>

<canvas id="imageCanvas"></canvas>

<script>
    const imageInput = document.getElementById('imageInput');
    const uploadedImage = document.getElementById('uploadedImage');
    const colorSuggestions = document.getElementById('colorSuggestions');
    const canvas = document.getElementById('imageCanvas');
    const ctx = canvas.getContext('2d');

    imageInput.addEventListener('change', handleImageUpload);

    function handleImageUpload(event) {
        const file = event.target.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = function(e) {
                uploadedImage.src = e.target.result;
                uploadedImage.onload = function() {
                    extractColors(uploadedImage);
                }
            }
            reader.readAsDataURL(file);
        }
    }

    function extractColors(image) {
        // Draw the uploaded image on the canvas
        canvas.width = image.width;
        canvas.height = image.height;
        ctx.drawImage(image, 0, 0, canvas.width, canvas.height);

        // Get the image data
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;

        // Object to store color counts
        const colorCount = {};

        // Loop through pixels and count the occurrences of each color
        for (let i = 0; i < data.length; i += 4) {
            const r = data[i];
            const g = data[i + 1];
            const b = data[i + 2];
            const colorKey = `${r},${g},${b}`;

            if (colorCount[colorKey]) {
                colorCount[colorKey]++;
            } else {
                colorCount[colorKey] = 1;
            }
        }

        // Sort colors by occurrences
        const sortedColors = Object.keys(colorCount).sort((a, b) => colorCount[b] - colorCount[a]);

        // Take top 5 dominant colors
        const dominantColors = sortedColors.slice(0, 5);

        // Display suggested complementary colors
        displaySuggestedColors(dominantColors);
    }

    function displaySuggestedColors(dominantColors) {
        colorSuggestions.innerHTML = ''; // Clear previous suggestions

        dominantColors.forEach(color => {
            const [r, g, b] = color.split(',').map(Number);
            const complementaryColor = `rgb(${255 - r}, ${255 - g}, ${255 - b})`;
            
            const colorBox = document.createElement('div');
            colorBox.classList.add('color-box');
            colorBox.style.backgroundColor = complementaryColor;

            const colorText = document.createElement('div');
            colorText.classList.add('color-text');
            colorText.innerText = complementaryColor;

            colorBox.appendChild(colorText);
            colorSuggestions.appendChild(colorBox);
        });
    }
</script>

</body>
</html>
