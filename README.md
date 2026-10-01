<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GFLINKS - Sistema de Orçamentos, Gestão & PDFs</title>

    <!-- BIBLIOTECA PARA LEITURA DE TEXTO EM PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>

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

        .grid-2 {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
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
        input[type="number"],
        select,
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

        input:focus, textarea:focus, select:focus {
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

        /* ESTILIZAÇÃO DA TABELA */
        .table-responsive {
            width: 100%;
            overflow-x: auto;
            border-radius: 8px;
            border: 1px solid var(--border);
            margin-top: 15px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            min-width: 850px;
            background: white;
        }

        th, td {
            border: 1px solid var(--border);
            padding: 12px 10px;
            text-align: left;
            font-size: 13px;
            vertical-align: middle;
            line-height: 1.4;
        }

        th {
            background-color: #f1f5f9;
            color: #334155;
            font-weight: 700;
            text-transform: uppercase;
            font-size: 12px;
            letter-spacing: 0.5px;
        }

        .col-num { width: 7%; text-align: center; font-weight: bold; }
        .col-sigla { width: 8%; text-align: center; font-weight: 600; }
        .col-data { width: 12%; white-space: nowrap; text-align: center; }
        .col-tec { width: 12%; font-weight: 600; }
        .col-motivo { width: 31%; word-break: break-word; }
        .col-itens { width: 18%; word-break: break-word; font-size: 12px; color: #334155; }
        .col-status { width: 6%; text-align: center; white-space: nowrap; }
        .col-acoes { width: 6%; text-align: center; white-space: nowrap; }

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
            border: 2px solid var(--border);
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            text-align: center;
            cursor: pointer;
            transition: transform 0.15s, border-color 0.15s;
        }

        .metric-card:hover {
            transform: translateY(-3px);
            border-color: var(--primary);
        }

        .metric-card.active-card {
            border-color: var(--primary);
            background-color: #eff6ff;
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
            flex-wrap: wrap;
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
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 11px;
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
            margin: 2px 0;
        }

        .btn-action:hover { opacity: 0.85; }
        .btn-enviar { background-color: var(--green); }
        .btn-declinar { background-color: var(--danger); }
        .btn-pendente { background-color: var(--yellow); }

        .status-alert {
            background-color: #d1fae5;
            color: #065f46;
            padding: 10px 14px;
            border-radius: 6px;
            font-size: 13px;
            font-weight: bold;
            margin-top: 10px;
            display: none;
        }

        @media print {
            body { background: white; padding: 0; }
            .container { max-width: 100%; }
            .top-nav, .no-print, #painelSection, #galeriaPdfSection, #importarPdfSection { display: none !important; }
            #orcamentoSection { display: block !important; }
            .card-box { box-shadow: none; border: none; padding: 0; }
            input, textarea, select { border: none !important; background: transparent !important; padding: 0 !important; }
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
        <button class="nav-btn" id="btnNavImportar" onclick="alternarTela('importar')">📤 Importar PDF Antigo</button>
        <button class="nav-btn" id="btnNavGaleria" onclick="alternarTela('galeria')">📂 Visualizador Pasta Drive</button>
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

        <div class="grid-2">
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

        <div class="metrics-grid">
            <div class="metric-card card-total" id="cardTotal" onclick="filtrarStatusPeloCard('TODOS')">
                <h3>Total Geral</h3>
                <div class="number" id="mTotal">0</div>
            </div>
            <div class="metric-card card-pendente active-card" id="cardPendente" onclick="filtrarStatusPeloCard('PENDENTE')">
                <h3>Pendentes</h3>
                <div class="number" id="mPendente">0</div>
            </div>
            <div class="metric-card card-enviado" id="cardEnviado" onclick="filtrarStatusPeloCard('ENVIADO')">
                <h3>Enviados</h3>
                <div class="number" id="mEnviado">0</div>
            </div>
            <div class="metric-card card-declinado" id="cardDeclinado" onclick="filtrarStatusPeloCard('DECLINADO')">
                <h3>Declinados</h3>
                <div class="number" id="mDeclinado">0</div>
            </div>
        </div>

        <div class="sub-tabs">
            <button class="sub-tab-btn active" id="tabPendentesBtn" onclick="filtrarStatusPeloCard('PENDENTE')">⏳ Pendentes (<span id="countTabPendentes">0</span>)</button>
            <button class="sub-tab-btn" id="tabEnviadosBtn" onclick="filtrarStatusPeloCard('ENVIADO')">✉️ Enviados (<span id="countTabEnviados">0</span>)</button>
            <button class="sub-tab-btn" id="tabDeclinadosBtn" onclick="filtrarStatusPeloCard('DECLINADO')">❌ Declinados (<span id="countTabDeclinados">0</span>)</button>
            <button class="sub-tab-btn" id="tabTodosBtn" onclick="filtrarStatusPeloCard('TODOS')">📋 Todos</button>
        </div>

        <div class="table-responsive">
            <table>
                <thead>
                    <tr>
                        <th class="col-num">Nº</th>
                        <th class="col-sigla">Sigla</th>
                        <th class="col-data">Data</th>
                        <th class="col-tec">Técnico</th>
                        <th class="col-motivo">Motivo do Atendimento</th>
                        <th class="col-itens">Itens / Detalhes</th>
                        <th class="col-status">Status</th>
                        <th class="col-acoes">Ações</th>
                    </tr>
                </thead>
                <tbody id="tabelaPainelBody">
                    <tr><td colspan="8" style="text-align:center;">Carregando dados...</td></tr>
                </tbody>
            </table>
        </div>
    </div>

    <!-- TELA 3: IMPORTAR PDF ANTIGO -->
    <div id="importarPdfSection" class="card-box" style="display: none;">
        <h1><span>📤 Importar Orçamento Antigo (PDF)</span></h1>
        <p style="font-size:13px; color:#64748b;">Ao selecionar um arquivo PDF, os dados serão lidos e preenchidos automaticamente no formulário abaixo.</p>

        <div id="alertLeituraPdf" class="status-alert">
            ✨ Dados lidos e preenchidos automaticamente do arquivo PDF!
        </div>

        <div class="grid-2" style="margin-top:20px;">
            <div>
                <div class="section-title">📝 Dados do Orçamento</div>
                <div class="grid-2">
                    <div class="form-group">
                        <label for="impNum">Nº Orçamento <span class="req">*</span></label>
                        <input type="text" id="impNum" placeholder="Ex: 0005">
                    </div>
                    <div class="form-group">
                        <label for="impSigla">Sigla Cliente / Loja <span class="req">*</span></label>
                        <input type="text" id="impSigla" placeholder="Ex: CNL">
                    </div>
                </div>

                <div class="grid-2">
                    <div class="form-group">
                        <label for="impData">Data <span class="req">*</span></label>
                        <input type="date" id="impData">
                    </div>
                    <div class="form-group">
                        <label for="impTecnico">Técnico Responsável <span class="req">*</span></label>
                        <input type="text" id="impTecnico" placeholder="Nome do Técnico">
                    </div>
                </div>

                <div class="form-group">
                    <label for="impStatus">Status Inicial no Painel <span class="req">*</span></label>
                    <select id="impStatus" style="font-weight:bold; color:var(--primary);">
                        <option value="PENDENTE">⏳ PENDENTE</option>
                        <option value="ENVIADO">✉️ ENVIADO</option>
                        <option value="DECLINADO">❌ DECLINADO</option>
                    </select>
                </div>

                <div class="form-group">
                    <label for="impMotivo">Motivo do Orçamento <span class="req">*</span></label>
                    <textarea id="impMotivo" placeholder="Descreva o motivo..."></textarea>
                </div>

                <div class="form-group">
                    <label for="impItens">Peças & Serviços Registrados</label>
                    <textarea id="impItens" placeholder="Ex: Câmera IP (2x); Nobreak (1x)"></textarea>
                </div>

                <button class="btn btn-add" style="width:100%; padding:12px; font-size:15px; margin-top:10px;" onclick="salvarOrcamentoAntigo()">💾 Registrar no Painel de Gestão</button>
            </div>

            <div>
                <div class="section-title">📄 Selecionar PDF do Computador</div>
                <div class="drop-zone" onclick="document.getElementById('impFilePdf').click()">
                    <div class="drop-zone-text">📁 Clique para selecionar o PDF antigo</div>
                    <div class="drop-zone-subtext">Os dados serão extraídos automaticamente após a seleção</div>
                </div>
                <input type="file" id="impFilePdf" accept="application/pdf" style="display:none;" onchange="previewPdfImportacao(event)">

                <div style="margin-top:15px;">
                    <iframe id="impPdfViewer" src="" width="100%" height="420" style="border:1px solid var(--border); border-radius:8px; display:none;"></iframe>
                </div>
            </div>
        </div>
    </div>

    <!-- TELA 4: VISUALIZADOR DA PASTA DO DRIVE -->
    <div id="galeriaPdfSection" class="card-box" style="display: none;">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
            <h1 style="border:none; padding:0; margin:0;">📂 Visualizador da Pasta do Google Drive</h1>
            <a href="https://drive.google.com/drive/folders/12p-H152le372P0Nx4C2_vX5hK34caOeS" target="_blank" class="btn" style="text-decoration:none; font-size:13px;">🔗 Abrir Pasta no Drive</a>
        </div>
        <p style="font-size:14px; color:#64748b; margin-top:-10px;">Visualize e navegue nativamente pelos PDFs e subpastas salvos no seu Google Drive:</p>

        <iframe id="driveFolderIframe" src="https://drive.google.com/embeddedfolderview?id=12p-H152le372P0Nx4C2_vX5hK34caOeS#list" width="100%" height="650" style="border: 1px solid var(--border); border-radius: 8px;"></iframe>
    </div>
</div>

<script>
    if (typeof pdfjsLib !== 'undefined') {
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
    }

    const CHAVE_BANCO_NUMERO = 'app_orcamento_ultimo_numero';
    const URL_GOOGLE_SHEETS = "https://script.google.com/macros/s/AKfycbyOe6e7jDIEYVLGXaNoqBXR2IkFeYS-EIfl_NMK65yZGVH65xnjmHCpuUVvKxhRWcYSKQ/exec";
    const SENHA_AUTORIZACAO = "GF01";

    let todosOrcamentos = [];
    let statusFiltroAtual = 'PENDENTE';

    window.onload = function() {
        const hoje = new Date();
        const dataInput = document.getElementById('dataEmissao');

        if (!dataInput.value) {
            const ano = hoje.getFullYear();
            const mes = String(hoje.getMonth() + 1).padStart(2, '0');
            const dia = String(hoje.getDate()).padStart(2, '0');
            dataInput.value = `${ano}-${mes}-${dia}`;
        }

        carregarNumeroOrcamento();
        atualizarCabecalho();
        configurarEventosColarEArrastar();
    };

    function alternarTela(tela) {
        document.getElementById('orcamentoSection').style.display = 'none';
        document.getElementById('painelSection').style.display = 'none';
        document.getElementById('importarPdfSection').style.display = 'none';
        document.getElementById('galeriaPdfSection').style.display = 'none';

        document.getElementById('btnNavOrcamento').classList.remove('active');
        document.getElementById('btnNavPainel').classList.remove('active');
        document.getElementById('btnNavImportar').classList.remove('active');
        document.getElementById('btnNavGaleria').classList.remove('active');

        if (tela === 'orcamento') {
            document.getElementById('orcamentoSection').style.display = 'block';
            document.getElementById('btnNavOrcamento').classList.add('active');
        } else if (tela === 'painel') {
            document.getElementById('painelSection').style.display = 'block';
            document.getElementById('btnNavPainel').classList.add('active');
            carregarDadosPainel();
        } else if (tela === 'importar') {
            document.getElementById('importarPdfSection').style.display = 'block';
            document.getElementById('btnNavImportar').classList.add('active');
        } else if (tela === 'galeria') {
            document.getElementById('galeriaPdfSection').style.display = 'block';
            document.getElementById('btnNavGaleria').classList.add('active');
        }
    }

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
                    processarArquivoFoto(items[i].getAsFile());
                }
            }
        });

        const dropZone = document.getElementById('dropZone');
        ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
            dropZone.addEventListener(eventName, e => { e.preventDefault(); e.stopPropagation(); }, false);
        });
        dropZone.addEventListener('drop', e => {
            const files = e.dataTransfer.files;
            for (let i = 0; i < files.length; i++) processarArquivoFoto(files[i]);
        }, false);
    }

    function validarFormulario() {
        let erros = [];
        const sigla = document.getElementById('sigla');
        const dataEmissao = document.getElementById('dataEmissao');
        const tecnico = document.getElementById('tecnico');
        const motivo = document.getElementById('motivo');

        if (!sigla.value.trim()) { sigla.classList.add('input-error'); erros.push("Sigla do Cliente / Loja"); }
        if (!dataEmissao.value) { dataEmissao.classList.add('input-error'); erros.push("Data da Emissão"); }
        if (!tecnico.value.trim()) { tecnico.classList.add('input-error'); erros.push("Técnico Responsável"); }
        if (!motivo.value.trim()) { motivo.classList.add('input-error'); erros.push("Motivo do Orçamento"); }

        const linhasItens = document.querySelectorAll('#corpoTabela tr');
        let itemPreenchido = false;
        if (linhasItens.length === 0) {
            erros.push("Adicione ao menos 1 item na tabela");
        } else {
            linhasItens.forEach(linha => {
                const desc = linha.querySelector('.item-desc');
                const qtd = linha.querySelector('.item-qtd');
                if (!desc.value.trim() || !qtd.value || parseFloat(qtd.value) <= 0) {
                    desc.classList.add('input-error');
                    qtd.classList.add('input-error');
                } else {
                    itemPreenchido = true;
                }
            });
            if (!itemPreenchido) erros.push("Descrição e Quantidade na tabela");
        }

        if (document.querySelectorAll('#galeriaFotos .photo-card').length === 0) {
            document.getElementById('dropZone').classList.add('input-error');
            erros.push("Anexo de pelo menos 1 Foto / Evidência");
        }

        if (erros.length > 0) {
            alert("⚠️ PREENCHIMENTO OBRIGATÓRIO!\n\n• " + erros.join("\n• "));
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
        });
    }

    function imprimirOuSalvarPDF() {
        if (!validarFormulario()) return;

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
            tecnico: document.getElementById('tecnico').value,
            semana: document.getElementById('semanaManual').value,
            motivo: document.getElementById('motivo').value,
            itens: listaItens.join('; '),
            status: "PENDENTE"
        };

        enviarParaGoogleSheets(dados);
        window.print();

        setTimeout(() => {
            avançarNumeroOrcamento();
            atualizarCabecalho();
        }, 1000);
    }

    /* PAINEL DE GESTÃO */
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

    function filtrarStatusPeloCard(status) {
        statusFiltroAtual = status;
        document.getElementById('cardTotal').classList.toggle('active-card', status === 'TODOS');
        document.getElementById('cardPendente').classList.toggle('active-card', status === 'PENDENTE');
        document.getElementById('cardEnviado').classList.toggle('active-card', status === 'ENVIADO');
        document.getElementById('cardDeclinado').classList.toggle('active-card', status === 'DECLINADO');

        document.getElementById('tabPendentesBtn').classList.toggle('active', status === 'PENDENTE');
        document.getElementById('tabEnviadosBtn').classList.toggle('active', status === 'ENVIADO');
        document.getElementById('tabDeclinadosBtn').classList.toggle('active', status === 'DECLINADO');
        document.getElementById('tabTodosBtn').classList.toggle('active', status === 'TODOS');

        renderizarTabelaFiltrada();
    }

    function renderizarDashboard() {
        let cntPendente = 0, cntEnviado = 0, cntDeclinado = 0;
        todosOrcamentos.forEach(item => {
            const st = (item.status || 'PENDENTE').toUpperCase();
            if (st === 'PENDENTE') cntPendente++;
            else if (st === 'ENVIADO') cntEnviado++;
            else if (st === 'DECLINADO') cntDeclinado++;
        });

        document.getElementById('mTotal').innerText = todosOrcamentos.length;
        document.getElementById('mPendente').innerText = cntPendente;
        document.getElementById('mEnviado').innerText = cntEnviado;
        document.getElementById('mDeclinado').innerText = cntDeclinado;

        document.getElementById('countTabPendentes').innerText = cntPendente;
        document.getElementById('countTabEnviados').innerText = cntEnviado;
        document.getElementById('countTabDeclinados').innerText = cntDeclinado;

        renderizarTabelaFiltrada();
    }

    /* FORMATAR APENAS A DATA */
    function formatarDataLimpo(dataRaw) {
        if (!dataRaw) return '-';
        let textoCompleto = String(dataRaw).trim();
        
        let matchData = textoCompleto.match(/(\d{2})[\/_-](\d{2})[\/_-](\d{4})/) || textoCompleto.match(/(\d{4})[\/_-](\d{2})[\/_-](\d{2})/);
        
        if (matchData) {
            if (matchData[0].includes('-') && matchData[1].length === 4) { // YYYY-MM-DD
                return `📅 ${matchData[3]}/${matchData[2]}/${matchData[1]}`;
            } else {
                return `📅 ${matchData[1]}/${matchData[2]}/${matchData[3]}`;
            }
        }
        return '-';
    }

    /* FILTRO RIGOROSO PARA REMOVER EVIDÊNCIAS FOTOGRÁFICAS E PATHS LOCAIS */
    function limparTextoItens(itensRaw) {
        if (!itensRaw) return '-';
        let lista = String(itensRaw).split(';');
        let itensFiltrados = lista.filter(item => {
            let txt = item.toUpperCase().trim();
            if (!txt) return false;
            if (txt.includes('EVIDÊNCIAS') || txt.includes('EVIDENCIAS')) return false;
            if (txt.includes('FOTOGRÁFICAS') || txt.includes('FOTOGRAFICAS')) return false;
            if (txt.includes('LEGENDA')) return false;
            if (txt.includes('/USERS/') || txt.includes('C:') || txt.includes('ONEDRIVE') || txt.includes('DESKTOP') || txt.includes('VALIDAÇÃO')) return false;
            if (txt.includes('.HTML') || txt.includes('.PNG') || txt.includes('.JPG') || txt.includes('.PDF')) return false;
            return true;
        });
        return itensFiltrados.join('; ').trim() || '-';
    }

    function renderizarTabelaFiltrada() {
        const tbody = document.getElementById('tabelaPainelBody');
        tbody.innerHTML = '';

        const itensFiltrados = todosOrcamentos.filter(item => {
            const st = (item.status || 'PENDENTE').toUpperCase();
            if (statusFiltroAtual === 'TODOS') return true;
            return st === statusFiltroAtual;
        });

        if (itensFiltrados.length === 0) {
            tbody.innerHTML = `<tr><td colspan="8" style="text-align:center; padding:25px; color:#64748b;">Nenhum orçamento encontrado com o status: <b>${statusFiltroAtual}</b></td></tr>`;
            return;
        }

        itensFiltrados.forEach(item => {
            const st = (item.status || 'PENDENTE').toUpperCase();
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td class="col-num">${item.numero}</td>
                <td class="col-sigla">${item.sigla}</td>
                <td class="col-data">${formatarDataLimpo(item.data)}</td>
                <td class="col-tec">${item.tecnico || '-'}</td>
                <td class="col-motivo">${item.motivo || '-'}</td>
                <td class="col-itens">${limparTextoItens(item.itens)}</td>
                <td class="col-status"><span class="badge badge-${st.toLowerCase()}">${st}</span></td>
                <td class="col-acoes">${gerarBotoesAcao(item.numero, st)}</td>
            `;
            tbody.appendChild(tr);
        });
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

    function alterarStatus(numero, novoStatus) {
        const senhaDigitada = prompt(`Para alterar o status do Orçamento Nº ${numero} para ${novoStatus}, digite a senha de confirmação:`);
        if (senhaDigitada === null) return;
        if (senhaDigitada !== SENHA_AUTORIZACAO) {
            alert("❌ Senha incorreta!");
            return;
        }

        const payload = { action: "updateStatus", numero: numero, status: novoStatus };
        fetch(URL_GOOGLE_SHEETS, { method: 'POST', body: JSON.stringify(payload) })
        .then(() => {
            alert(`✅ Status atualizado para ${novoStatus}!`);
            carregarDadosPainel();
        })
        .catch(err => alert("Erro ao atualizar status."));
    }

    /* IMPORTAR PDF ANTIGO E LEITURA AUTOMÁTICA */
    async function previewPdfImportacao(event) {
        const file = event.target.files[0];
        const viewer = document.getElementById('impPdfViewer');
        const alertBox = document.getElementById('alertLeituraPdf');

        if (!file || file.type !== 'application/pdf') return;

        viewer.src = URL.createObjectURL(file);
        viewer.style.display = 'block';

        if (typeof pdfjsLib === 'undefined') return;

        try {
            const arrayBuffer = await file.arrayBuffer();
            const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;
            let fullText = '';

            for (let pageNum = 1; pageNum <= pdf.numPages; pageNum++) {
                const page = await pdf.getPage(pageNum);
                const textContent = await page.getTextContent();
                const pageText = textContent.items.map(item => item.str).join(' ');
                fullText += pageText + '\n';
            }

            extrairPreencherDadosDoTexto(fullText, file.name);

            alertBox.style.display = 'block';
            setTimeout(() => { alertBox.style.display = 'none'; }, 4000);
        } catch (err) {
            console.error("Erro ao ler o arquivo PDF:", err);
        }
    }

    function extrairPreencherDadosDoTexto(texto, nomeArquivo) {
        let numMatch = texto.match(/ORÇAMENTO\s*N[º°]?\s*(\d+)/i) || texto.match(/ORÇAMENTOS?\s*(\d+)/i) || nomeArquivo.match(/(\d{4})/);
        if (numMatch && numMatch[1]) {
            document.getElementById('impNum').value = String(numMatch[1]).padStart(4, '0');
        }

        let siglaMatch = texto.match(/Sigla\s*(?:do\s*Cliente\s*\/?\s*Loja)?:?\s*([A-Za-z0-9_-]+)/i);
        if (siglaMatch && siglaMatch[1]) {
            document.getElementById('impSigla').value = siglaMatch[1].trim().toUpperCase();
        }

        let dataMatch = texto.match(/Data:?\s*(\d{2}\/\d{2}\/\d{4})/i) || texto.match(/(\d{2}\/\d{2}\/\d{4})/);
        if (dataMatch && dataMatch[1]) {
            let partes = dataMatch[1].split('/');
            if (partes.length === 3) {
                document.getElementById('impData').value = `${partes[2]}-${partes[1]}-${partes[0]}`;
            }
        }

        let tecMatch = texto.match(/Técnico\s*(?:Responsável)?:?\s*([A-Za-zÀ-ÿ\s]+?)(?=\s*Semana|\s*Data|\s*Hora|\s*Endereço|\n|$)/i);
        if (tecMatch && tecMatch[1]) {
            document.getElementById('impTecnico').value = tecMatch[1].trim();
        }

        let motMatch = texto.match(/(?:Endereço\s*Completo\s*do\s*Local|Motivo\s*(?:do\s*Orçamento)?):?\s*([\s\S]+?)(?=\s*PEÇAS|\s*Evidências|\n\n|$)/i);
        if (motMatch && motMatch[1]) {
            let motivoLimpo = motMatch[1].replace(/\s+/g, ' ').trim();
            document.getElementById('impMotivo').value = motivoLimpo;
        }

        let textoSemFotos = texto;
        if (texto.toUpperCase().includes('EVIDÊNCIAS FOTOGRÁFICAS')) {
            textoSemFotos = texto.split(/EVIDÊNCIAS FOTOGRÁFICAS/i)[0];
        }

        let pecasIdx = textoSemFotos.indexOf('PEÇAS & SERVIÇOS');
        if (pecasIdx !== -1) {
            let subTexto = textoSemFotos.substring(pecasIdx);
            let regexItens = /([A-Za-z0-9À-ÿ\s\-\.\/]+?)\s+(\d+)\b/g;
            let match;
            let itensArray = [];

            while ((match = regexItens.exec(subTexto)) !== null) {
                let nomeItem = match[1].replace(/Descrição do Item \/ Serviço/gi, '').replace(/Qtd/gi, '').trim();
                let qtd = match[2];
                if (nomeItem && !nomeItem.toUpperCase().includes('DESCRIÇÃO') && !nomeItem.toUpperCase().includes('SERVIÇO')) {
                    itensArray.push(`${nomeItem} (${qtd}x)`);
                }
            }

            if (itensArray.length > 0) {
                document.getElementById('impItens').value = itensArray.join('; ');
            }
        }
    }

    function salvarOrcamentoAntigo() {
        const num = document.getElementById('impNum').value.trim();
        const sigla = document.getElementById('impSigla').value.trim();
        const data = document.getElementById('impData').value;
        const tecnico = document.getElementById('impTecnico').value.trim();
        const status = document.getElementById('impStatus').value;
        const motivo = document.getElementById('impMotivo').value.trim();
        const itens = document.getElementById('impItens').value.trim();

        if (!num || !sigla || !data || !tecnico || !motivo) {
            alert("⚠️ Preencha os campos obrigatórios.");
            return;
        }

        const dados = {
            numero: String(num).padStart(4, '0'),
            sigla: sigla,
            data: data,
            tecnico: tecnico,
            semana: "Importado",
            motivo: motivo,
            itens: itens || "Orçamento Importado",
            status: status
        };

        enviarParaGoogleSheets(dados);
        alert(`✅ Orçamento Antigo Nº ${dados.numero} enviado com sucesso!`);

        document.getElementById('impNum').value = '';
        document.getElementById('impSigla').value = '';
        document.getElementById('impData').value = '';
        document.getElementById('impTecnico').value = '';
        document.getElementById('impMotivo').value = '';
        document.getElementById('impItens').value = '';
        document.getElementById('impPdfViewer').style.display = 'none';

        alternarTela('painel');
    }
</script>

</body>
</html>
