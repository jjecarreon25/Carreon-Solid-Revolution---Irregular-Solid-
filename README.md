<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Solid Revolution Lab</title>

<style>
body {
    margin: 0;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #0b0e14;
    font-family: 'Segoe UI', sans-serif;
    color: #e0e0e0;
    padding: 20px;
}
.glass {
    background: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(10px);
    border-radius: 20px;
    padding: 25px;
    width: 100%;
    max-width: 420px;
    border: 1px solid rgba(255,255,255,0.1);
}
canvas {
    width: 100%;
    height: 180px;
    background: #000;
    border-radius: 12px;
    margin: 15px 0;
}
select, input {
    width: 100%;
    padding: 12px;
    margin: 8px 0;
    border-radius: 8px;
    border: 1px solid #444;
    background: #1a1d23;
    color: white;
}
button {
    width: 100%;
    padding: 15px;
    border-radius: 8px;
    border: none;
    background: #00ffcc;
    font-weight: bold;
    cursor: pointer;
}
.res {
    font-size: 1.4rem;
    color: #00ffcc;
    margin-top: 10px;
    text-align: center;
}
.info {
    font-size: 0.9rem;
    color: #aaa;
    text-align: center;
}
</style>
</head>

<body>

<div class="glass">
    <h2 style="color:#00ffcc">Solid Visualizer</h2>

    <select id="shape">
        <option value="exp">f(x) = e^(0.5x)</option>
        <option value="para">f(x) = √x + 2</option>
        <option value="sin">f(x) = sin(x) + 2</option>
    </select>

    <canvas id="canvas"></canvas>

    <div style="display:flex; gap:10px;">
        <input type="number" id="a" value="0">
        <input type="number" id="b" value="4">
    </div>

    <button onclick="run()">Compute Volume</button>

    <div class="info" id="formula"></div>
    <div class="res" id="out">---</div>
</div>

<script>
const cvs = document.getElementById('canvas');
const ctx = cvs.getContext('2d');
cvs.width = 400;
cvs.height = 200;

const shapes = {
    exp: {
        f: (x) => Math.exp(0.5 * x),
        label: "e^(0.5x)"
    },
    para: {
        f: (x) => x >= 0 ? Math.sqrt(x) + 2 : NaN,
        label: "√x + 2"
    },
    sin: {
        f: (x) => Math.sin(x) + 2,
        label: "sin(x) + 2"
    }
};

function run() {
    const shapeKey = document.getElementById('shape').value;
    const f = shapes[shapeKey].f;
    const label = shapes[shapeKey].label;

    const a = parseFloat(document.getElementById('a').value);
    const b = parseFloat(document.getElementById('b').value);

    // ✅ Validation
    if (isNaN(a) || isNaN(b) || a >= b) {
        alert("Invalid interval. Make sure a < b.");
        return;
    }

    // Prevent sqrt negative crash
    if (shapeKey === "para" && a < 0) {
        alert("√x is undefined for negative x.");
        return;
    }

    // ✅ Numerical Integration
    let n = 1000;
    let dx = (b - a) / n;
    let vol = 0;

    for (let i = 0; i < n; i++) {
        let x = a + i * dx;
        let r = f(x);
        if (!isNaN(r)) {
            vol += Math.PI * r * r * dx;
        }
    }

    document.getElementById('out').innerText =
        vol.toFixed(2) + " u³";

    // ✅ Show formula
    document.getElementById('formula').innerText =
        `V = π ∫[${a}, ${b}] (${label})² dx`;

    // ✅ Auto-scale graph
    let maxY = 0;
    for (let x = 0; x <= 10; x += 0.1) {
        let y = f(x);
        if (!isNaN(y) && y > maxY) maxY = y;
    }

    let scaleY = 160 / maxY;

    // Draw
    ctx.clearRect(0,0,400,200);
    ctx.strokeStyle = "#00ffcc";
    ctx.beginPath();

    for (let x = 0; x <= 400; x++) {
        let mX = (x / 400) * 10;
        let y = f(mX);

        if (isNaN(y)) continue;

        let sY = 180 - (y * scaleY);

        if (x === 0) ctx.moveTo(x, sY);
        else ctx.lineTo(x, sY);

        if (mX >= a && mX <= b) {
            ctx.fillStyle = "rgba(0,255,204,0.15)";
            ctx.fillRect(x, sY, 1, 180 - sY);
        }
    }

    ctx.stroke();
}

run();
</script>

</body>
</html>
