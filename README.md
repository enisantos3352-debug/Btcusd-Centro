
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BTCUSD • Mira Total MT5</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            background-color: #0b0e11;
            color: #d1d4dc;
            font-family: Arial, sans-serif;
            overflow: hidden;
        }
        .tela-mira {
            position: relative;
            width: 100vw;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .linha-v {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 1px;
            background: rgba(41, 98, 255, 0.4);
            border-left: 1px dashed #2962FF;
        }
        .linha-h {
            position: absolute;
            left: 0;
            right: 0;
            height: 1px;
            background: rgba(255, 153, 0, 0.4);
            border-top: 1px dashed #ff9900;
        }
        .caixa-preco-centro {
            position: relative;
            z-index: 10;
            background: #131722;
            border: 2px solid #ff9900;
            padding: 12px 20px;
            border-radius: 8px;
            text-align: center;
            box-shadow: 0 4px 15px rgba(0,0,0,0.8);
            max-width: 280px;
            width: 85%;
        }
        .caixa-preco-centro .label {
            font-size: 9px;
            color: #848e9c;
            text-transform: uppercase;
            margin-bottom: 2px;
        }
        .caixa-preco-centro .valor {
            font-size: 24px;
            font-weight: bold;
            color: #ffffff;
            margin-bottom: 6px;
        }
        .caixa-preco-centro .valor-media {
            font-size: 14px;
            font-weight: bold;
            color: #ff9900;
            border-top: 1px solid #2a2e39;
            padding-top: 6px;
        }
        .painel-inferior {
            position: absolute;
            bottom: 10px;
            left: 10px;
            right: 10px;
            background: rgba(19, 23, 34, 0.95);
            border: 1px solid #2a2e39;
            border-radius: 6px;
            padding: 8px;
            display: flex;
            justify-content: space-around;
            text-align: center;
            font-size: 10px;
            z-index: 20;
        }
        .info-item span {
            display: block;
            font-weight: bold;
            color: #fff;
            margin-top: 2px;
        }
    </style>
</head>
<body>

    <div class="tela-mira">
        <div class="linha-v"></div>
        <div class="linha-h"></div>

        <div class="caixa-preco-centro">
            <div class="label">Cruzamento / Preço M1</div>
            <div class="valor" id="lbl-preco">Carregando...</div>
            <div class="valor-media" id="lbl-media-centro">Média 39: --</div>
        </div>

        <div class="painel-inferior">
            <div class="info-item">Máxima<span id="lbl-max">--</span></div>
            <div class="info-item">Mínima<span id="lbl-min">--</span></div>
        </div>
    </div>

    <script>
        function atualizarMira() {
            fetch('https://api.binance.com/api/v3/klines?symbol=BTCUSDT&interval=1m&limit=60')
                .then(res => res.json())
                .then(raw => {
                    const dados = raw.map(d => ({
                        high: parseFloat(d[2]),
                        low: parseFloat(d[3]),
                        close: parseFloat(d[4])
                    }));

                    const precoAtual = dados[dados.length - 1].close;
                    const maxima = Math.max(...dados.map(d => d.high));
                    const minima = Math.min(...dados.map(d => d.low));

                    let soma = 0;
                    let qtd = Math.min(39, dados.length);
                    for(let i = 0; i < qtd; i++) {
                        soma += dados[dados.length - 1 - i].close;
                    }
                    let media39 = soma / qtd;

                    document.getElementById('lbl-preco').innerText = '$ ' + precoAtual.toFixed(2);
                    document.getElementById('lbl-media-centro').innerText = 'Média 39: $ ' + media39.toFixed(2);
                    document.getElementById('lbl-max').innerText = '$ ' + maxima.toFixed(2);
                    document.getElementById('lbl-min').innerText = '$ ' + minima.toFixed(2);
                })
                .catch(() => {
                    document.getElementById('lbl-preco').innerText = "Erro de Conexão";
                });
        }

        atualizarMira();
        setInterval(atualizarMira, 5000);
    </script>
</body>
</html>
