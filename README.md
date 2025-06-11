<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ShinyFX - Animated Shine Effects</title>
<!-- Import Poppins Font (ExtraBold 800) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@800&display=swap" rel="stylesheet">

<style>
body {
margin: 0;
padding: 20px;
font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
background: #1a1a1a;
color: white;
min-height: 100vh;
display: flex;
flex-direction: column;
align-items: center;
justify-content: center;
}

/* --- ANIMATED TEXT STYLES --- */

@keyframes gradient-pan {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
}

.animated-gradient-text {
    font-family: 'Poppins', sans-serif;
    font-weight: 800;
    
    background: linear-gradient(105deg, #FDEECA, #FFC7C7, #C9D8FF, #E3C9FF, #FDEECA);
    background-size: 300% 300%;
    animation: gradient-pan 4s linear infinite;

    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    -webkit-text-stroke-color: rgb(76, 76, 76);
}

h1.animated-gradient-text {
    text-align: center;
    margin-bottom: 15px;
    font-size: 2.8em;
    -webkit-text-stroke-width: 1.849px;
}

.header-comment {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 0.25em;
    text-align: center;
    margin-top: 15px;
    margin-bottom: 30px;
}
.header-comment .animated-gradient-text {
    font-size: 1.2em;
    -webkit-text-stroke-width: 1px;
}

/* --- MAIN CONTAINER STYLES --- */

.gradient-container {
    width: 420px;
    height: 420px;
    border-radius: 15px;
    position: relative;
    overflow: hidden;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
    margin-bottom: 30px;
    transition: all 0.3s ease;
    background-color: #111;
}

/* MODIFIED: Add this new rule to make the container have sharp corners when the border is active */
.gradient-container.with-border {
    border-radius: 0;
}


#imageCanvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 2;
    cursor: grab;
}

#imageCanvas:active {
    cursor: grabbing;
}

/* --- BORDER OVERLAY & SHINY FX LAYERS --- */

.border-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: 10px solid black;
    box-sizing: border-box; 
    z-index: 3; 
    pointer-events: none;
    opacity: 0; 
    transition: opacity 0.3s ease;
}

.gradient-container.with-border .border-overlay {
    opacity: 1;
}


.shine-pulse-layer {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: url('https://i.imgur.com/37pBWms_d.webp?maxwidth=760&fidelity=grand');
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
    opacity: 0.4;
    z-index: 4; /* Above the border */
    pointer-events: none;
}

.shine-streak {
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background-image: url('https://i.imgur.com/3jQMyVI_d.webp?maxwidth=760&fidelity=grand');
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
    opacity: 0;
    z-index: 5; /* The very top layer */
    pointer-events: none;
    transform-origin: center;
}

/* --- UI ELEMENTS --- */

.upload-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(0, 0, 0, 0.8);
    border: 2px dashed #ffd700;
    border-radius: 15px; /* Keeps the overlay itself rounded */
    z-index: 10;
    transition: all 0.3s ease;
    cursor: pointer;
}

/* NEW: Also make upload overlay sharp when border is on for consistency */
.gradient-container.with-border .upload-overlay {
    border-radius: 0;
}


.upload-overlay.dragover {
    background: rgba(255, 215, 0, 0.2);
    border-color: #ffffff;
}

.upload-overlay.hidden {
    opacity: 0;
    pointer-events: none;
}

.upload-text {
    text-align: center;
    color: white;
    font-size: 18px;
    font-weight: 600;
}

.upload-subtext {
    color: #888;
    font-size: 14px;
    margin-top: 10px;
}

.controls {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 15px;
    margin-bottom: 30px;
}

.shiny-btn {
    background: linear-gradient(45deg, #ffd700, #ffed4e);
    border: none;
    color: #333;
    padding: 12px 25px;
    border-radius: 8px;
    cursor: pointer;
    font-weight: 600;
    transition: all 0.3s ease;
    font-size: 16px;
}

.shiny-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 20px rgba(255, 215, 0, 0.3);
}

.shiny-btn.active {
    background: linear-gradient(45deg, #27ae60, #2ecc71);
    color: white;
}

.shiny-btn.disabled, .shiny-btn:disabled {
    background: #555;
    color: #888;
    cursor: not-allowed;
    transform: none;
    box-shadow: none;
    opacity: 0.7;
}

.info-panel {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border-radius: 12px;
    padding: 25px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    max-width: 600px;
    width: 100%;
}

.title-line {
    border: 0;
    height: 1px;
    background-color: #fff;
    opacity: 0.7;
    width: 90%;
    max-width: 600px;
    margin: 0 auto;
}

.property { 
    display: flex; 
    justify-content: space-between; 
    margin: 10px 0; 
    padding: 8px 12px; 
    background: rgba(255, 255, 255, 0.05); 
    border-radius: 6px; 
}
.property-label { 
    font-weight: 600; 
    color: #ffd700; 
}

/* --- ANIMATIONS --- */

@keyframes streak-move {
    0% {
        transform: rotate(0deg) translate(-75%, -75%);
        opacity: 0.8;
    }
    100% {
        transform: rotate(0deg) translate(75%, 75%);
        opacity: 0;
    }
}

</style>
</head>
<body>

<h1 class="animated-gradient-text">ShinyFX</h1>
<hr class="title-line">
<div class="header-comment">
  <span class="animated-gradient-text">Made with love by: @AngelGamingBTW</span>
  <span>✨💎</span>
</div>

<div class="gradient-container" id="gradientContainer">
    <canvas id="imageCanvas" width="420" height="420"></canvas>
    
    <div class="border-overlay" id="borderOverlay"></div>
    <div class="shine-pulse-layer" id="shinePulseLayer"></div>

    <div class="upload-overlay" id="uploadOverlay">
        <div>
            <div class="upload-text">✨ Upload Image</div>
            <div class="upload-subtext">Drag & drop or click to select<br>Scroll to zoom, drag to pan</div>
        </div>
    </div>
    <input type="file" id="fileInput" accept="image/*" style="display: none;">
</div>

<div class="controls">
    <button class="shiny-btn" id="toggleBtn" onclick="toggleShinyFX()">Start ShinyFX</button>
    <button class="shiny-btn" id="saveBtn" onclick="saveAsPNG()">Save Photo</button>
    <button class="shiny-btn" onclick="clearImage()">Clear Image</button>
    <button class="shiny-btn" id="toggleBorderBtn" onclick="toggleBorder()">Add Border</button>
</div>

<div class="info-panel">
    <h3>ShinyFX Properties</h3>
    <div class="property">
        <span class="property-label">Size:</span>
        <span>420x420px</span>
    </div>
    <div class="property">
        <span class="property-label">Animation:</span>
        <span id="animationStatus">Disabled</span>
    </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<script>
    let shinyActive = false;
    let streakTimeoutId;

    let originalImage = null;
    let zoom = 1.0;
    let initialZoom = 1.0;
    let offsetX = 0;
    let offsetY = 0;
    let isDragging = false;
    let lastX, lastY;

    const gradientContainer = document.getElementById('gradientContainer');
    const shinePulseLayer = document.getElementById('shinePulseLayer');
    const uploadOverlay = document.getElementById('uploadOverlay');
    const fileInput = document.getElementById('fileInput');
    const toggleBtn = document.getElementById('toggleBtn');
    const saveBtn = document.getElementById('saveBtn'); 
    const animationStatus = document.getElementById('animationStatus');
    const toggleBorderBtn = document.getElementById('toggleBorderBtn');

    const imageCanvas = document.getElementById('imageCanvas');
    const ctx = imageCanvas.getContext('2d');

    const delay = ms => new Promise(res => setTimeout(res, ms));

    async function startPulseAnimation() {
        while (shinyActive) {
            const thisSpeed = Math.random() * 0.5 + 0.5;
            const nextSpeed = Math.random() * 0.5 + 0.5;

            shinePulseLayer.style.transition = `all ${thisSpeed}s linear`;
            shinePulseLayer.style.width = '200%';
            shinePulseLayer.style.height = '200%';
            shinePulseLayer.style.top = '-50%';
            shinePulseLayer.style.left = '-50%';

            await delay(thisSpeed * 1000);
            if (!shinyActive) break;

            shinePulseLayer.style.transition = `all ${nextSpeed}s linear`;
            shinePulseLayer.style.width = '100%';
            shinePulseLayer.style.height = '100%';
            shinePulseLayer.style.top = '0%';
            shinePulseLayer.style.left = '0%';
            
            await delay(nextSpeed * 1000);
            if (!shinyActive) break;

            await delay(500);
        }
    }

    function createShineStreak() {
        if (!shinyActive) return;
        
        const shine = document.createElement('div');
        shine.className = 'shine-streak';
        
        shine.style.animation = 'streak-move 1.5s linear forwards';
        
        gradientContainer.appendChild(shine);
        
        setTimeout(() => {
            shine.remove();
        }, 1500);
    }
    
    function startStreakAnimation() {
        if (!shinyActive) return;
        const randomDelay = (Math.random() * 2.2 + 0.8) * 1000;
        streakTimeoutId = setTimeout(() => {
            createShineStreak();
            startStreakAnimation();
        }, randomDelay);
    }

    uploadOverlay.addEventListener('click', () => fileInput.click());
    uploadOverlay.addEventListener('dragover', (e) => { 
        e.preventDefault(); 
        uploadOverlay.classList.add('dragover'); 
    });
    uploadOverlay.addEventListener('dragleave', () => uploadOverlay.classList.remove('dragover'));
    uploadOverlay.addEventListener('drop', (e) => {
        e.preventDefault();
        uploadOverlay.classList.remove('dragover');
        if (e.dataTransfer.files.length > 0) handleImageUpload(e.dataTransfer.files[0]);
    });
    fileInput.addEventListener('change', (e) => {
        if (e.target.files.length > 0) handleImageUpload(e.target.files[0]);
    });

    function handleImageUpload(file) {
        if (!file.type.startsWith('image/')) {
            alert('Please upload an image file');
            return;
        }
        const reader = new FileReader();
        reader.onload = (e) => {
            const img = new Image();
            img.crossOrigin = 'anonymous'; 
            img.onload = () => {
                originalImage = img;
                const canvasAspect = imageCanvas.width / imageCanvas.height;
                const imageAspect = img.width / img.height;
                
                if (imageAspect > canvasAspect) {
                    initialZoom = imageCanvas.width / img.width;
                } else {
                    initialZoom = imageCanvas.height / img.height;
                }
                zoom = initialZoom;
                
                offsetX = (imageCanvas.width - img.width * zoom) / 2;
                offsetY = (imageCanvas.height - img.height * zoom) / 2;

                drawImageWithTransform();
                uploadOverlay.classList.add('hidden');
            };
            img.src = e.target.result;
        };
        reader.readAsDataURL(file);
    }

    function drawImageWithTransform() {
        if (!originalImage) return;
        ctx.clearRect(0, 0, imageCanvas.width, imageCanvas.height);
        ctx.save();
        ctx.drawImage(
            originalImage, 
            offsetX, 
            offsetY, 
            originalImage.width * zoom, 
            originalImage.height * zoom
        );
        ctx.restore();
    }

    function clearImage() {
        originalImage = null;
        ctx.clearRect(0, 0, imageCanvas.width, imageCanvas.height);
        uploadOverlay.classList.remove('hidden');
        fileInput.value = '';
    }

    imageCanvas.addEventListener('wheel', (e) => {
        e.preventDefault();
        if (!originalImage) return;

        const rect = imageCanvas.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        const zoomFactor = 1.1;
        let newZoom;

        if (e.deltaY < 0) {
            newZoom = zoom * zoomFactor;
        } else {
            newZoom = zoom / zoomFactor;
        }
        
        if (newZoom < initialZoom) {
            newZoom = initialZoom;
        }

        offsetX = x - (x - offsetX) * (newZoom / zoom);
        offsetY = y - (y - offsetY) * (newZoom / zoom);

        zoom = newZoom;
        drawImageWithTransform();
    });

    imageCanvas.addEventListener('pointerdown', (e) => {
        if (!originalImage) return;
        isDragging = true;
        lastX = e.clientX;
        lastY = e.clientY;
        imageCanvas.setPointerCapture(e.pointerId);
    });
    
    imageCanvas.addEventListener('pointermove', (e) => {
        if (!isDragging || !originalImage) return;
        const dx = e.clientX - lastX;
        const dy = e.clientY - lastY;
        offsetX += dx;
        offsetY += dy;
        lastX = e.clientX;
        lastY = e.clientY;
        drawImageWithTransform();
    });

    const stopDragging = (e) => {
        isDragging = false;
        try { imageCanvas.releasePointerCapture(e.pointerId); } catch(err) {};
    }
    imageCanvas.addEventListener('pointerup', stopDragging);
    imageCanvas.addEventListener('pointerleave', stopDragging);

    function toggleBorder() {
        gradientContainer.classList.toggle('with-border');
        const isBorderActive = gradientContainer.classList.contains('with-border');
        toggleBorderBtn.textContent = isBorderActive ? 'Remove Border' : 'Add Border';
    }

    function toggleShinyFX() {
        shinyActive = !shinyActive;
        if (shinyActive) {
            toggleBtn.textContent = 'Stop ShinyFX';
            toggleBtn.classList.add('active');
            animationStatus.textContent = 'Active';
            startPulseAnimation();
            startStreakAnimation();
        } else {
            toggleBtn.textContent = 'Start ShinyFX';
            toggleBtn.classList.remove('active');
            animationStatus.textContent = 'Disabled';
            
            clearTimeout(streakTimeoutId);

            shinePulseLayer.style.transition = 'none';
            shinePulseLayer.style.width = '100%';
            shinePulseLayer.style.height = '100%';
            shinePulseLayer.style.top = '0%';
            shinePulseLayer.style.left = '0%';
            
            const existingShines = gradientContainer.querySelectorAll('.shine-streak');
            existingShines.forEach(shine => shine.remove());
        }
    }

    async function saveAsPNG() {
        if (!originalImage) {
            alert("Please upload an image first!");
            return;
        }
        
        const wasActive = shinyActive;
        if (wasActive) toggleShinyFX();
        
        saveBtn.textContent = 'Saving...';
        saveBtn.disabled = true;

        await new Promise(resolve => setTimeout(resolve, 100));
        
        try {
            const canvas = await html2canvas(gradientContainer, {
                 useCORS: true,
                 backgroundColor: null
            });
            const link = document.createElement('a');
            link.download = 'shinyfx-image.png';
            link.href = canvas.toDataURL('image/png');
            link.click();
        } catch(err) {
            console.error("Error saving PNG:", err);
            alert("Sorry, an error occurred while saving the image.");
        } finally {
            if (wasActive) toggleShinyFX();
            saveBtn.textContent = 'Save Photo';
            saveBtn.disabled = false;
        }
    }
</script>

</body>
</html>
