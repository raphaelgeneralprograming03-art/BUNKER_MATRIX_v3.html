<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BUNKER_MATRIX_v3 - Triad Defense & Radiation Shielding</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
        }
        body {
            background-color: #05080c;
            color: #00ffcc;
            font-family: 'Courier New', Courier, monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 10px;
        }
        .container {
            width: 100%;
            max-width: 1100px;
            background: #0a1118;
            border: 2px solid #00ffcc;
            box-shadow: 0 0 25px rgba(0, 255, 204, 0.25);
            border-radius: 8px;
            overflow: hidden;
        }
        .header {
            background: #030609;
            padding: 12px;
            text-align: center;
            border-bottom: 2px solid #00ffcc;
            font-weight: bold;
            letter-spacing: 1.5px;
            color: #00ffcc;
            text-shadow: 0 0 8px #00ffcc;
        }
        .hud-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 8px;
            padding: 10px;
            background: #070d14;
            border-bottom: 1px solid #162838;
        }
        .hud-card {
            background: #04080d;
            border: 1px solid #00ffcc33;
            padding: 6px 10px;
            border-radius: 4px;
        }
        .hud-title {
            font-size: 0.70rem;
            color: #7a9bb8;
            text-transform: uppercase;
        }
        .hud-value {
            font-size: 1rem;
            font-weight: bold;
            color: #ffffff;
            margin-top: 2px;
        }
        .hud-value.alert { color: #ff3366; text-shadow: 0 0 6px #ff3366; }
        .hud-value.active { color: #00ffcc; text-shadow: 0 0 6px #00ffcc; }
        .hud-value.warning { color: #ffcc00; }

        canvas {
            display: block;
            width: 100%;
            height: 500px;
            background-color: #020407;
            cursor: crosshair;
        }

        .controls {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            padding: 10px;
            background: #060b10;
            justify-content: center;
            align-items: center;
            border-top: 1px solid #162838;
        }

        button {
            background: #0b1721;
            color: #00ffcc;
            border: 1px solid #00ffcc;
            padding: 8px 14px;
            font-family: inherit;
            font-size: 0.80rem;
            font-weight: bold;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.2s ease;
            text-shadow: 0 0 4px #00ffcc;
        }

        button:hover {
            background: #00ffcc;
            color: #030609;
            box-shadow: 0 0 12px #00ffcc;
        }

        button.btn-danger {
            color: #ff3366;
            border-color: #ff3366;
            text-shadow: 0 0 4px #ff3366;
        }
        button.btn-danger:hover {
            background: #ff3366;
            color: #000;
            box-shadow: 0 0 12px #ff3366;
        }

        button.btn-warning {
            color: #ffcc00;
            border-color: #ffcc00;
            text-shadow: 0 0 4px #ffcc00;
        }
        button.btn-warning:hover {
            background: #ffcc00;
            color: #000;
            box-shadow: 0 0 12px #ffcc00;
        }

        .terminal {
            background: #020406;
            padding: 8px 12px;
            font-size: 0.75rem;
            color: #00ffccaa;
            height: 65px;
            overflow-y: auto;
            border-top: 1px solid #162838;
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        BUNKER_MATRIX_v3 // TRIAD DEFENSE & ADVANCED NUCLEAR SHIELDING
    </div>

    <!-- PAINEL DE LEITURAS HUD -->
    <div class="hud-grid">
        <div class="hud-card">
            <div class="hud-title">TEMP. EXTERNA</div>
            <div class="hud-value" id="hud-temp">25 °C</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">PRESSÃO DE IMPACTO</div>
            <div class="hud-value" id="hud-press">1.00 atm</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">RADIAÇÃO (GAMA / BETA / ALFA)</div>
            <div class="hud-value" id="hud-rad">0.00 / 0 / 0</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">TRÍADE DEFENSIVA (MHD)</div>
            <div class="hud-value" id="hud-mhd">DESATIVADA</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">BLINDAGEM CONCRETO</div>
            <div class="hud-value active" id="hud-wall-concrete">100%</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">CAMADA CHUMBO</div>
            <div class="hud-value active" id="hud-wall-lead">100%</div>
        </div>
        <div class="hud-card">
            <div class="hud-title">MADEIRA ESPECIAL</div>
            <div class="hud-value active" id="hud-wall-wood">100%</div>
        </div>
    </div>

    <!-- TELA DA SIMULAÇÃO (CANVAS 2D) -->
    <canvas id="simCanvas" width="1050" height="500"></canvas>

    <!-- BOTOES DE CONTROLE -->
    <div class="controls">
        <button id="btn-toggle">⏸️ PARAR SIMULAÇÃO</button>
        <button id="btn-reset">🔄 REINICIAR</button>
        <button id="btn-triad" class="btn-warning">⚡ ATIVAR TRÍADE (MHD + CAMPO TÉRMICO)</button>
        <button id="btn-launch" class="btn-danger">☢️ DETONAR BOMBA EXTREMA (10 KM)</button>
        <button id="btn-view">🔬 ALTERAR MODO DE VISÃO (GERAL / CORTE DAS PAREDES)</button>
    </div>

    <!-- LOG TERMINAL DO SISTEMA -->
    <div class="terminal" id="terminal-log">
        [SYSTEM] Bunker Matrix v3 inicializado. Camadas de Chumbo, Concreto e Madeira Anti-Radiação integradas.
    </div>
</div>

<script>
    const canvas = document.getElementById('simCanvas');
    const ctx = canvas.getContext('2d');

    // Estados da Simulação
    let isRunning = true;
    let triadActive = false;
    let viewMode = 'OVERVIEW'; // 'OVERVIEW' ou 'WALL_CROSS_SECTION'

    // Status Físicos
    let externalTemp = 25; // °C
    let impactPressure = 1.00; // atm
    let radGamma = 0.00; // Sv/h
    let radBeta = 0; // P/s
    let radAlpha = 0; // P/s

    // Integridade das Paredes
    let wallConcrete = 100;
    let wallLead = 100;
    let wallWood = 100;

    // Entidades da simulação
    let particles = [];
    let shockwave = null;
    let thermalWave = null;

    // Coordenadas
    const GROUND_Y = 320;
    const BUNKER_X = 220;
    const BUNKER_Y = 390;
    const DETONATION_X = 900;

    function log(msg) {
        const term = document.getElementById('terminal-log');
        term.innerHTML = `> ${msg}<br>` + term.innerHTML;
    }

    function updateHUD() {
        // Temperatura
        const tempElem = document.getElementById('hud-temp');
        if (externalTemp > 1000000) {
            tempElem.innerText = `${(externalTemp / 1000000).toFixed(1)}M °C`;
            tempElem.className = 'hud-value alert';
        } else if (externalTemp > 1000) {
            tempElem.innerText = `${(externalTemp / 1000).toFixed(1)}k °C`;
            tempElem.className = 'hud-value warning';
        } else {
            tempElem.innerText = `${Math.round(externalTemp)} °C`;
            tempElem.className = 'hud-value';
        }

        // Pressao
        const pressElem = document.getElementById('hud-press');
        pressElem.innerText = `${impactPressure.toFixed(2)} atm`;
        pressElem.className = impactPressure > 5.0 ? 'hud-value alert' : 'hud-value';

        // Radiações
        document.getElementById('hud-rad').innerText = `${radGamma.toFixed(1)}Sv/h | B:${radBeta} | A:${radAlpha}`;

        // Tríade
        const triadElem = document.getElementById('hud-mhd');
        if (triadActive) {
            triadElem.innerText = "ATIVADA (350 MW)";
            triadElem.className = "hud-value active";
        } else {
            triadElem.innerText = "DESATIVADA";
            triadElem.className = "hud-value";
        }

        // Paredes
        document.getElementById('hud-wall-concrete').innerText = `${Math.round(wallConcrete)}%`;
        document.getElementById('hud-wall-lead').innerText = `${Math.round(wallLead)}%`;
        document.getElementById('hud-wall-wood').innerText = `${Math.round(wallWood)}%`;
    }

    // Eventos dos Botões
    document.getElementById('btn-toggle').addEventListener('click', (e) => {
        isRunning = !isRunning;
        e.target.innerText = isRunning ? "⏸️ PARAR SIMULAÇÃO" : "▶️ INICIAR SIMULAÇÃO";
        log(isRunning ? "Simulação retomada." : "Simulação pausada.");
    });

    document.getElementById('btn-reset').addEventListener('click', () => {
        particles = [];
        shockwave = null;
        thermalWave = null;
        externalTemp = 25;
        impactPressure = 1.00;
        radGamma = 0; radBeta = 0; radAlpha = 0;
        wallConcrete = 100; wallLead = 100; wallWood = 100;
        triadActive = false;
        updateHUD();
        log("Sistema reiniciado. Integridade das camadas restaurada a 100%.");
    });

    document.getElementById('btn-triad').addEventListener('click', () => {
        triadActive = !triadActive;
        updateHUD();
        log(triadActive ? "⚡ TRÍADE DEFENSIVA ATIVADA: Campo MHD de Alta Frequência, Barreira Térmica e Neutralizador Eletrônico." : "Tríade Defensiva Desativada.");
    });

    document.getElementById('btn-view').addEventListener('click', () => {
        viewMode = viewMode === 'OVERVIEW' ? 'WALL_CROSS_SECTION' : 'OVERVIEW';
        log(`Modo de visão alterado para: ${viewMode === 'OVERVIEW' ? 'Visão Geral do Terreno' : 'Corte Detalhado das Camadas da Parede'}.`);
    });

    document.getElementById('btn-launch').addEventListener('click', () => {
        // Disparar detonação extrema
        externalTemp = 15000000; // 15 Milhões de °C
        impactPressure = 45.0; // 45 atm
        radGamma = 850.0;
        radBeta = 1200;
        radAlpha = 2500;

        shockwave = { x: DETONATION_X, y: GROUND_Y, radius: 10, maxRadius: 1000, speed: 7 };
        thermalWave = { x: DETONATION_X, y: GROUND_Y, radius: 5, maxRadius: 900, speed: 9 };

        // Gerar Partículas de Radiação (Alfa, Beta, Gama)
        particles = [];
        for (let i = 0; i < 300; i++) {
            let type = Math.random() < 0.4 ? 'gamma' : (Math.random() < 0.7 ? 'beta' : 'alpha');
            particles.push({
                x: DETONATION_X + (Math.random() - 0.5) * 40,
                y: GROUND_Y + (Math.random() - 0.5) * 40,
                vx: - (Math.random() * 8 + 3),
                vy: (Math.random() - 0.5) * 6,
                type: type,
                alive: true
            });
        }

        log("☢️ ATENÇÃO! Detonação Nuclear Extrema efetuada a 10 km! Radiações Alfa, Beta, Gama e Pulso Térmico emitidos.");
    });

    // Atualização
    function update() {
        if (!isRunning) return;

        // Atualizar Onda Térmica
        if (thermalWave) {
            thermalWave.radius += thermalWave.speed;
            if (thermalWave.radius > thermalWave.maxRadius) thermalWave = null;
        }

        // Atualizar Onda de Choque de Pressão
        if (shockwave) {
            shockwave.radius += shockwave.speed;

            // Verificar choque no Bunker
            let distToBunker = Math.hypot(shockwave.x - BUNKER_X, shockwave.y - BUNKER_Y);
            if (Math.abs(shockwave.radius - distToBunker) < shockwave.speed) {
                if (triadActive) {
                    impactPressure = 2.1;
                    externalTemp = 1200;
                    wallConcrete = Math.max(90, wallConcrete - 0.5);
                    log("🛡️ TRÍADE DEFENSIVA ABSORVEU 95% DO PULSO TÉRMICO E ONDA DE PRESSÃO!");
                } else {
                    impactPressure = 38.5;
                    externalTemp = 8500000;
                    wallConcrete = Math.max(15, wallConcrete - 40);
                    wallLead = Math.max(40, wallLead - 25);
                    log("⚠️ DANO ESTRUTURAL GRAVE! Sem a Tríade, a onda de calor e pressão desgastou o Concreto e Chumbo.");
                }
            }

            if (shockwave.radius > shockwave.maxRadius) shockwave = null;
        }

        // Atualizar Partículas de Radiação
        particles.forEach(p => {
            if (!p.alive) return;
            p.x += p.vx;
            p.y += p.vy;

            // Interação com a Tríade MHD
            if (triadActive && Math.hypot(p.x - BUNKER_X, p.y - BUNKER_Y) < 140) {
                if (p.type === 'alpha' || p.type === 'beta') {
                    p.alive = false; // Desviadas/Repelidas pelo campo MHD
                } else if (p.type === 'gamma' && Math.random() < 0.6) {
                    p.alive = false; // Parcialmente atenuadas
                }
            }

            // Interação com as Paredes do Bunker
            if (p.x <= BUNKER_X + 60 && p.x >= BUNKER_X - 60 && p.y >= BUNKER_Y - 50) {
                if (p.type === 'alpha') {
                    p.alive = false; // Retida na camada externa de Concreto
                    wallConcrete = Math.max(0, wallConcrete - 0.01);
                } else if (p.type === 'beta') {
                    if (Math.random() < 0.8) {
                        p.alive = false; // Retida no Concreto / Chumbo
                        wallLead = Math.max(0, wallLead - 0.02);
                    }
                } else if (p.type === 'gamma') {
                    if (Math.random() < 0.9) {
                        p.alive = false; // Absorvida pela espessa camada de Chumbo
                        wallLead = Math.max(0, wallLead - 0.04);
                    } else {
                        // Neutrons/Gama residual absorvido pela Madeira Anti-Radiação/Polímero
                        p.alive = false;
                        wallWood = Math.max(0, wallWood - 0.01);
                    }
                }
            }

            if (p.x < 0 || p.y < 0 || p.y > canvas.height) p.alive = false;
        });

        // Dissipação de leituras
        if (externalTemp > 25) externalTemp *= 0.985;
        if (impactPressure > 1.00) impactPressure = Math.max(1.00, impactPressure - 0.15);
        if (radGamma > 0) radGamma *= 0.98;
        if (radBeta > 0) radBeta = Math.max(0, radBeta - 5);
        if (radAlpha > 0) radAlpha = Math.max(0, radAlpha - 10);

        updateHUD();
    }

    // Desenho na Tela
    function draw() {
        ctx.fillStyle = '#020407';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        if (viewMode === 'OVERVIEW') {
            drawOverview();
        } else {
            drawCrossSection();
        }
    }

    // VISTA 1: TERRENO E ESTRUTURA GERAL
    function drawOverview() {
        // Céu e Grade SROS
        ctx.strokeStyle = '#00ffcc11';
        ctx.lineWidth = 1;
        for (let x = 0; x < canvas.width; x += 50) {
            ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
        }

        // Solo Isolado Plano
        ctx.fillStyle = '#0a141d';
        ctx.fillRect(0, GROUND_Y, canvas.width, canvas.height - GROUND_Y);
        ctx.strokeStyle = '#00ffcc88';
        ctx.beginPath(); ctx.moveTo(0, GROUND_Y); ctx.lineTo(canvas.width, GROUND_Y); ctx.stroke();

        // Onda Térmica
        if (thermalWave) {
            ctx.fillStyle = 'rgba(255, 68, 0, 0.15)';
            ctx.beginPath();
            ctx.arc(thermalWave.x, thermalWave.y, thermalWave.radius, 0, Math.PI * 2);
            ctx.fill();
        }

        // Onda de Choque de Pressão
        if (shockwave) {
            ctx.strokeStyle = '#ffcc00';
            ctx.lineWidth = 4;
            ctx.beginPath();
            ctx.arc(shockwave.x, shockwave.y, shockwave.radius, 0, Math.PI * 2);
            ctx.stroke();
        }

        // TRÍADE DE DEFESA (MHD + ESCUDO ELETRÔNICO)
        if (triadActive) {
            ctx.save();
            ctx.strokeStyle = '#00ffcc';
            ctx.lineWidth = 3;
            ctx.shadowBlur = 20;
            ctx.shadowColor = '#00ffcc';
            ctx.beginPath();
            ctx.arc(BUNKER_X, BUNKER_Y, 130, 0, Math.PI * 2);
            ctx.stroke();

            // Campo de Plasma
            ctx.strokeStyle = 'rgba(0, 255, 204, 0.3)';
            ctx.beginPath();
            ctx.arc(BUNKER_X, BUNKER_Y, 140, 0, Math.PI * 2);
            ctx.stroke();
            ctx.restore();
        }

        // Bunker Desenho EXTERNO
        ctx.fillStyle = '#182b3a';
        ctx.fillRect(BUNKER_X - 60, BUNKER_Y - 40, 120, 90);
        ctx.strokeStyle = '#00ffcc';
        ctx.strokeRect(BUNKER_X - 60, BUNKER_Y - 40, 120, 90);

        ctx.fillStyle = '#00ffcc';
        ctx.font = '11px Courier New';
        ctx.fillText("BUNKER (SUBTERRÂNEO)", BUNKER_X - 55, BUNKER_Y + 10);

        // Partículas de Radiação Render
        particles.forEach(p => {
            if (!p.alive) return;
            ctx.beginPath();
            if (p.type === 'alpha') {
                ctx.fillStyle = '#ff0055'; // Alfa = Magenta Heavy
                ctx.arc(p.x, p.y, 4, 0, Math.PI * 2);
            } else if (p.type === 'beta') {
                ctx.fillStyle = '#00ccff'; // Beta = Azul
                ctx.arc(p.x, p.y, 2.5, 0, Math.PI * 2);
            } else {
                ctx.fillStyle = '#ffff00'; // Gama = Amarelo
                ctx.arc(p.x, p.y, 1.5, 0, Math.PI * 2);
            }
            ctx.fill();
        });
    }

    // VISTA 2: CORTE DETALHADO DAS CAMADAS DAS PAREDES
    function drawCrossSection() {
        ctx.fillStyle = '#00ffcc';
        ctx.font = '14px Courier New';
        ctx.fillText("CORTE TRANSVERSAL: CAMADAS DE BLINDAGEM MULTI-PROTEÇÃO", 40, 40);

        const startX = 200;
        const widthCon = 160;
        const widthLead = 120;
        const widthWood = 100;
        const startY = 80;
        const height = 360;

        // 1. CAMADA 1: CONCRETO ARMADO TÉRMICO (EXTERNA)
        ctx.fillStyle = `rgba(100, 110, 120, ${wallConcrete / 100})`;
        ctx.fillRect(startX, startY, widthCon, height);
        ctx.strokeStyle = '#ffffff';
        ctx.strokeRect(startX, startY, widthCon, height);
        ctx.fillStyle = '#ffffff';
        ctx.fillText("1. CONCRETO TÉRMICO", startX + 10, startY + 30);
        ctx.fillText(`Absorção de Pressão/Calor`, startX + 10, startY + 50);

        // 2. CAMADA 2: CHUMBO DENSO D-36 (INTERMEDIÁRIA)
        ctx.fillStyle = `rgba(50, 60, 75, ${wallLead / 100})`;
        ctx.fillRect(startX + widthCon, startY, widthLead, height);
        ctx.strokeStyle = '#00ffcc';
        ctx.strokeRect(startX + widthCon, startY, widthLead, height);
        ctx.fillStyle = '#00ffcc';
        ctx.fillText("2. CHUMBO DENSO", startX + widthCon + 10, startY + 30);
        ctx.fillText(`Bloqueio Rad. Gama/Beta`, startX + widthCon + 10, startY + 50);

        // 3. CAMADA 3: MADEIRA ANTI-RADIAÇÃO / POLÍMERO DE BORO (INTERNA)
        ctx.fillStyle = `rgba(120, 70, 30, ${wallWood / 100})`;
        ctx.fillRect(startX + widthCon + widthLead, startY, widthWood, height);
        ctx.strokeStyle = '#ffcc00';
        ctx.strokeRect(startX + widthCon + widthLead, startY, widthWood, height);
        ctx.fillStyle = '#ffcc00';
        ctx.fillText("3. MADEIRA/BORO", startX + widthCon + widthLead + 5, startY + 30);
        ctx.fillText(`Absorção Nêutrons`, startX + widthCon + widthLead + 5, startY + 50);

        // 4. INTERIOR DO BUNKER (HABITÁVEL)
        ctx.fillStyle = '#05121c';
        ctx.fillRect(startX + widthCon + widthLead + widthWood, startY, 250, height);
        ctx.fillStyle = '#00ffcc';
        ctx.fillText("NÚCLEO HABITÁVEL (100% PROTEGIDO)", startX + widthCon + widthLead + widthWood + 10, startY + 180);

        // Partículas caindo nas camadas no modo de visão
        particles.forEach(p => {
            if (!p.alive) return;
            let displayX = startX - 100 + (p.x % 120);
            let displayY = startY + (Math.abs(p.y * 3) % (height - 20));

            ctx.beginPath();
            if (p.type === 'alpha') { ctx.fillStyle = '#ff0055'; ctx.arc(displayX, displayY, 5, 0, Math.PI*2); }
            else if (p.type === 'beta') { ctx.fillStyle = '#00ccff'; ctx.arc(displayX, displayY, 3, 0, Math.PI*2); }
            else { ctx.fillStyle = '#ffff00'; ctx.arc(displayX, displayY, 2, 0, Math.PI*2); }
            ctx.fill();
        });
    }

    // Loop
    function loop() {
        update();
        draw();
        requestAnimationFrame(loop);
    }

    loop();
</script>
</body>
</html>
