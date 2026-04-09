<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>Six Seven Particle Animation</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #1a5fb4; /* Синій фон як на відео */
        }
        canvas {
            display: block;
        }
        .instruction {
            position: absolute;
            bottom: 20px;
            width: 100%;
            text-align: center;
            color: white;
            font-family: sans-serif;
            pointer-events: none;
            opacity: 0.7;
        }
    </style>
</head>
<body>

<div class="instruction">Затисніть мишку, щоб зібрати текст</div>
<canvas id="canvas"></canvas>

<script>
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

let particles = [];
let targetPoints = [];
const particleCount = 9999; // Кількість рожевих цяток
let isPressed = false;

// Налаштування розмірів
function resize() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    prepareText();
}

// Функція для створення координат тексту "SIX SEVEN"
function prepareText() {
    const tempCanvas = document.createElement('canvas');
    const tempCtx = tempCanvas.getContext('2d');
    tempCanvas.width = canvas.width;
    tempCanvas.height = canvas.height;

    // Малюємо текст для сканування
    const fontSize = Math.min(canvas.width / 6, 120);
    tempCtx.fillStyle = 'black';
    tempCtx.font = `bold ${fontSize}px sans-serif`;
    tempCtx.textAlign = 'center';
    tempCtx.textBaseline = 'middle';
    tempCtx.fillText('SIX SEVEN', canvas.width / 2, canvas.height / 2);

    // Отримуємо дані пікселів
    const imageData = tempCtx.getImageData(0, 0, canvas.width, canvas.height).data;
    targetPoints = [];

    // Скануємо кожен 4-й піксель, щоб знайти чорні (де є текст)
    for (let y = 0; y < canvas.height; y += 4) {
        for (let x = 0; x < canvas.width; x += 4) {
            const index = (y * canvas.width + x) * 4;
            if (imageData[index + 3] > 128) {
                targetPoints.push({ x, y });
            }
        }
    }
}

class Particle {
    constructor() {
        this.init();
    }

    init() {
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
        // Початкова випадкова швидкість (хаотичний рух)
        this.vx = (Math.random() - 0.5) * 2;
        this.vy = (Math.random() - 0.5) * 2;
        this.radius = Math.random() * 2 + 1;
        this.color = '#ff69b4'; // Рожевий колір
        this.target = targetPoints[Math.floor(Math.random() * targetPoints.length)];
    }

    update() {
        if (isPressed && this.target) {
            // Летимо до цілі (тексту)
            const dx = this.target.x - this.x;
            const dy = this.target.y - this.y;
            this.x += dx * 0.15; // Швидкість притягання
            this.y += dy * 0.15;
        } else {
            // Просто хаотично літаємо
            this.x += this.vx;
            this.y += this.vy;

            // Відштовхування від стінок
            if (this.x < 0 || this.x > canvas.width) this.vx *= -1;
            if (this.y < 0 || this.y > canvas.height) this.vy *= -1;
        }
    }

    draw() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = this.color;
        ctx.fill();
    }
}

function initParticles() {
    particles = [];
    for (let i = 0; i < particleCount; i++) {
        particles.push(new Particle());
    }
}

function animate() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    particles.forEach(p => {
        p.update();
        p.draw();
    });
    requestAnimationFrame(animate);
}

// Події мишки/тачу
window.addEventListener('mousedown', () => isPressed = true);
window.addEventListener('mouseup', () => isPressed = false);
window.addEventListener('touchstart', (e) => { e.preventDefault(); isPressed = true; });
window.addEventListener('touchend', () => isPressed = false);
window.addEventListener('resize', resize);

// Старт
resize();
initParticles();
animate();

</script>
</body>
</html>
