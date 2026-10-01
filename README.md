<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Orçamentos, Gestão & PDFs</title>

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- BIBLIOTECA PARA LEITURA DE TEXTO EM PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>

    <style>
        @media print {
            .no-print { display: none !important; }
            body { background: white !important; font-size: 9pt !important; }
            .card-box { box-shadow: none !important; border: none !important; padding: 0 !important; }
            input, textarea, select { border: none !important; background: transparent !important; padding: 0 !important; }
            .photo-card img { height: auto !important; max-height: 200px !important; }
            .btn-remove-photo, .req { display: none !important; }
        }
    </style>
</head>
<body class="bg-slate-100 font-sans min-h-screen text-slate-800 p-4 sm:p-6">

<div class="max-w-[1200px] mx-auto space-y-6">

    <!-- HEADER / MENU DE NAVEGAÇÃO SUPERIOR -->
    <header class="bg-slate-900 text-white shadow-lg rounded-2xl p-4 no-print">
        <div class="flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <img src="logo.png" alt="Logo" class="h-10 w-auto object-contain" onerror="this.style.display='none'">
                <i class="fa-solid fa-file-invoice-dollar text-emerald-400 text-2xl"></i>
                <div>
                    <h1 class="text-lg font-bold tracking-wide">Sistema de Orçamentos</h1>
                    <p class="text-xs text-slate-400">ORÇAMENTOS</p>
                </div>
            </div>
            
            <div class="flex items-center gap-2 flex-wrap justify-center">
                <button class="nav-btn bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 active" id="btnNavOrcamento" onclick="alternarTela('orcamento')">
                    <i class="fa-solid fa-pen-to-square"></i> Criar Orçamento
                </button>
                <button class="nav-btn bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 border border-slate-700" id="btnNavPainel" onclick="alternarTela('painel')">
                    <i class="fa-solid fa-chart-pie"></i> Painel de Gestão
                </button>
                <button class="nav-btn bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 border border-slate-700" id="btnNavGaleria" onclick="alternarTela('galeria')">
                    <i class="fa-solid fa-folder-open"></i> Arquivos
                </button>
            </div>
        </div>
    </header>

    <!-- TELA 1: EMISSÃO DE ORÇAMENTO -->
    <div id="orcamentoSection" class="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 sm:p-8 space-y-6">
        <div class="text-center">
            <img src="logo.png" alt="Logo da Empresa" class="h-12 mx-auto object-contain mb-2" onerror="this.style.display='none'">
            <h2 class="text-xl font-bold text-slate-800">Emissão de Orçamento</h2>
            <p class="text-xs text-slate-500">Preencha os campos abaixo para gerar o atendimento técnico e salvar na planilha.</p>
        </div>

        <div>
            <h3 class="text-xs font-bold text-indigo-600 uppercase tracking-wider mb-3 flex items-center gap-1.5 border-b border-slate-100 pb-2">
                <i class="fa-solid fa-location-dot"></i> Identificação do Atendimento
            </h3>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="space-y-1">
                    <label class="block text-xs font-semibold text-slate-600">Nº do Orçamento <span class="text-rose-500">*</span></label>
                    <div class="flex gap-2">
                        <input type="text" id="numOrcamento" readonly class="w-full text-xs px-3 py-2.5 border border-slate-300 rounded-lg font-bold text-indigo-900 bg-slate-50">
                        <button class="no-print bg-slate-200 hover:bg-slate-300 text-slate-700 text-xs font-semibold px-3 py-2.5 rounded-lg transition" onclick="alterarNumeroManual()" title="Alterar Numeração">🔄</button>
                    </div>
                </div>
                <div class="space-y-1">
                    <label class="block text-xs font-semibold text-slate-600">Sigla do Cliente / Loja <span class="text-rose-500">*</span></label>
                    <input type="text" id="sigla" placeholder="Ex: CNL" class="w-full text-xs px-3 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 uppercase font-semibold" oninput="removerErro(this);">
                </div>
                <div class="space-y-1">
                    <label class="block text-xs font-semibold text-slate-600">Data <span class="text-rose-500">*</span></label>
                    <input type="date" id="dataEmissao" class="w-full text-xs px-3 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-medium" onchange="removerErro(this);">
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
                <div class="space-y-1">
                    <label class="block text-xs font-semibold text-slate-600">Hora <span class="text-rose-500">*</span></label>
                    <input type="time" id="horaEmissao" class="w-full text-xs px-3 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-medium" onchange="removerErro(this)">
                </div>
                <div class="space-y-1">
                    <label class="block text-xs font-semibold text-slate-600">Técnico Responsável <span class="text-rose-500">*</span></label>
                    <input type="text" id="tecnico" placeholder="Nome do técnico" class="w-full text-xs px-3 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-medium" oninput="removerErro(this)">
                </div>
            </div>

            <div class="space-y-1 mt-4">
                <label class="block text-xs font-semibold text-slate-600">Motivo do Orçamento <span class="text-rose-500">*</span></label>
                <textarea id="motivo" placeholder="Digite o motivo detalhado do orçamento..." class="w-full text-xs p-3 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-medium" rows="2" oninput="removerErro(this)"></textarea>
            </div>
        </div>

        <div>
            <h3 class="text-xs font-bold text-indigo-600 uppercase tracking-wider mb-3 flex items-center gap-1.5 border-b border-slate-100 pb-2">
                <i class="fa-solid fa-boxes-stacked"></i> Peças & Serviços <span class="text-rose-500">*</span>
            </h3>
            
            <div class="border border-slate-200 rounded-xl overflow-hidden">
                <table class="w-full text-left text-xs border-collapse">
                    <thead>
                        <tr class="bg-slate-100 text-slate-800 font-bold border-b border-slate-200">
                            <th class="p-3 text-slate-800 font-bold">Descrição do Item / Serviço</th>
                            <th class="p-3 w-32 text-slate-800 font-bold">Qtd</th>
                            <th class="p-3 w-20 text-center no-print text-slate-800 font-bold">Ação</th>
                        </tr>
                    </thead>
                    <tbody id="corpoTabela" class="divide-y divide-slate-100 bg-white">
                        <tr>
                            <td class="p-2"><input type="text" placeholder="Descrição do item ou serviço" class="item-desc w-full text-xs px-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-slate-50" oninput="removerErro(this)"></td>
                            <td class="p-2"><input type="number" value="1" min="1" class="item-qtd w-full text-xs px-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-slate-50" oninput="removerErro(this)"></td>
                            <td class="p-2 text-center no-print"><button class="bg-rose-600 hover:bg-rose-500 text-white px-2.5 py-1.5 rounded-lg text-xs transition" onclick="removerLinha(this)"><i class="fa-solid fa-trash-can"></i></button></td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <button class="no-print mt-3 bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-4 py-2 rounded-lg transition shadow flex items-center gap-1.5" onclick="adicionarLinha()">
                <i class="fa-solid fa-plus"></i> Adicionar Item
            </button>
        </div>

        <div>
            <h3 class="text-xs font-bold text-indigo-600 uppercase tracking-wider mb-3 flex items-center gap-1.5 border-b border-slate-100 pb-2">
                <i class="fa-solid fa-camera"></i> Evidências Fotográficas <span class="text-rose-500">*</span>
            </h3>
            
            <div class="no-print border-2 border-dashed border-indigo-300 bg-indigo-50/50 hover:bg-indigo-50 rounded-xl p-6 text-center cursor-pointer transition" id="dropZone" onclick="document.getElementById('fotosInput').click()">
                <i class="fa-solid fa-cloud-arrow-up text-3xl text-indigo-500 mb-2"></i>
                <p class="text-xs font-bold text-slate-700">Clique para escolher as fotos ou Arraste os arquivos</p>
                <p class="text-[11px] text-slate-500 mt-1">💡 Você também pode <b>COLAR</b> uma imagem copiada pressionando <b>Ctrl + V</b>!</p>
            </div>
            <input type="file" id="fotosInput" accept="image/*" multiple class="hidden" onchange="carregarFotos(event)">

            <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4 mt-4" id="galeriaFotos"></div>
        </div>

        <div class="flex justify-end pt-4 border-t border-slate-100 no-print">
            <button class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-6 py-3 rounded-xl transition shadow-md flex items-center gap-2" onclick="imprimirOuSalvarPDF()">
                <i class="fa-solid fa-print text-sm"></i> Imprimir / Salvar em PDF
            </button>
        </div>
    </div>

    <!-- TELA 2: PAINEL DE GESTÃO -->
    <div id="painelSection" class="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 sm:p-8 space-y-6 hidden">
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 border-b border-slate-100 pb-4">
            <div>
                <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-chart-column text-indigo-600"></i> Painel de Gestão de Orçamentos
                </h2>
                <p class="text-xs text-slate-500">Acompanhe o status e gerencie todos os atendimentos emitidos.</p>
            </div>
            <button class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2" onclick="carregarDadosPainel()">
                <i class="fa-solid fa-rotate"></i> Atualizar Dados
            </button>
        </div>

        <!-- Cards de Métricas -->
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4">
            <div class="bg-slate-50 p-4 rounded-xl shadow-sm border border-slate-200 cursor-pointer hover:border-indigo-500 transition group" id="cardTotal" onclick="filtrarStatusPeloCard('TODOS')">
                <p class="text-[11px] font-semibold text-slate-500 uppercase tracking-wide">Total Geral</p>
                <h3 class="text-2xl font-bold text-indigo-600 mt-1" id="mTotal">0</h3>
            </div>
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500 cursor-pointer hover:shadow-md transition" id="cardPendente" onclick="filtrarStatusPeloCard('PENDENTE')">
                <p class="text-[11px] font-bold text-amber-600 uppercase tracking-wide">Pendentes</p>
                <h3 class="text-2xl font-bold text-amber-600 mt-1" id="mPendente">0</h3>
            </div>
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500 cursor-pointer hover:shadow-md transition" id="cardEnviado" onclick="filtrarStatusPeloCard('ENVIADO')">
                <p class="text-[11px] font-bold text-emerald-600 uppercase tracking-wide">Enviados</p>
                <h3 class="text-2xl font-bold text-emerald-600 mt-1" id="mEnviado">0</h3>
            </div>
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500 cursor-pointer hover:shadow-md transition" id="cardDeclinado" onclick="filtrarStatusPeloCard('DECLINADO')">
                <p class="text-[11px] font-bold text-rose-600 uppercase tracking-wide">Declinados</p>
                <h3 class="text-2xl font-bold text-rose-600 mt-1" id="mDeclinado">0</h3>
            </div>
        </div>

        <!-- Abas de Filtro -->
        <div class="flex gap-2 border-b border-slate-200 pb-2 overflow-x-auto">
            <button class="sub-tab-btn px-4 py-2 text-xs font-bold rounded-lg transition" id="tabPendentesBtn" onclick="filtrarStatusPeloCard('PENDENTE')">⏳ Pendentes (<span id="countTabPendentes">0</span>)</button>
            <button class="sub-tab-btn px-4 py-2 text-xs font-bold text-slate-600 hover:bg-slate-100 rounded-lg transition" id="tabEnviadosBtn" onclick="filtrarStatusPeloCard('ENVIADO')">✉ Enviados (<span id="countTabEnviados">0</span>)</button>
            <button class="sub-tab-btn px-4 py-2 text-xs font-bold text-slate-600 hover:bg-slate-100 rounded-lg transition" id="tabDeclinadosBtn" onclick="filtrarStatusPeloCard('DECLINADO')">❌ Declinados (<span id="countTabDeclinados">0</span>)</button>
            <button class="sub-tab-btn px-4 py-2 text-xs font-bold text-slate-600 hover:bg-slate-100 rounded-lg transition" id="tabTodosBtn" onclick="filtrarStatusPeloCard('TODOS')">📋 Todos</button>
        </div>

        <div class="border border-slate-200 rounded-xl overflow-hidden">
            <div class="overflow-x-auto w-full">
                <table class="min-w-max w-full text-left text-xs border-collapse">
                    <thead>
                        <tr class="bg-slate-100 text-slate-800 font-bold border-b border-slate-200">
                            <th class="p-3 text-center w-16 text-slate-800 font-bold">Nº</th>
                            <th class="p-3 text-center w-20 text-slate-800 font-bold">Sigla</th>
                            <th class="p-3 w-36 text-slate-800 font-bold">Data / Hora</th>
                            <th class="p-3 w-28 text-slate-800 font-bold">Técnico</th>
                            <th class="p-3 text-slate-800 font-bold">Motivo do Atendimento</th>
                            <th class="p-3 text-slate-800 font-bold">Itens / Detalhes</th>
                            <th class="p-3 text-center w-24 text-slate-800 font-bold">Status</th>
                            <th class="p-3 text-center w-28 text-slate-800 font-bold">Ações</th>
                        </tr>
                    </thead>
                    <tbody id="tabelaPainelBody" class="divide-y divide-slate-100 bg-white text-slate-700">
                        <tr><td colspan="8" class="p-6 text-center text-slate-400">Carregando dados...</td></tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- TELA 3: VISUALIZADOR DA PASTA DO DRIVE (ARQUIVOS) -->
    <div id="galeriaPdfSection" class="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 sm:p-8 space-y-6 hidden">
        <div>
            <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                <i class="fa-solid fa-folder-tree text-indigo-600"></i> Arquivos e Documentos
            </h2>
            <p class="text-xs text-slate-500">Navegue pelos PDFs e subpastas salvos no Google Drive diretamente na página.</p>
        </div>

        <div class="border border-slate-200 rounded-xl overflow-hidden">
            <iframe id="driveFolderIframe" src="https://drive.google.com/embeddedfolderview?id=12p-H152le372P0Nx4C2_vX5hK34caOeS#list" width="100%" height="600" class="border-0"></iframe>
        </div>
    </div>

</div>

<script>
    const CHAVE_BANCO_NUMERO = 'app_orcamento_ultimo_numero';
    const URL_GOOGLE_SHEETS = "https://script.google.com/macros/s/AKfycbw9sSw41bHALRiDoJvFYDyHVfiA5OhNgaC5harmTg-2pWPbcnsQuZKQkyL1yOZ7eP68qQ/exec";
    const SENHA_AUTORIZACAO = "GF01";

    let todosOrcamentos = [];
    let statusFiltroAtual = 'PENDENTE';

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
        configurarEventosColarEArrastar();
        carregarDadosSilenciosamente();
    };

    function alternarTela(tela) {
        document.getElementById('orcamentoSection').classList.add('hidden');
        document.getElementById('painelSection').classList.add('hidden');
        document.getElementById('galeriaPdfSection').classList.add('hidden');

        document.getElementById('btnNavOrcamento').className = 'nav-btn bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 border border-slate-700';
        document.getElementById('btnNavPainel').className = 'nav-btn bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 border border-slate-700';
        document.getElementById('btnNavGaleria').className = 'nav-btn bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 border border-slate-700';

        if (tela === 'orcamento') {
            document.getElementById('orcamentoSection').classList.remove('hidden');
            document.getElementById('btnNavOrcamento').className = 'nav-btn bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 active';
        } else if (tela === 'painel') {
            document.getElementById('painelSection').classList.remove('hidden');
            document.getElementById('btnNavPainel').className = 'nav-btn bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 active';
            
            filtrarStatusPeloCard('PENDENTE');
            carregarDadosPainel();
        } else if (tela === 'galeria') {
            document.getElementById('galeriaPdfSection').classList.remove('hidden');
            document.getElementById('btnNavGaleria').className = 'nav-btn bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2 active';
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
        }
    }

    function adicionarLinha() {
        const tbody = document.getElementById('corpoTabela');
        const tr = document.createElement('tr');
        tr.innerHTML = `
            <td class="p-2"><input type="text" placeholder="Descrição do item ou serviço" class="item-desc w-full text-xs px-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-slate-50" oninput="removerErro(this)"></td>
            <td class="p-2"><input type="number" value="1" min="1" class="item-qtd w-full text-xs px-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-slate-50" oninput="removerErro(this)"></td>
            <td class="p-2 text-center no-print"><button class="bg-rose-600 hover:bg-rose-500 text-white px-2.5 py-1.5 rounded-lg text-xs transition" onclick="removerLinha(this)"><i class="fa-solid fa-trash-can"></i></button></td>
        `;
        tbody.appendChild(tr);
    }

    function removerLinha(btn) {
        btn.closest('tr').remove();
    }

    function removerErro(elemento) {
        elemento.classList.remove('border-rose-500', 'bg-rose-50');
    }

    function processarArquivoFoto(arquivo) {
        if (!arquivo || !arquivo.type.startsWith('image/')) return;
        const leitor = new FileReader();
        leitor.onload = function(e) {
            const galeria = document.getElementById('galeriaFotos');
            const card = document.createElement('div');
            card.className = 'border border-slate-200 rounded-xl overflow-hidden bg-white p-2 text-center shadow-sm space-y-2';
            card.innerHTML = `
                <img src="${e.target.result}" alt="Evidência" class="w-full h-28 object-cover rounded-lg">
                <input type="text" placeholder="Legenda da foto" class="w-full text-[11px] px-2 py-1 border border-slate-300 rounded-md bg-slate-50">
                <button class="no-print w-full bg-rose-600 hover:bg-rose-500 text-white text-[11px] font-semibold py-1 rounded transition" onclick="this.closest('.border').remove()">Excluir</button>
            `;
            galeria.appendChild(card);
            document.getElementById('dropZone').classList.remove('border-rose-500', 'bg-rose-50');
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
        const horaEmissao = document.getElementById('horaEmissao');
        const tecnico = document.getElementById('tecnico');
        const motivo = document.getElementById('motivo');

        if (!sigla.value.trim()) { sigla.classList.add('border-rose-500', 'bg-rose-50'); erros.push("Sigla do Cliente / Loja"); }
        if (!dataEmissao.value) { dataEmissao.classList.add('border-rose-500', 'bg-rose-50'); erros.push("Data da Emissão"); }
        if (!horaEmissao.value) { horaEmissao.classList.add('border-rose-500', 'bg-rose-50'); erros.push("Hora da Emissão"); }
        if (!tecnico.value.trim()) { tecnico.classList.add('border-rose-500', 'bg-rose-50'); erros.push("Técnico Responsável"); }
        if (!motivo.value.trim()) { motivo.classList.add('border-rose-500', 'bg-rose-50'); erros.push("Motivo do Orçamento"); }

        const linhasItens = document.querySelectorAll('#corpoTabela tr');
        let itemPreenchido = false;
        if (linhasItens.length === 0) {
            erros.push("Adicione ao menos 1 item na tabela");
        } else {
            linhasItens.forEach(linha => {
                const desc = linha.querySelector('.item-desc');
                const qtd = linha.querySelector('.item-qtd');
                if (!desc.value.trim() || !qtd.value || parseFloat(qtd.value) <= 0) {
                    desc.classList.add('border-rose-500', 'bg-rose-50');
                    qtd.classList.add('border-rose-500', 'bg-rose-50');
                } else {
                    itemPreenchido = true;
                }
            });
            if (!itemPreenchido) erros.push("Descrição e Quantidade na tabela");
        }

        if (document.querySelectorAll('#galeriaFotos > div').length === 0) {
            document.getElementById('dropZone').classList.add('border-rose-500', 'bg-rose-50');
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

    function carregarDadosSilenciosamente() {
        fetch(URL_GOOGLE_SHEETS)
            .then(res => res.json())
            .then(data => {
                if (Array.isArray(data)) {
                    todosOrcamentos = data;
                }
            })
            .catch(err => console.log("Aguardando conexão com a planilha."));
    }

    function obterUltimoOrcamentoPorSigla(siglaProcurada) {
        if (!todosOrcamentos || todosOrcamentos.length === 0) return null;
        
        const siglaUpper = siglaProcurada.trim().toUpperCase();
        const registrosCliente = todosOrcamentos.filter(item => 
            String(item.sigla || '').trim().toUpperCase() === siglaUpper
        );

        if (registrosCliente.length === 0) return null;
        return registrosCliente[registrosCliente.length - 1];
    }

    async function imprimirOuSalvarPDF() {
        if (!validarFormulario()) return;

        const siglaDig = document.getElementById('sigla').value.trim();

        try {
            const res = await fetch(URL_GOOGLE_SHEETS);
            const data = await res.json();
            if (Array.isArray(data)) todosOrcamentos = data;
        } catch (e) {}

        const ultimoOrcamento = obterUltimoOrcamentoPorSigla(siglaDig);

        if (ultimoOrcamento) {
            const dataFormatada = formatarDataHoraLimpo(ultimoOrcamento.data, ultimoOrcamento.hora).replace(/<br>/g, ' ').replace(/<[^>]*>?/gm, '');
            
            const confirmar = confirm(
                `⚠️ ATENÇÃO: Já existe um orçamento cadastrado para a sigla "${siglaDig.toUpperCase()}"!\n\n` +
                `• Nº do Último Orçamento: ${ultimoOrcamento.numero}\n` +
                `• Data do Último Atendimento: ${dataFormatada}\n\n` +
                `Deseja prosseguir com o envio de um novo orçamento para este cliente?`
            );

            if (!confirmar) return;

            const senhaDigitada = prompt(`Para confirmar a emissão duplicada para a sigla "${siglaDig.toUpperCase()}", digite a senha de autorização:`);
            
            if (senhaDigitada === null) return;
            if (senhaDigitada !== SENHA_AUTORIZACAO) {
                alert("❌ Senha incorreta! Envio cancelado.");
                return;
            }
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
            sigla: siglaDig,
            data: document.getElementById('dataEmissao').value,
            hora: document.getElementById('horaEmissao').value,
            tecnico: document.getElementById('tecnico').value,
            semana: "",
            motivo: document.getElementById('motivo').value,
            itens: listaItens.join('; '),
            status: "PENDENTE"
        };

        enviarParaGoogleSheets(dados);
        window.print();

        setTimeout(() => {
            avançarNumeroOrcamento();
            carregarDadosSilenciosamente();
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
        document.getElementById('cardTotal').className = `bg-slate-50 p-4 rounded-xl shadow-sm border border-slate-200 cursor-pointer hover:border-indigo-500 transition group ${status === 'TODOS' ? 'ring-2 ring-indigo-500 bg-indigo-50/50' : ''}`;
        document.getElementById('cardPendente').className = `bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500 cursor-pointer hover:shadow-md transition ${status === 'PENDENTE' ? 'ring-2 ring-amber-500 bg-amber-50/50' : ''}`;
        document.getElementById('cardEnviado').className = `bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500 cursor-pointer hover:shadow-md transition ${status === 'ENVIADO' ? 'ring-2 ring-emerald-500 bg-emerald-50/50' : ''}`;
        document.getElementById('cardDeclinado').className = `bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500 cursor-pointer hover:shadow-md transition ${status === 'DECLINADO' ? 'ring-2 ring-rose-500 bg-rose-50/50' : ''}`;

        document.getElementById('tabPendentesBtn').className = `px-4 py-2 text-xs font-bold rounded-lg transition ${status === 'PENDENTE' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-600 hover:bg-slate-100'}`;
        document.getElementById('tabEnviadosBtn').className = `px-4 py-2 text-xs font-bold rounded-lg transition ${status === 'ENVIADO' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-600 hover:bg-slate-100'}`;
        document.getElementById('tabDeclinadosBtn').className = `px-4 py-2 text-xs font-bold rounded-lg transition ${status === 'DECLINADO' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-600 hover:bg-slate-100'}`;
        document.getElementById('tabTodosBtn').className = `px-4 py-2 text-xs font-bold rounded-lg transition ${status === 'TODOS' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-600 hover:bg-slate-100'}`;

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

    function formatarDataHoraLimpo(dataRaw, horaRaw) {
        let textoCompleto = String(dataRaw || '') + ' ' + String(horaRaw || '');
        
        let matchData = textoCompleto.match(/(\d{2})[\/_-](\d{2})[\/_-](\d{4})/) || textoCompleto.match(/(\d{4})[\/_-](\d{2})[\/_-](\d{2})/);
        let dataFormatada = '';
        
        if (matchData) {
            if (matchData[0].includes('-') && matchData[1].length === 4) {
                dataFormatada = `${matchData[3]}/${matchData[2]}/${matchData[1]}`;
            } else {
                dataFormatada = `${matchData[1]}/${matchData[2]}/${matchData[3]}`;
            }
        }

        let matchHora = textoCompleto.match(/(?:^|[^\d])([0-1]?\d|2[0-3]):([0-5]\d)(?=[^\d]|$)/);
        let horaFormatada = '';

        if (matchHora) {
            if (!(textoCompleto.includes('1899') && (matchHora[1] === '12' || matchHora[1] === '00'))) {
                horaFormatada = `${matchHora[1].padStart(2, '0')}:${matchHora[2]}`;
            }
        }

        if (dataFormatada && horaFormatada) {
            return `📅 ${dataFormatada}<br><span style="color:#64748b; font-size:11px;">⏰ ${horaFormatada}</span>`;
        } else if (dataFormatada) {
            return `📅 ${dataFormatada}`;
        }
        return '-';
    }

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
            tbody.innerHTML = `<tr><td colspan="8" class="p-6 text-center text-slate-400">Nenhum orçamento encontrado com o status: <b>${statusFiltroAtual}</b></td></tr>`;
            return;
        }

        itensFiltrados.forEach(item => {
            const st = (item.status || 'PENDENTE').toUpperCase();
            const badgeClass = st === 'PENDENTE' ? 'bg-amber-100 text-amber-800' : (st === 'ENVIADO' ? 'bg-emerald-100 text-emerald-800' : 'bg-rose-100 text-rose-800');
            const tr = document.createElement('tr');
            tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';
            tr.innerHTML = `
                <td class="p-3 text-center font-bold">${item.numero}</td>
                <td class="p-3 text-center font-semibold text-indigo-600">${item.sigla}</td>
                <td class="p-3 whitespace-nowrap">${formatarDataHoraLimpo(item.data, item.hora)}</td>
                <td class="p-3 font-semibold">${item.tecnico || '-'}</td>
                <td class="p-3">${item.motivo || '-'}</td>
                <td class="p-3 text-slate-600">${limparTextoItens(item.itens)}</td>
                <td class="p-3 text-center"><span class="px-2.5 py-1 rounded-full text-[10px] font-bold ${badgeClass}">${st}</span></td>
                <td class="p-3 text-center">${gerarBotoesAcao(item.numero, st)}</td>
            `;
            tbody.appendChild(tr);
        });
    }

    function gerarBotoesAcao(numero, statusAtual) {
        if (statusAtual === 'PENDENTE') {
            return `
                <div class="flex flex-col gap-1">
                    <button class="bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-[11px] px-2.5 py-1 rounded transition" onclick="alterarStatus('${numero}', 'ENVIADO')">Enviar</button>
                    <button class="bg-rose-600 hover:bg-rose-500 text-white font-bold text-[11px] px-2.5 py-1 rounded transition" onclick="alterarStatus('${numero}', 'DECLINADO')">Declinar</button>
                </div>
            `;
        } else {
            return `
                <button class="bg-amber-500 hover:bg-amber-400 text-white font-bold text-[11px] px-2.5 py-1.5 rounded transition" onclick="alterarStatus('${numero}', 'PENDENTE')">Voltar p/ Pendente</button>
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
</script>

</body>
</html>
