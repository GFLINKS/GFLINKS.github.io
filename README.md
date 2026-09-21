<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GFLINK NEW ATIVOS</title>
    <!-- FontAwesome para os Ícones -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: #0f172a;
            color: #f8fafc;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 40px 20px;
        }

        header {
            text-align: center;
            margin-bottom: 40px;
        }

        header h1 {
            font-size: 2rem;
            color: #ffffff;
            font-weight: 700;
            letter-spacing: 1px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
        }

        header h1 i {
            color: #3b82f6;
        }

        .container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
            gap: 25px;
            width: 100%;
            max-width: 1280px;
        }

        .card {
            background-color: #1e293b;
            border: 1px solid #334155;
            border-radius: 12px;
            padding: 24px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.4);
            display: flex;
            flex-direction: column;
        }

        .card-title {
            font-size: 1.05rem;
            font-weight: 700;
            color: #38bdf8;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: 20px;
            padding-bottom: 12px;
            border-bottom: 1px solid #334155;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .link-list {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .link-btn {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 14px 16px;
            background-color: #0f172a;
            color: #e2e8f0;
            text-decoration: none;
            border-radius: 8px;
            border: 1px solid #334155;
            font-size: 0.9rem;
            font-weight: 600;
            transition: all 0.2s ease-in-out;
        }

        .link-btn:hover {
            background-color: #334155;
            border-color: #38bdf8;
            color: #ffffff;
            transform: translateY(-2px);
        }

        .link-btn i {
            color: #64748b;
            font-size: 1.1rem;
            width: 20px;
            text-align: center;
            transition: color 0.2s ease;
        }

        .link-btn:hover i {
            color: #38bdf8;
        }

        @media (max-width: 768px) {
            .container {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>

    <header>
        <h1><i class="fa-solid fa-network-wired"></i> GFLINK NEW ATIVOS</h1>
    </header>

    <div class="container">
        <!-- COLUNA 1 -->
        <div class="card">
            <div class="card-title">
                <i class="fa-solid fa-server"></i> GFLINK - ATIVOS
            </div>
            <div class="link-list">
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-link"></i> CONTROLE DE LINK VIVO
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-signal"></i> PLANO MÓVEL - VIVO - 5G
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-table"></i> PLANILHA ATIVOS - LINOVO - BRASIL TELECOM - CLARO
                </a>
            </div>
        </div>

        <!-- COLUNA 2 -->
        <div class="card">
            <div class="card-title">
                <i class="fa-solid fa-shield-halved"></i> SISTEMA DE MONITORAMENTO E PORTARIA
            </div>
            <div class="link-list">
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-location-dot"></i> RASTREADORES - VEHICLE
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-desktop"></i> MONI - WEB
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-circle-nodes"></i> SIGMA - WEB
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-video"></i> SISTEMA VMS
                </a>
            </div>
        </div>

        <!-- COLUNA 3 -->
        <div class="card">
            <div class="card-title">
                <i class="fa-solid fa-sliders"></i> UTILITÁRIOS E GESTÃO
            </div>
            <div class="link-list">
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-comments"></i> GF CHAT
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-key"></i> MEU GERENCIADOR DE SENHAS
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-globe"></i> SITE INSTITUCIONAL
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-building-shield"></i> SITE INSTITUCIONAL GUARDIAN
                </a>
                <a href="#" class="link-btn" target="_blank">
                    <i class="fa-solid fa-headset"></i> CHAMADOS - ATENDIMENTO
                </a>
            </div>
        </div>
    </div>

</body>
</html>
