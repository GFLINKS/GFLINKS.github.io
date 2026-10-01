<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GFLINKS - Sistema de Orçamentos, Gestão & PDFs</title>
    <style>
        :root {
            --primary: #1e3a8a;
            --primary-hover: #1d4ed8;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --border: #cbd5e1;
            --text: #0f172a;
            --danger: #ef4444;
            --yellow: #f59e0b;
            --green: #10b981;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
        }

        /* MENU DE NAVEGAÇÃO SUPERIOR */
        .top-nav {
            display: flex;
            justify-content: center;
            gap: 12px;
            margin-bottom: 25px;
            background: var(--card-bg);
            padding: 12px;
            border-radius: 10px;
            border: 1px solid var(--border);
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            flex-wrap: wrap;
        }

        .nav-btn {
            background-color: #f1f5f9;
            color: #334155;
            border: 1px solid var(--border);
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 14px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .nav-btn:hover {
            background-color: #e2e8f0;
        }

        .nav-btn.active {
            background-color: var(--primary);
            color: white;
            border-color: var(--primary);
        }

        /* CAIXAS DAS TELAS */
        .card-box {
            background: var(--card-bg);
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            border: 1px solid var(--border);
        }

        .logo-container {
            text-align: center;
            margin-bottom: 20px;
        }

        .logo-container img {
            max-height: 110px;
            max-width: 300px;
            object-fit: contain;
        }

        h1 {
            color: var(--primary);
            border-bottom: 2px solid var(--primary);
            padding-bottom: 10px;
            margin-top: 0;
            font-size: 22px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .header-title {
            font-size: 15px;
            background: #e0e7ff;
            color: #3730a3;
            padding: 6px 12px;
            border-radius: 6px;
            font-weight: bold;
        }

        .section-title {
            font-size: 15px;
            font-weight: bold;
            color: var(--primary);
            margin-top: 25px;
            margin-bottom: 12px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            border-left: 4px solid var(--primary);
            padding-left: 8px;
        }

        .grid-3 {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            margin-bottom: 12px;
        }

        label {
            font-size: 13px;
            font-weight: 600;
            margin-bottom: 5px;
            color: #475569;
        }

        .req {
            color: var(--danger);
            font-weight: bold;
        }

        input[type="text"],
        input[type="date"],
        input[type="time"],
        input[type="number"],
        textarea {
            padding: 10px;
            border: 1px solid var(--border);
            border-radius: 6px;
            font-size: 14px;
            outline: none;
            width: 100%;
            box-sizing: border-box;
            transition: border-color 0.2s;
        }

        input:focus, textarea:focus {
            border-color: var(--primary);
        }

        .input-error {
            border-color: var(--danger) !important;
            background-color: #fef2f2 !important;
        }

        textarea {
            resize: vertical;
            min-height: 65px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        th, td {
            border: 1px solid var(--border);
            padding: 10px;
            text-align: left;
            font-size: 14px;
        }

        th {
            background-color: #f1f5f9;
            color: #334155;
            font-weight: 600;
        }

        .btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 10px 18px;
            border-radius: 6px;
            font-weight: bold;
            cursor: pointer;
            font-size: 14px;
            transition: background 0.2s;
        }

        .btn:hover {
            background-color: var(--primary-hover);
        }

        .btn-danger {
            background-color: var(--danger);
            padding: 6px 10px;
            font-size: 12px;
        }

        .btn-add {
            background-color: var(--green);
            margin-top: 10px;
        }

        .btn-small {
            background-color: #64748b;
            color: white;
            padding: 6px 10px;
            font-size: 11px;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            margin-top: 4px;
        }

        .drop-zone {
            border: 2px dashed var(--primary);
            border-radius: 8px;
            padding: 20px;
            text-align: center;
            background-color: #f1f5f9;
            cursor: pointer;
            transition: background-color 0.2s;
        }

        .drop-zone:hover, .drop-zone.dragover {
            background-color: #e2e8f0;
        }

        .drop-zone-text {
            font-size: 14px;
            color: #334155;
            font-weight: 600;
        }

        .drop-zone-subtext {
            font-size: 12px;
            color: #64748b;
            margin-top: 4px;
        }

        .photo-gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }

        .photo-card {
            border: 1px solid var(--border);
            border-radius: 8px;
            overflow: hidden;
            background: #fff;
            padding: 8px;
            text-align: center;
        }

        .photo-card img {
            width: 100%;
            height: 130px;
            object-fit: cover;
            border-radius: 4px;
        }

        .btn-remove-photo {
            margin-top: 6px;
            background-color: var(--danger);
            color: white;
            border: none;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 11px;
            cursor: pointer;
            width: 100%;
        }

        .actions {
            margin-top: 30px;
            display: flex;
            justify-content: flex-end;
            gap: 10px;
        }

        /* PAINEL DE GESTÃO */
        .metrics-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
            margin-bottom: 25px;
        }

        .metric-card {
            background: var(--card-bg);
            padding: 18px;
            border-radius: 10px;
            border: 1px solid var(--border);
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            text-align: center;
        }

        .metric-card h3 {
            margin: 0;
            font-size: 13px;
            color: #64748b;
            text-transform: uppercase;
        }

        .metric-card .number {
            font-size: 28px;
            font-weight: bold;
            margin-top: 8px;
        }

        .card-total .number { color: var(--primary); }
        .card-pendente .number { color: var(--yellow); }
        .card-enviado .number { color: var(--green); }
        .card-declinado .number { color: var(--danger); }

        .sub-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            border-bottom: 2px solid var(--border);
        }

        .sub-tab-btn {
            padding: 10px 20px;
            background: none;
            border: none;
            font-size: 15px;
            font-weight: bold;
            color: #64748b;
            cursor: pointer;
            border-bottom: 3px solid transparent;
            margin-bottom: -2px;
        }

        .sub-tab-btn.active {
            color: var(--primary);
            border-bottom-color: var(--primary);
        }

        .badge {
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: bold;
            display: inline-block;
        }

        .badge-pendente { background: #fef3c7; color: #b45309; }
        .badge-enviado { background: #d1fae5; color: #047857; }
        .badge-declinado { background: #fee2e2; color: #b91c1c; }

        .btn-action {
            padding: 6px 12px;
            border-radius: 5px;
            border: none;
            font-size: 12px;
            font-weight: bold;
            cursor: pointer;
            color: white;
            transition: opacity 0.2s;
        }

        .btn-action:hover { opacity: 0.85; }
        .btn-enviar { background-color: var(--green); }
        .btn-declinar { background-color: var(--danger); }
        .btn-pendente { background-color: var(--yellow); }

        /* GALERIA & VISUALIZADOR DE PDF */
        .pdf-controls {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            align-items: center;
            flex-wrap: wrap;
        }

        .pdf-viewer-box {
            margin-top: 25px;
            border-top: 2px solid var(--border);
            padding-top: 20px;
        }

        /* AJUSTES PARA IMPRESSÃO EM PDF DO ORÇAMENTO */
        @media print {
            body { background: white; padding: 0; }
            .container { max-width: 100%; }
            .top-nav, .no-print, #painelSection, #galeriaPdfSection { display: none !important; }
            #orcamentoSection { display: block !important; }
            .card-box { box-shadow: none; border: none; padding: 0; }
            input, textarea { border: none !important; background: transparent !important; padding: 0 !important; }
            .photo-card img { height: auto; max-height: 200px; }
            .btn-remove-photo, .req { display: none !important; }
        }
    </style>
</head>
<body>

<div class="container">
    <!-- MENU DE NAVEGAÇÃO PRINCIPAL -->
    <div class="top-nav no-print">
        <button class="nav-btn active" id="btnNavOrcamento" onclick="alternarTela('orcamento')">📝 Criar Orçamento</button>
        <button class="nav-btn" id="btnNavPainel" onclick="alternarTela('painel')">📊 Painel de Gestão</button>
        <button class="nav-btn" id="btnNavGaleria" onclick="alternarTela('galeria')">📂 Galeria & PDFs</button>
    </div>

    <!-- TELA 1: EMISSÃO DE ORÇAMENTO -->
    <div id="orcamentoSection" class="card-box">
        <div class="logo-container">
            <img src="logo.png" alt="Logo da Empresa" onerror="this.style.display='none'">
        </div>

        <h1>
            <span>📝 APP ORÇAMENTO</span>
            <span class="header-title" id="semanaHeader">ORÇAMENTOS</span>
        </h1>

        <div class="section-title">📍 Identificação do Atendimento</div>
        
        <div class="grid-3">
            <div class="form-group">
                <label for="numOrcamento">Nº do Orçamento <span class="req">*</span></label>
                <input type="text" id="numOrcamento" readonly style="font-weight: bold; color: var(--primary); background-color: #f1f5f9;">
                <button class="btn-small no-print" onclick="alterarNumeroManual()">🔄 Alterar Numeração</button>
            </div>
            <div class="form-group">
                <label for="sigla">Sigla do Cliente / Loja <span class="req">*</span></label>
                <input type="text" id="sigla" value="" placeholder="Ex: CNL" oninput="atualizarCabecalho(); removerErro(this);">
            </div>
            <div class="form-group">
                <label for="dataEmissao">Data <span class="req">*</span></label>
                <input type="date" id="dataEmissao" onchange="atualizarCabecalho(); removerErro(this);">
            </div>
        </div>

        <div class="grid-3">
            <div class="form-group">
                <label for="horaEmissao">Hora <span class="req">*</span></label>
                <input type="time" id="horaEmissao" onchange="removerErro(this)">
            </div>
            <div class="form-group">
                <label for="tecnico">Técnico Responsável <span class="req">*</span></label>
                <input type="text" id="tecnico" value="" placeholder="Nome do técnico" oninput="removerErro(this)">
            </div>
            <div class="form-group">
                <label for="semanaManual">Semana do Ano</label>
                <input type="text" id="semanaManual" placeholder="Semana X" readonly>
            </div>
        </div>

        <div class="form-group">
            <label for="motivo">Motivo do Orçamento <span class="req">*</span></label>
            <textarea id="motivo" placeholder="Digite o motivo do orçamento..." oninput="removerErro(this)"></textarea>
        </div>

        <div class="section-title">📦 Peças & Serviços <span class="req">*</span></div>
        <table id="tabelaItens">
            <thead>
                <tr>
                    <th style="width: 75%;">Descrição do Item / Serviço</th>
                    <th style="width: 15%;">Qtd</th>
                    <th class="no-print" style="width: 10%;">Ação</th>
                </tr>
            </thead>
            <tbody id="corpoTabela">
                <tr>
                    <td><input type="text" value="" placeholder="Descrição do item ou serviço" class="item-desc" oninput="removerErro(this)"></td>
                    <td><input type="number" value="1" min="1" class="item-qtd" oninput="removerErro(this)"></td>
                    <td class="no-print"><button class="btn btn-danger" onclick="removerLinha(this)">X</button></td>
                </tr>
            </tbody>
        </table>
        <button class="btn btn-add no-print" onclick="adicionarLinha()">+ Adicionar Item</button>

        <div class="section-title">📷 Evidências Fotográficas <span class="req">*</span></div>
        
        <div class="no-print">
            <div class="drop-zone" id="dropZone" onclick="document.getElementById('fotosInput').click()">
                <div class="drop-zone-text">📸 Clique aqui para escolher as fotos ou Arraste os arquivos</div>
                <div class="drop-zone-subtext">💡 Você também pode **COLAR** uma imagem copiada pressionando **Ctrl + V**!</div>
            </div>
            <input type="file" id="fotosInput" accept="image/*" multiple style="display: none;" onchange="carregarFotos(event)">
        </div>

        <div class="photo-gallery" id="galeriaFotos"></div>

        <div class="actions no-print">
            <button class="btn" onclick="imprimirOuSalvarPDF()">🖨️ Imprimir / Salvar em PDF</button>
        </div>
    </div>

    <!-- TELA 2: PAINEL DE GESTÃO -->
    <div id="painelSection" class="card-box" style="display: none;">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
            <h1 style="border:none; padding:0; margin:0;">📊 Painel de Gestão de Orçamentos</h1>
            <button class="btn" onclick="carregarDadosPainel()">🔄 Atualizar Dados</button>
        </div>

        <!-- MÉTRICAS -->
        <div class="metrics-grid">
            <div class="metric-card card-total">
                <h3>Total Geral</h3>
                <div class="number" id="mTotal">0</div>
            </div>
            <div class="metric-card card-pendente">
                <h3>Pendentes</h3>
                <div class="number" id="mPendente">0</div>
            </div>
            <div class="metric-card card-enviado">
                <h3>Enviados</h3>
                <div class="number" id="mEnviado">0</div>
            </div>
            <div class="metric-card card-declinado">
                <h3>Declinados</h3>
                <div class="number" id="mDeclinado">0</div>
            </div>
        </div>

        <!-- SUB ABAS -->
        <div class="sub-tabs">
            <button class="sub-tab-btn active" id="tabPendentesBtn" onclick="alternarSubAba('pendentes')">⏳ Pendentes (<span id="countTabPendentes">0</span>)</button>
            <button class="sub-tab-btn" id="tabHistoricoBtn" onclick="alternarSubAba('historico')">📁 Histórico (Enviados e Declinados)</button>
        </div>

        <!-- TABELA PENDENTES -->
        <div id="viewPendentes" style="overflow-x:auto;">
            <table>
                <thead>
                    <tr>
                        <th>Nº Orçamento</th>
                        <th>Sigla</th>
                        <th>Data / Hora</th>
                        <th>Técnico</th>
                        <th>Motivo</th>
                        <th>Itens</th>
                        <th>Status</th>
                        <th>Ações</th>
                    </tr>
                </thead>
                <tbody id="listaPendentes">
                    <tr><td colspan="8" style="text-align:center;">Carregando dados...</td></tr>
                </tbody>
            </table>
        </div>

        <!-- TABELA HISTÓRICO -->
        <div id="viewHistorico" style="display: none; overflow-x:auto;">
            <table>
                <thead>
                    <tr>
                        <th>Nº Orçamento</th>
                        <th>Sigla</th>
                        <th>Data / Hora</th>
                        <th>Técnico</th>
                        <th>Motivo</th>
                        <th>Itens</th>
                        <th>Status</th>
                        <th>Ações</th>
                    </tr>
                </thead>
                <tbody id="listaHistorico">
                    <tr><td colspan="8" style="text-align:center;">Carregando dados...</td></tr>
                </tbody>
            </table>
        </div>
    </div>

    <!-- TELA 3: GALERIA E VISUALIZADOR DE PDF -->
    <div id="galeriaPdfSection" class="card-box" style="display: none;">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
            <h1 style="border:none; padding:0; margin:0;">📁 Galeria e Visualizador de PDFs</h1>
            <a href="https://drive.google.com/drive/folders/12p-H152le372P0Nx4C2_vX5hK34caOeS" target="_blank" class="btn" style="text-decoration:none;">🔗 Abrir Pasta no Google Drive</a>
        </div>

        <div class="pdf-controls">
            <button class="btn-small" style="padding:8px 14px; font-size:13px;" onclick="alternarModoPasta('grid')">📱 Modo Grade (Ícones)</button>
            <button class="btn-small" style="padding:8px 14px; font-size:13px;" onclick="alternarModoPasta('list')">📜 Modo Lista</button>
        </div>

        <!-- PASTA DO GOOGLE DRIVE INCORPORADA -->
        <div class="section-title">📂 Arquivos da Pasta do Google Drive</div>
        <iframe id="folderIframe" src="https://drive.google.com/embeddedfolderview?id=12p-H152le372P0Nx4C2_vX5hK34caOeS#grid" width="100%" height="480" style="border: 1px solid var(--border); border-radius: 8px;"></iframe>

        <!-- VISUALIZADOR INDIVIDUAL DE PDF -->
        <div class="pdf-viewer-box">
            <div class="section-title">🔍 Visualizador Direto de Documento PDF</div>
            <p style="font-size:13px; color:#64748b; margin-top:-5px;">Cole o link ou o ID do arquivo do Google Drive para visualizá-lo em tela cheia abaixo:</p>
            
            <div style="display:flex; gap:10px; margin-bottom:15px;">
                <input type="text" id="pdfUrlInput" placeholder="Ex: https://drive.google.com/file/d/12p-H152le372P0Nx4C2_vX5hK34caOeS/view ou o ID do arquivo">
                <button class="btn" style="white-space:nowrap;" onclick="carregarPdfPorInput()">🔍 Visualizar PDF</button>
            </div>

            <iframe id="pdfPreviewFrame" src="" width="100%" height="600" style="border: 1px solid var(--border); border-radius: 8px; display:none;"></iframe>
        </div>
    </div>
</div>

<script>
    const CHAVE_BANCO_NUMERO = 'app_orcamento_ultimo_numero';
    const URL_GOOGLE_SHEETS = "https://script.google.com/macros/s/AKfycbx_CTDs1fCmZ1PmS5ZKstISqJ5nk0Aay0xJTia8AaWowMIh4zqXFBSXRYJu71qPeOY_Jw/exec";
    const ID_PASTA_DRIVE = "12p-H152le372P0Nx4C2_vX5hK34caOeS";
    const SENHA_AUTORIZACAO = "GF01";

    let todosOrcamentos = [];

    window.onload = function() {
        const hoje = new Date();
        const dataInput = document.getElementById('dataEmissao');
        const horaInput = document.getElementById('horaEmissao');

        if (!dataInput.value) {
            const ano = hoje.getFullYear();
            const mes = String(hoje.getMonth() + 1).padStart(2, '0');
            const dia = String(hoje.getDate()).padStart(2, '0');
            dataInput.value = `${ano}-${mes}-${dia}`;
        }

        if (!horaInput.value) {
            const horas = String(hoje.getHours()).padStart(2, '0');
            const minutos = String(hoje.getMinutes()).padStart(2, '0');
            horaInput.value = `${horas}:${minutos}`;
        }

        carregarNumeroOrcamento();
        atualizarCabecalho();
        configurarEventosColarEArrastar();
    };

    /* NAVEGAÇÃO ENTRE AS TELAS PRINCIPAIS */
    function alternarTela(tela) {
        const secOrcamento = document.getElementById('orcamentoSection');
        const secPainel = document.getElementById('painelSection');
        const secGaleria = document.getElementById('galeriaPdfSection');

        const btnOrcamento = document.getElementById('btnNavOrcamento');
        const btnPainel = document.getElementById('btnNavPainel');
        const btnGaleria = document.getElementById('btnNavGaleria');

        // Esconde todas
        secOrcamento.style.display = 'none';
        secPainel.style.display = 'none';
        secGaleria.style.display = 'none';

        btnOrcamento.classList.remove('active');
        btnPainel.classList.remove('active');
        btnGaleria.classList.remove('active');

        if (tela === 'orcamento') {
            secOrcamento.style.display = 'block';
            btnOrcamento.classList.add('active');
        } else if (tela === 'painel') {
            secPainel.style.display = 'block';
            btnPainel.classList.add('active');
            carregarDadosPainel();
        } else if (tela === 'galeria') {
            secGaleria.style.display = 'block';
            btnGaleria.classList.add('active');
        }
    }

    /* EMISSÃO DE ORÇAMENTO */
    function carregarNumeroOrcamento() {
        let numeroSalvo = localStorage.getItem(CHAVE_BANCO_NUMERO);
        if (!numeroSalvo) {
            numeroSalvo = 6;
            localStorage.setItem(CHAVE_BANCO_NUMERO, numeroSalvo);
        }
        exibirNumeroFormatado(parseInt(numeroSalvo));
    }

    function formatarComZeros(num) {
        return String(num).padStart(4, '0');
    }

    function exibirNumeroFormatado(num) {
        document.getElementById('numOrcamento').value = formatarComZeros(num);
    }

    function avançarNumeroOrcamento() {
        let atual = parseInt(localStorage.getItem(CHAVE_BANCO_NUMERO)) || 6;
        let proximo = atual + 1;
        localStorage.setItem(CHAVE_BANCO_NUMERO, proximo);
        exibirNumeroFormatado(proximo);
    }

    function alterarNumeroManual() {
        let atual = parseInt(localStorage.getItem(CHAVE_BANCO_NUMERO)) || 6;
        let novo = prompt("Digite o novo número inicial do orçamento (ex: 6):", atual);
        if (novo !== null && !isNaN(parseInt(novo))) {
            let numParsed = parseInt(novo);
            localStorage.setItem(CHAVE_BANCO_NUMERO, numParsed);
            exibirNumeroFormatado(numParsed);
            atualizarCabecalho();
        }
    }

    function getNumeroSemana(d) {
        const date = new Date(Date.UTC(d.getFullYear(), d.getMonth(), d.getDate()));
        const dayNum = date.getUTCDay() || 7;
        date.setUTCDate(date.getUTCDate() + 4 - dayNum);
        const yearStart = new Date(Date.UTC(date.getUTCFullYear(), 0, 1));
        return Math.ceil((((date - yearStart) / 86400000) + 1) / 7);
    }

    function atualizarCabecalho() {
        const dataVal = document.getElementById('dataEmissao').value;
        const numVal = document.getElementById('numOrcamento').value || '0006';
        if (dataVal) {
            const partes = dataVal.split('-');
            const dataFormatada = `${partes[2]}_${partes[1]}_${partes[0]}`;
            const dataObj = new Date(partes[0], partes[1] - 1, partes[2]);
            const numSemana = getNumeroSemana(dataObj);
            
            document.getElementById('semanaManual').value = `Semana ${numSemana}`;
            document.getElementById('semanaHeader').innerText = `ORÇAMENTO Nº ${numVal} - ${dataFormatada} (SEMANA ${numSemana})`;
        }
    }

    function adicionarLinha() {
        const tbody = document.getElementById('corpoTabela');
        const tr = document.createElement('tr');
        tr.innerHTML = `
            <td><input type="text" placeholder="Descrição do item ou serviço" class="item-desc" oninput="removerErro(this)"></td>
            <td><input type="number" value="1" min="1" class="item-qtd" oninput="removerErro(this)"></td>
            <td class="no-print"><button class="btn btn-danger" onclick="removerLinha(this)">X</button></td>
        `;
        tbody.appendChild(tr);
    }

    function removerLinha(btn) {
        btn.closest('tr').remove();
    }

    function removerErro(elemento) {
        elemento.classList.remove('input-error');
    }

    function processarArquivoFoto(arquivo) {
        if (!arquivo || !arquivo.type.startsWith('image/')) return;

        const leitor = new FileReader();
        leitor.onload = function(e) {
            const galeria = document.getElementById('galeriaFotos');
            const card = document.createElement('div');
            card.className = 'photo-card';
            card.innerHTML = `
                <img src="${e.target.result}" alt="Evidência">
                <input type="text" placeholder="Legenda da foto" style="margin-top:5px; font-size:11px;">
                <button class="btn-remove-photo no-print" onclick="this.closest('.photo-card').remove()">Excluir</button>
            `;
            galeria.appendChild(card);
            document.getElementById('dropZone').classList.remove('input-error');
        };
        leitor.readAsDataURL(arquivo);
    }

    function carregarFotos(event) {
        const arquivos = event.target.files;
        for (let i = 0; i < arquivos.length; i++) {
            processarArquivoFoto(arquivos[i]);
        }
        event.target.value = '';
    }

    function configurarEventosColarEArrastar() {
        document.addEventListener('paste', function(e) {
            const clipboardData = e.clipboardData || window.clipboardData;
            if (!clipboardData) return;

            const items = clipboardData.items;

            for (let i = 0; i < items.length; i++) {
                if (items[i].type.indexOf('image') !== -1) {
                    const blob = items[i].getAsFile();
                    processarArquivoFoto(blob);
                }
            }
        });

        const dropZone = document.getElementById('dropZone');

        ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
            dropZone.addEventListener(eventName, function(e) {
                e.preventDefault();
                e.stopPropagation();
            }, false);
        });

        ['dragenter', 'dragover'].forEach(eventName => {
            dropZone.addEventListener(eventName, () => dropZone.classList.add('dragover'), false);
        });

        ['dragleave', 'drop'].forEach(eventName => {
            dropZone.addEventListener(eventName, () => dropZone.classList.remove('dragover'), false);
        });

        dropZone.addEventListener('drop', function(e) {
            const dt = e.dataTransfer;
            const files = dt.files;

            for (let i = 0; i < files.length; i++) {
                processarArquivoFoto(files[i]);
            }
        }, false);
    }

    function validarFormulario() {
        let erros = [];

        const sigla = document.getElementById('sigla');
        const dataEmissao = document.getElementById('dataEmissao');
        const horaEmissao = document.getElementById('horaEmissao');
        const tecnico = document.getElementById('tecnico');
        const motivo = document.getElementById('motivo');

        if (!sigla.value.trim()) {
            sigla.classList.add('input-error');
            erros.push("Sigla do Cliente / Loja");
        }

        if (!dataEmissao.value) {
            dataEmissao.classList.add('input-error');
            erros.push("Data da Emissão");
        }

        if (!horaEmissao.value) {
            horaEmissao.classList.add('input-error');
            erros.push("Hora da Emissão");
        }

        if (!tecnico.value.trim()) {
            tecnico.classList.add('input-error');
            erros.push("Técnico Responsável");
        }

        if (!motivo.value.trim()) {
            motivo.classList.add('input-error');
            erros.push("Motivo do Orçamento");
        }

        const linhasItens = document.querySelectorAll('#corpoTabela tr');
        let itemPreenchido = false;

        if (linhasItens.length === 0) {
            erros.push("Adicione ao menos 1 item na tabela de Peças & Serviços");
        } else {
            linhasItens.forEach((linha) => {
                const desc = linha.querySelector('.item-desc');
                const qtd = linha.querySelector('.item-qtd');

                if (!desc.value.trim() || !qtd.value || parseFloat(qtd.value) <= 0) {
                    desc.classList.add('input-error');
                    qtd.classList.add('input-error');
                } else {
                    itemPreenchido = true;
                }
            });

            if (!itemPreenchido) {
                erros.push("Descrição e Quantidade dos Itens/Serviços na tabela");
            }
        }

        const fotos = document.querySelectorAll('#galeriaFotos .photo-card');
        const dropZone = document.getElementById('dropZone');
        if (fotos.length === 0) {
            dropZone.classList.add('input-error');
            erros.push("Anexo de pelo menos 1 Foto / Evidência");
        }

        if (erros.length > 0) {
            alert("⚠️ PREENCHIMENTO OBRIGATÓRIO!\n\nPor favor, preencha os seguintes campos antes de salvar o PDF:\n\n• " + erros.join("\n• "));
            return false;
        }

        return true;
    }

    function enviarParaGoogleSheets(dados) {
        fetch(URL_GOOGLE_SHEETS, {
            method: 'POST',
            mode: 'no-cors',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(dados)
        })
        .then(() => console.log("Dados salvos no Google Sheets!"))
        .catch(err => console.error("Erro ao enviar dados para a planilha:", err));
    }

    function imprimirOuSalvarPDF() {
        if (!validarFormulario()) {
            return;
        }

        const linhasItens = document.querySelectorAll('#corpoTabela tr');
        let listaItens = [];
        linhasItens.forEach(linha => {
            const desc = linha.querySelector('.item-desc').value.trim();
            const qtd = linha.querySelector('.item-qtd').value;
            if (desc) listaItens.push(`${desc} (${qtd}x)`);
        });

        const dados = {
            numero: document.getElementById('numOrcamento').value,
            sigla: document.getElementById('sigla').value,
            data: document.getElementById('dataEmissao').value,
            hora: document.getElementById('horaEmissao').value,
            tecnico: document.getElementById('tecnico').value,
            semana: document.getElementById('semanaManual').value,
            motivo: document.getElementById('motivo').value,
            itens: listaItens.join('; ')
        };

        enviarParaGoogleSheets(dados);
        window.print();

        setTimeout(() => {
            avançarNumeroOrcamento();
            atualizarCabecalho();
        }, 1000);
    }

    /* PAINEL DE GESTÃO DE ORÇAMENTOS */
    function carregarDadosPainel() {
        fetch(URL_GOOGLE_SHEETS)
            .then(res => res.json())
            .then(data => {
                todosOrcamentos = data;
                renderizarDashboard();
            })
            .catch(err => {
                console.error("Erro ao carregar dados:", err);
                alert("Erro ao carregar os dados da planilha.");
            });
    }

    function renderizarDashboard() {
        let cntPendente = 0, cntEnviado = 0, cntDeclinado = 0;

        const tbodyPendentes = document.getElementById('listaPendentes');
        const tbodyHistorico = document.getElementById('listaHistorico');

        tbodyPendentes.innerHTML = '';
        tbodyHistorico.innerHTML = '';

        todosOrcamentos.forEach(item => {
            const status = (item.status || 'PENDENTE').toUpperCase();

            if (status === 'PENDENTE') cntPendente++;
            else if (status === 'ENVIADO') cntEnviado++;
            else if (status === 'DECLINADO') cntDeclinado++;

            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td><b>${item.numero}</b></td>
                <td>${item.sigla}</td>
                <td>${item.data} ${item.hora}</td>
                <td>${item.tecnico}</td>
                <td>${item.motivo}</td>
                <td><small>${item.itens}</small></td>
                <td><span class="badge badge-${status.toLowerCase()}">${status}</span></td>
                <td>${gerarBotoesAcao(item.numero, status)}</td>
            `;

            if (status === 'PENDENTE') {
                tbodyPendentes.appendChild(tr);
            } else {
                tbodyHistorico.appendChild(tr);
            }
        });

        if (tbodyPendentes.children.length === 0) {
            tbodyPendentes.innerHTML = '<tr><td colspan="8" style="text-align:center;">Nenhum orçamento pendente.</td></tr>';
        }
        if (tbodyHistorico.children.length === 0) {
            tbodyHistorico.innerHTML = '<tr><td colspan="8" style="text-align:center;">Nenhum histórico registrado.</td></tr>';
        }

        document.getElementById('mTotal').innerText = todosOrcamentos.length;
        document.getElementById('mPendente').innerText = cntPendente;
        document.getElementById('mEnviado').innerText = cntEnviado;
        document.getElementById('mDeclinado').innerText = cntDeclinado;
        document.getElementById('countTabPendentes').innerText = cntPendente;
    }

    function gerarBotoesAcao(numero, statusAtual) {
        if (statusAtual === 'PENDENTE') {
            return `
                <button class="btn-action btn-enviar" onclick="alterarStatus('${numero}', 'ENVIADO')">Enviar</button>
                <button class="btn-action btn-declinar" onclick="alterarStatus('${numero}', 'DECLINADO')">Declinar</button>
            `;
        } else {
            return `
                <button class="btn-action btn-pendente" onclick="alterarStatus('${numero}', 'PENDENTE')">Voltar p/ Pendente</button>
            `;
        }
    }

    function alternarSubAba(aba) {
        const btnPendentes = document.getElementById('tabPendentesBtn');
        const btnHistorico = document.getElementById('tabHistoricoBtn');
        const viewPendentes = document.getElementById('viewPendentes');
        const viewHistorico = document.getElementById('viewHistorico');

        if (aba === 'pendentes') {
            btnPendentes.classList.add('active');
            btnHistorico.classList.remove('active');
            viewPendentes.style.display = 'block';
            viewHistorico.style.display = 'none';
        } else {
            btnHistorico.classList.add('active');
            btnPendentes.classList.remove('active');
            viewHistorico.style.display = 'block';
            viewHistorico.style.display = 'none';
        }
    }

    function alterarStatus(numero, novoStatus) {
        const senhaDigitada = prompt(`Para alterar o status do Orçamento Nº ${numero} para ${novoStatus}, digite a senha de confirmação:`);

        if (senhaDigitada === null) return;

        if (senhaDigitada !== SENHA_AUTORIZACAO) {
            alert("❌ Senha incorreta! A alteração não foi realizada.");
            return;
        }

        const payload = {
            action: "updateStatus",
            numero: numero,
            status: novoStatus
        };

        fetch(URL_GOOGLE_SHEETS, {
            method: 'POST',
            body: JSON.stringify(payload)
        })
        .then(() => {
            alert(`✅ Status do Orçamento Nº ${numero} atualizado para ${novoStatus}!`);
            carregarDadosPainel();
        })
        .catch(err => {
            console.error("Erro ao alterar status:", err);
            alert("Erro ao atualizar o status na planilha.");
        });
    }

    /* GALERIA & VISUALIZADOR DE PDF */
    function alternarModoPasta(modo) {
        const iframe = document.getElementById('folderIframe');
        iframe.src = `https://drive.google.com/embeddedfolderview?id=${ID_PASTA_DRIVE}#${modo}`;
    }

    function carregarPdfPorInput() {
        const val = document.getElementById('pdfUrlInput').value.trim();
        const frame = document.getElementById('pdfPreviewFrame');

        if (!val) {
            alert("Por favor, cole o link ou ID de um arquivo PDF do Google Drive.");
            return;
        }

        let fileId = val;

        // Extrai o ID caso tenha colado um link completo do Drive
        if (val.includes('/d/')) {
            const partes = val.split('/d/');
            if (partes[1]) {
                fileId = partes[1].split('/')[0].split('?')[0];
            }
        }

        frame.src = `https://drive.google.com/file/d/${fileId}/preview`;
        frame.style.display = 'block';
    }
</script>

</body>
</html>
