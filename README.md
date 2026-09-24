<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque & Operações - Grupo Forte Protege</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Ícones -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        body {
            background-color: #f8fafc;
            color: #1e293b;
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        /* Estilização da Tabela Estilo GF Links */
        table.custom-table {
            width: 100%;
            border-collapse: collapse;
            background-color: #ffffff;
        }

        table.custom-table th {
            background-color: #1e293b !important;
            color: #ffffff !important;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            padding: 0.75rem 0.75rem;
            border-bottom: 1px solid #334155 !important;
            user-select: none;
        }

        table.custom-table td {
            color: #334155 !important;
            font-weight: 600 !important;
            font-size: 0.8125rem !important;
            padding: 0.75rem 0.85rem;
            border-bottom: 1px solid #f1f5f9 !important;
        }

        table.custom-table tr:hover td {
            background-color: #f8fafc !important;
        }

        /* Menu de Filtro Suspenso Estilo Excel */
        .excel-filter-menu {
            position: absolute;
            z-index: 100;
            background-color: #ffffff;
            color: #334155;
            border: 1px solid #cbd5e1;
            border-radius: 12px;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
            width: 260px;
            font-size: 0.75rem;
            display: none;
        }

        .excel-filter-menu .filter-option {
            padding: 8px 12px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 600;
            color: #334155;
        }

        .excel-filter-menu .filter-option:hover {
            background-color: #f1f5f9;
            color: #4f46e5;
        }

        .excel-filter-list {
            max-height: 160px;
            overflow-y: auto;
            border: 1px solid #e2e8f0;
            border-radius: 6px;
            background-color: #f8fafc;
            padding: 4px;
        }

        .excel-filter-item {
            display: flex;
            align-items: center;
            gap: 6px;
            padding: 4px 6px;
            cursor: pointer;
            border-radius: 4px;
            font-weight: 500;
        }

        .excel-filter-item:hover {
            background-color: #e2e8f0;
        }

        .th-container {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 6px;
        }

        .filter-btn {
            background-color: #334155;
            color: #cbd5e1;
            border-radius: 4px;
            padding: 3px 6px;
            cursor: pointer;
            font-size: 0.65rem;
            transition: all 0.2s;
        }

        .filter-btn:hover {
            background-color: #4f46e5;
            color: #ffffff;
        }

        .filter-btn.active-filter {
            background-color: #10b981 !important;
            color: #ffffff !important;
        }

        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 3px; }
    </style>
</head>
<body class="bg-slate-100 font-sans min-h-screen text-slate-800 relative" onclick="closeAllFilterMenus(event)">

    <!-- TELA DE LOGIN / BLOQUEIO POR SENHA FUNCIONAL -->
    <div id="loginScreen" class="fixed inset-0 bg-slate-900/95 z-50 flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl p-8 max-w-md w-full text-center space-y-5 border border-slate-200">
            <img src="logo.png" alt="Grupo Forte Protege" class="h-12 w-auto mx-auto object-contain" onerror="this.style.display='none'">
            <div class="bg-indigo-100 text-indigo-600 w-16 h-16 rounded-full flex items-center justify-center mx-auto text-2xl shadow-inner">
                <i class="fa-solid fa-boxes-stacked"></i>
            </div>
            <div>
                <h2 class="text-xl font-bold text-slate-800">Acesso Restrito</h2>
                <p class="text-xs text-slate-500 mt-1">Informe a senha corporativa para acessar o Painel de Estoque</p>
            </div>
            <form onsubmit="handleLogin(event)" class="space-y-4 text-left">
                <div>
                    <label for="inputPassword" class="block text-xs font-semibold uppercase text-slate-500 mb-1">Senha de Acesso</label>
                    <div class="relative">
                        <input type="password" id="inputPassword" placeholder="••••••••••••" class="w-full text-sm px-4 py-3 border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500 text-center font-semibold tracking-wider bg-slate-50 pr-10">
                        <button type="button" onclick="togglePasswordVisibility()" class="absolute right-3 top-3.5 text-slate-400 hover:text-slate-600">
                            <i id="eyeIcon" class="fa-solid fa-eye"></i>
                        </button>
                    </div>
                    <p id="loginError" class="text-xs text-rose-600 font-semibold mt-2 hidden flex items-center gap-1">
                        <i class="fa-solid fa-circle-exclamation"></i> Senha incorreta. Tente novamente.
                    </p>
                </div>
                <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 text-white font-bold py-3 px-4 rounded-xl text-sm transition shadow-md shadow-indigo-600/20 flex items-center justify-center gap-2">
                    <i class="fa-solid fa-key text-xs"></i> Acessar Painel
                </button>
            </form>
            <div class="text-[11px] text-slate-400 border-t border-slate-100 pt-4 text-center">
                Grupo Forte Protege &copy; 2026 — Acesso Seguro
            </div>
        </div>
    </div>

    <!-- MODAL DE NOVA MOVIMENTAÇÃO (HISTÓRICO) -->
    <div id="modalMovimentacao" class="fixed inset-0 z-50 bg-slate-900/80 backdrop-blur-sm flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-2xl shadow-2xl p-6 max-w-lg w-full space-y-4 border border-slate-200">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="text-base font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-right-left text-emerald-600"></i> Registrar Nova Movimentação
                </h3>
                <button onclick="closeMovimentacaoModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>

            <form onsubmit="saveMovimentacao(event)" class="space-y-3">
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">Equipamento</label>
                    <select id="movEquipamento" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                        <option value="">Selecione um equipamento...</option>
                    </select>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">Data</label>
                        <input type="date" id="movData" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">Tipo de Operação</label>
                        <select id="movTipo" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="Entrada">Entrada</option>
                            <option value="Saída">Saída</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">Quantidade</label>
                        <input type="number" id="movQtd" min="1" value="1" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">Origem</label>
                        <select id="movOrigem" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="">Selecione a origem...</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">Destino</label>
                        <select id="movDestino" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="">Selecione o destino...</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">Observações / Projeto</label>
                    <input type="text" id="movObs" placeholder="Ex: Instalação Cliente X" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-xs text-slate-800 font-medium focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>

                <div class="flex justify-end gap-2 pt-2 border-t border-slate-100">
                    <button type="button" onclick="closeMovimentacaoModal()" class="px-4 py-2 bg-slate-200 hover:bg-slate-300 text-xs font-semibold rounded-lg text-slate-700 transition">Cancelar</button>
                    <button type="submit" id="btnSaveMov" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-xs font-bold rounded-lg text-white transition shadow flex items-center gap-2">
                        <i class="fa-solid fa-plus"></i> Registrar Movimentação
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- TOAST DE NOTIFICAÇÃO -->
    <div id="toastSync" class="fixed bottom-5 right-5 z-50 bg-emerald-600 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 transition-all duration-300 transform translate-y-20 opacity-0 pointer-events-none">
        <i class="fa-solid fa-circle-check text-lg"></i>
        <span id="toastMsg" class="text-xs font-semibold">Dados atualizados com sucesso!</span>
    </div>

    <!-- CABEÇALHO FIXO ESTILO GF LINKS -->
    <header id="mainHeader" class="bg-slate-900 text-white shadow-lg sticky top-0 z-40">
        <div class="max-w-[1800px] mx-auto px-6 py-4 flex flex-col lg:flex-row items-center justify-between gap-4">
            <div class="flex items-center space-x-4 w-full lg:w-auto justify-between lg:justify-start">
                <div class="flex items-center gap-3">
                    <img src="logo.png" alt="Grupo Forte Protege" class="h-10 w-auto object-contain max-h-12" onerror="this.style.display='none'">
                    <i class="fa-solid fa-boxes-stacked text-emerald-400 text-2xl"></i>
                    <div>
                        <h1 class="text-xl font-bold tracking-wide">Estoque & Operações</h1>
                        <p class="text-xs text-slate-400" id="dbStatusBadge">Status DB: Conectando...</p>
                        <p class="text-[11px] text-slate-400 font-medium mt-0.5" id="lastUpdateText">Última atualização: Em tempo real</p>
                    </div>
                </div>
            </div>

            <div class="flex items-center gap-3 flex-wrap justify-center lg:justify-end w-full lg:w-auto">
                <button onclick="openMovimentacaoModal()" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-bold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-plus text-sm"></i>
                    <span>Nova Movimentação</span>
                </button>

                <button onclick="syncData()" id="btnSync" class="bg-sky-600 hover:bg-sky-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i id="iconSync" class="fa-solid fa-rotate text-sm"></i>
                    <span>Sincronizar Google Drive</span>
                </button>

                <!-- TAB SWITCHER COM A ABA COMPRAS -->
                <div class="flex items-center bg-slate-950 p-1 rounded-xl border border-slate-800">
                    <button onclick="switchTab('estoque')" id="btnTabEstoque" class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-200 bg-indigo-600 text-white shadow-md flex items-center gap-2">
                        <i class="fa-solid fa-cubes"></i> Estoque
                    </button>
                    <button onclick="switchTab('historico')" id="btnTabHistorico" class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left"></i> Histórico
                    </button>
                    <button onclick="switchTab('compras')" id="btnTabCompras" class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2">
                        <i class="fa-solid fa-cart-shopping"></i> Compras
                    </button>
                </div>

                <button onclick="handleLogout()" class="bg-slate-700 hover:bg-rose-600 text-white text-xs font-semibold px-3 py-2.5 rounded-lg transition shadow flex items-center gap-1.5" title="Sair do Painel">
                    <i class="fa-solid fa-right-from-bracket"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- CONTEÚDO PRINCIPAL -->
    <main class="max-w-[1800px] mx-auto px-6 py-6 space-y-6">

        <!-- CARDS DE MÉTRICAS PRINCIPAIS (KPIS) -->
        <section class="space-y-4">
            <div class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 gap-4">
                <div onclick="filterByMetric('central')" id="kpi-card-central" class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-sky-500 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group">
                    <div class="flex items-center justify-between pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-sky-600 uppercase tracking-wide">Central</p>
                            <h3 class="text-2xl font-bold text-sky-600 mt-1" id="kpiCentral">0</h3>
                        </div>
                        <div class="bg-sky-100 p-3 rounded-lg text-sky-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-warehouse text-lg"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-sky-600 mt-2 pointer-events-none">Clique para isolar <i class="fa-solid fa-arrow-down text-[10px] ml-1"></i></p>
                </div>

                <div onclick="filterByMetric('tecnico')" id="kpi-card-tecnico" class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group">
                    <div class="flex items-center justify-between pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-amber-600 uppercase tracking-wide">Técnico Marcelo</p>
                            <h3 class="text-2xl font-bold text-amber-600 mt-1" id="kpiTecnico">0</h3>
                        </div>
                        <div class="bg-amber-100 p-3 rounded-lg text-amber-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-user-gear text-lg"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-amber-600 mt-2 pointer-events-none">Clique para isolar <i class="fa-solid fa-arrow-down text-[10px] ml-1"></i></p>
                </div>

                <div onclick="filterByMetric('op')" id="kpi-card-op" class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group">
                    <div class="flex items-center justify-between pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-emerald-600 uppercase tracking-wide">Estoque Op.</p>
                            <h3 class="text-2xl font-bold text-emerald-600 mt-1" id="kpiOperacional">0</h3>
                        </div>
                        <div class="bg-emerald-100 p-3 rounded-lg text-emerald-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-truck-ramp-box text-lg"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-emerald-600 mt-2 pointer-events-none">Clique para isolar <i class="fa-solid fa-arrow-down text-[10px] ml-1"></i></p>
                </div>

                <div onclick="filterByMetric('acervo')" id="kpi-card-acervo" class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-purple-500 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group">
                    <div class="flex items-center justify-between pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-purple-600 uppercase tracking-wide">Acervo Op.</p>
                            <h3 class="text-2xl font-bold text-purple-600 mt-1" id="kpiAcervo">0</h3>
                        </div>
                        <div class="bg-purple-100 p-3 rounded-lg text-purple-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-laptop text-lg"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-purple-600 mt-2 pointer-events-none">Clique para isolar <i class="fa-solid fa-arrow-down text-[10px] ml-1"></i></p>
                </div>

                <div onclick="filterByMetric('total')" id="kpi-card-total" class="col-span-2 sm:col-span-1 bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-indigo-600 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group">
                    <div class="flex items-center justify-between pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-indigo-600 uppercase tracking-wide">Total Físico</p>
                            <h3 class="text-2xl font-bold text-indigo-600 mt-1" id="kpiTotal">0</h3>
                        </div>
                        <div class="bg-indigo-100 p-3 rounded-lg text-indigo-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-cubes text-lg"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-indigo-600 mt-2 pointer-events-none">Exibir todas as colunas <i class="fa-solid fa-sliders text-[10px] ml-1"></i></p>
                </div>
            </div>

            <!-- BOTÃO LISTAR EQUIPAMENTOS & CARDS OCULTOS POR EQUIPAMENTO -->
            <div class="flex flex-col gap-3">
                <div class="flex justify-between items-center bg-slate-200/60 px-4 py-2.5 rounded-xl border border-slate-300/70">
                    <span class="text-xs font-bold text-slate-700 flex items-center gap-2">
                        <i class="fa-solid fa-list-ol text-indigo-600"></i> Resumo Individual de Equipamentos
                    </span>
                    <button onclick="toggleEquipCards()" id="btnToggleEquip" class="px-3 py-1.5 bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg text-xs font-bold transition shadow flex items-center gap-2">
                        <i id="iconToggleEquip" class="fa-solid fa-layer-group"></i> 
                        <span id="textToggleEquip">Listar Equipamentos</span>
                    </button>
                </div>

                <!-- PAINEL DE CARDS DE EQUIPAMENTOS (INICIALMENTE OCULTO) -->
                <div id="secEquipCards" class="hidden transition-all duration-300 pt-2">
                    <div id="gridEquipCards" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-3">
                        <!-- Gerado dinamicamente via JS -->
                    </div>
                </div>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
            <div class="p-4 border-b border-slate-100 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-50/50">
                <div>
                    <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-indigo-600 text-base"></i> Disponibilidade por Equipamento
                    </h2>
                    <p class="text-xs text-slate-500" id="activeFilterBadge">Mostrando todas as localizações</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('estoque')" class="px-3 py-2 bg-slate-200 hover:bg-slate-300 text-xs font-semibold text-slate-700 rounded-lg transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-600 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Pesquisa rápida..." class="w-full text-xs pl-8 pr-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                        <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-400 text-xs"></i>
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblEstoque">
                    <thead>
                        <tr id="theadEstoqueTr"></tr>
                    </thead>
                    <tbody id="tbodyEstoque" class="divide-y divide-slate-100"></tbody>
                </table>
            </div>
        </section>

        <!-- ABA 2: HISTÓRICO DE MOVIMENTAÇÕES -->
        <section id="secHistorico" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden hidden">
            <div class="p-4 border-b border-slate-100 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-50/50">
                <div>
                    <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-arrow-right-arrow-left text-indigo-600 text-base"></i> Histórico de Entradas e Saídas
                    </h2>
                    <p class="text-xs text-slate-500">Exibindo automaticamente da movimentação mais recente para a mais antiga</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('historico')" class="px-3 py-2 bg-slate-200 hover:bg-slate-300 text-xs font-semibold text-slate-700 rounded-lg transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-600 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Pesquisa rápida..." class="w-full text-xs pl-8 pr-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                        <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-400 text-xs"></i>
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblHistorico">
                    <thead>
                        <tr>
                            <th>
                                <div class="th-container">
                                    <span>Data</span>
                                    <button class="filter-btn" id="fbtn-historico-data" onclick="toggleFilterDropdown(event, 'historico', 'data')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Equipamento</span>
                                    <button class="filter-btn" id="fbtn-historico-item" onclick="toggleFilterDropdown(event, 'historico', 'item')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Tipo</span>
                                    <button class="filter-btn" id="fbtn-historico-tipo" onclick="toggleFilterDropdown(event, 'historico', 'tipo')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Qtd</span>
                                    <button class="filter-btn" id="fbtn-historico-qtd" onclick="toggleFilterDropdown(event, 'historico', 'qtd')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Origem</span>
                                    <button class="filter-btn" id="fbtn-historico-origem" onclick="toggleFilterDropdown(event, 'historico', 'origem')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Destino</span>
                                    <button class="filter-btn" id="fbtn-historico-destino" onclick="toggleFilterDropdown(event, 'historico', 'destino')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Observações / Projeto</span>
                                    <button class="filter-btn" id="fbtn-historico-obs" onclick="toggleFilterDropdown(event, 'historico', 'obs')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                        </tr>
                    </thead>
                    <tbody id="tbodyHistorico" class="divide-y divide-slate-100"></tbody>
                </table>
            </div>
        </section>

        <!-- ABA 3: HISTÓRICO DE COMPRAS (FORNECEDORES) -->
        <section id="secCompras" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden hidden">
            <div class="p-4 border-b border-slate-100 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-50/50">
                <div>
                    <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-cart-shopping text-indigo-600 text-base"></i> Histórico de Compras (Fornecedores)
                    </h2>
                    <p class="text-xs text-slate-500">Exibindo automaticamente movimentações com origem em Fornecedores</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('compras')" class="px-3 py-2 bg-slate-200 hover:bg-slate-300 text-xs font-semibold text-slate-700 rounded-lg transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-600 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <input type="text" id="searchCompras" onkeyup="filterCompras()" placeholder="Pesquisa rápida..." class="w-full text-xs pl-8 pr-3 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                        <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-400 text-xs"></i>
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblCompras">
                    <thead>
                        <tr>
                            <th>
                                <div class="th-container">
                                    <span>Data</span>
                                    <button class="filter-btn" id="fbtn-compras-data" onclick="toggleFilterDropdown(event, 'compras', 'data')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Equipamento</span>
                                    <button class="filter-btn" id="fbtn-compras-item" onclick="toggleFilterDropdown(event, 'compras', 'item')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Tipo</span>
                                    <button class="filter-btn" id="fbtn-compras-tipo" onclick="toggleFilterDropdown(event, 'compras', 'tipo')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Qtd</span>
                                    <button class="filter-btn" id="fbtn-compras-qtd" onclick="toggleFilterDropdown(event, 'compras', 'qtd')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Origem</span>
                                    <button class="filter-btn" id="fbtn-compras-origem" onclick="toggleFilterDropdown(event, 'compras', 'origem')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Destino</span>
                                    <button class="filter-btn" id="fbtn-compras-destino" onclick="toggleFilterDropdown(event, 'compras', 'destino')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Observações / Projeto</span>
                                    <button class="filter-btn" id="fbtn-compras-obs" onclick="toggleFilterDropdown(event, 'compras', 'obs')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                        </tr>
                    </thead>
                    <tbody id="tbodyCompras" class="divide-y divide-slate-100"></tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- MENU ESTILO EXCEL FLUTUANTE -->
    <div id="excelFilterDropdown" class="excel-filter-menu" onclick="event.stopPropagation()">
        <div class="filter-option border-b border-slate-100" onclick="applySort('asc')">
            <i class="fa-solid fa-arrow-down-a-z text-indigo-600"></i> Classificar de A a Z
        </div>
        <div class="filter-option border-b border-slate-200" onclick="applySort('desc')">
            <i class="fa-solid fa-arrow-up-z-a text-indigo-600"></i> Classificar de Z a A
        </div>

        <div class="p-2 border-b border-slate-200">
            <input type="text" id="excelSearchBox" oninput="filterExcelCheckboxList()" placeholder="Pesquisar itens..." class="w-full bg-slate-100 border border-slate-300 rounded px-2 py-1 text-xs focus:outline-none focus:ring-1 focus:ring-indigo-500">
        </div>

        <div class="p-2">
            <div class="excel-filter-item font-bold border-b border-slate-200 pb-1 mb-1">
                <input type="checkbox" id="chkSelectAll" onchange="toggleSelectAllCheckboxes(this.checked)" checked class="rounded text-indigo-600 focus:ring-indigo-500">
                <label for="chkSelectAll">(Selecionar Tudo)</label>
            </div>
            <div id="excelCheckboxList" class="excel-filter-list"></div>
        </div>

        <div class="p-2 bg-slate-50 border-t border-slate-200 flex justify-end gap-2 rounded-b-xl">
            <button onclick="confirmColumnFilter()" class="px-3 py-1 bg-indigo-600 text-white rounded font-semibold hover:bg-indigo-500 text-xs transition">OK</button>
            <button onclick="closeExcelFilterMenu()" class="px-3 py-1 bg-slate-200 text-slate-700 rounded font-semibold hover:bg-slate-300 text-xs transition">Cancelar</button>
        </div>
    </div>

    <!-- RODAPÉ -->
    <footer class="bg-white border-t border-slate-200 py-4 text-center text-xs text-slate-500 mt-12">
        Grupo Forte Protege &copy; 2026 — Controle Interno Operacional
    </footer>

    <!-- SCRIPT DE INTEGRAÇÃO FUNCIONAL COMPLETA -->
    <script>
        const CORRECT_PASSWORD = "gF@2026*Estoque";
        const SHEET_ID = '1v-MZ_ga3DtOk2UfDxRZNV0awVWd3jdo1hSzCwUyvARE';
        const WEB_APP_URL = 'https://script.google.com/macros/s/AKfycbwz6R2-bOIcDliyuPDDNjInlYDNB6aL_uUdSGMTbfLubJhb1YCmvlUf2PeeNZdfhItW1g/exec';

        const TAB_ESTOQUE_NAME = 'Geral';
        const TAB_HISTORICO_NAME = 'Entradas-Saidas';

        let estoqueData = [];
        let historicoData = [];

        function checkAuth() {
            if (sessionStorage.getItem('gf_authenticated') === 'true') {
                document.getElementById('loginScreen').classList.add('hidden');
            } else {
                document.getElementById('loginScreen').classList.remove('hidden');
            }
        }

        function handleLogin(e) {
            e.preventDefault();
            const passInput = document.getElementById('inputPassword');
            const errorMsg = document.getElementById('loginError');

            if (passInput.value === CORRECT_PASSWORD) {
                sessionStorage.setItem('gf_authenticated', 'true');
                document.getElementById('loginScreen').classList.add('hidden');
                errorMsg.classList.add('hidden');
                passInput.classList.remove('border-rose-500');
            } else {
                errorMsg.classList.remove('hidden');
                passInput.classList.add('border-rose-500');
                passInput.focus();
            }
        }

        function handleLogout() {
            sessionStorage.removeItem('gf_authenticated');
            document.getElementById('inputPassword').value = '';
            document.getElementById('loginError').classList.add('hidden');
            checkAuth();
        }

        function togglePasswordVisibility() {
            const input = document.getElementById('inputPassword');
            const icon = document.getElementById('eyeIcon');
            if (input.type === 'password') {
                input.type = 'text';
                icon.classList.remove('fa-eye');
                icon.classList.add('fa-eye-slash');
            } else {
                input.type = 'password';
                icon.classList.remove('fa-eye-slash');
                icon.classList.add('fa-eye');
            }
        }

        function parseDateString(dateVal) {
            if (!dateVal || dateVal === '-' || dateVal.toString().trim() === '') {
                return { formatted: '-', timestamp: 0 };
            }
            let str = dateVal.toString().trim();
            if (str.includes('T')) str = str.split('T')[0];

            let day, month, year;

            if (str.includes('/')) {
                const parts = str.split('/');
                if (parts.length === 3) {
                    let p1 = parseInt(parts[0], 10);
                    let p2 = parseInt(parts[1], 10);
                    let p3 = parseInt(parts[2].trim(), 10);

                    if (parts[0].length === 4) {
                        year = p1; month = p2; day = p3;
                    } else if (p1 > 12) {
                        day = p1; month = p2; year = p3;
                    } else if (p2 > 12) {
                        month = p1; day = p2; year = p3;
                    } else {
                        day = p1; month = p2; year = p3;
                    }
                }
            } else if (str.includes('-')) {
                const parts = str.split('-');
                if (parts.length === 3) {
                    if (parts[0].length === 4) {
                        year = parseInt(parts[0], 10);
                        month = parseInt(parts[1], 10);
                        day = parseInt(parts[2], 10);
                    } else {
                        day = parseInt(parts[0], 10);
                        month = parseInt(parts[1], 10);
                        year = parseInt(parts[2], 10);
                    }
                }
            }

            if (day && month && year && !isNaN(day) && !isNaN(month) && !isNaN(year)) {
                const dt = new Date(year, month - 1, day);
                const dd = String(day).padStart(2, '0');
                const mm = String(month).padStart(2, '0');
                return {
                    formatted: `${dd}/${mm}/${year}`,
                    timestamp: dt.getTime()
                };
            }

            return { formatted: str, timestamp: 0 };
        }

        function normalizeKey(str) {
            if (!str) return '';
            return str.toString()
                      .toLowerCase()
                      .normalize("NFD").replace(/[\u0300-\u036f]/g, "")
                      .replace(/[^a-z0-9]/g, "");
        }

        async function fetchGoogleSheetCSV(tabName) {
            const url = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?tqx=out:csv&sheet=${encodeURIComponent(tabName)}`;
            const response = await fetch(url);
            if (!response.ok) throw new Error(`Falha ao carregar aba ${tabName}`);
            const csvText = await response.text();
            return parseCSV(csvText);
        }

        function parseCSV(text) {
            const lines = text.split('\n').map(l => l.trim()).filter(l => l.length > 0);
            if (lines.length <= 1) return [];

            const rawHeaders = lines[0].split(',').map(h => h.replace(/^"(.*)"$/, '$1').trim());
            const normalizedHeaders = rawHeaders.map(h => normalizeKey(h));
            const result = [];

            for (let i = 1; i < lines.length; i++) {
                const values = lines[i].split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/).map(v => v.replace(/^"(.*)"$/, '$1').trim());
                const obj = { _rowIndex: i + 1 };
                normalizedHeaders.forEach((normKey, index) => {
                    let val = values[index] || '';
                    if (!isNaN(val) && val !== '') val = Number(val);
                    obj[normKey] = val;
                });
                result.push(obj);
            }
            return result;
        }

        async function loadDataFromSheet() {
            try {
                let loadedViaWebApp = false;

                try {
                    const webAppResponse = await fetch(WEB_APP_URL);
                    if (webAppResponse.ok) {
                        const json = await webAppResponse.json();
                        if (json && Array.isArray(json.estoque) && Array.isArray(json.historico)) {
                            parseWebAppData(json);
                            loadedViaWebApp = true;
                        }
                    }
                } catch (e) {
                    console.warn('Busca via WebApp GET indisponível, utilizando fallback CSV gviz...');
                }

                if (!loadedViaWebApp) {
                    await parseCSVData();
                }

                updateKPICards();
                renderEquipmentCards();
                processData('estoque');
                processData('historico');
                processData('compras');
                populateMovimentacaoOptions();
                
                const badge = document.getElementById('dbStatusBadge');
                if (badge) {
                    badge.innerText = `Status DB: Conectado (${estoqueData.length} itens)`;
                    badge.className = "text-xs text-emerald-400 font-semibold";
                }

            } catch (err) {
                console.error('Erro ao conectar com o Google Sheets:', err);
                const badge = document.getElementById('dbStatusBadge');
                if (badge) {
                    badge.innerText = "Status DB: Erro de Conexão";
                    badge.className = "text-xs text-rose-400 font-semibold";
                }
            }
        }

        function parseWebAppData(json) {
            estoqueData = [];
            json.estoque.forEach(r => {
                const item = r['equipamento'] || r['item'] || r['descricao'] || r['nome'] || '';
                const itemLower = item.toString().toLowerCase().trim();
                if (!item || itemLower.includes('total') || itemLower === 'subtotal') return;

                const central = Number(r['central']) || 0;
                const tecnico = Number(r['tecnicomarcelo'] || r['tecnico'] || r['marcelo']) || 0;
                const op = Number(r['estoqueop'] || r['op'] || r['estoqueoperacional']) || 0;
                const acervo = Number(r['acervoop'] || r['acervo'] || r['acervooperacional']) || 0;
                const totalGeral = Number(r['totalgeral'] || r['total']) || (central + tecnico + op + acervo);

                estoqueData.push({
                    _rowIndex: r._rowIndex,
                    item: item,
                    central, tecnico, op, acervo,
                    total: totalGeral
                });
            });

            historicoData = [];
            json.historico.forEach(r => {
                const item = r['equipamento'] || r['item'] || '';
                const itemLower = item.toString().toLowerCase().trim();
                if (!item || itemLower.includes('total') || itemLower === 'subtotal') return;

                const tipo = r['entradasaida'] || r['tipo'] || r['operacao'] || '-';
                const qtd = Number(r['quantidade'] || r['qtd']) || 0;

                if (tipo !== 'Entrada' && tipo !== 'Saída') return;

                const rawDate = r['data'] || r['datahora'] || '';
                const parsedDate = parseDateString(rawDate);

                historicoData.push({
                    _rowIndex: r._rowIndex,
                    data: parsedDate.formatted,
                    _timestamp: parsedDate.timestamp,
                    item: item || '-',
                    tipo: tipo,
                    qtd: qtd,
                    origem: r['origem'] || '-',
                    destino: r['destino'] || '-',
                    obs: r['observacoes'] || r['observacao'] || r['obs'] || r['projeto'] || '-'
                });
            });

            historicoData.sort((a, b) => {
                if (b._timestamp !== a._timestamp) return b._timestamp - a._timestamp;
                return b._rowIndex - a._rowIndex;
            });
        }

        async function parseCSVData() {
            const rawEstoque = await fetchGoogleSheetCSV(TAB_ESTOQUE_NAME);
            estoqueData = [];

            rawEstoque.forEach(r => {
                const item = r['equipamento'] || r['item'] || r['descricao'] || r['nome'] || '';
                const itemLower = item.toString().toLowerCase().trim();

                if (!item || itemLower.includes('total') || itemLower === 'subtotal') return;

                const central = Number(r['central']) || 0;
                const tecnico = Number(r['tecnicomarcelo'] || r['tecnico'] || r['marcelo']) || 0;
                const op = Number(r['estoqueop'] || r['op'] || r['estoqueoperacional']) || 0;
                const acervo = Number(r['acervoop'] || r['acervo'] || r['acervooperacional']) || 0;
                const totalGeral = Number(r['totalgeral'] || r['total']) || (central + tecnico + op + acervo);

                estoqueData.push({
                    _rowIndex: r._rowIndex,
                    item: item,
                    central, tecnico, op, acervo,
                    total: totalGeral
                });
            });

            let rawHistorico = [];
            try {
                rawHistorico = await fetchGoogleSheetCSV(TAB_HISTORICO_NAME);
            } catch (e) {
                console.warn(`Aba ${TAB_HISTORICO_NAME} não encontrada.`);
            }

            historicoData = [];
            rawHistorico.forEach(r => {
                const item = r['equipamento'] || r['item'] || '';
                const itemLower = item.toString().toLowerCase().trim();

                if (!item || itemLower.includes('total') || itemLower === 'subtotal') return;

                const tipo = r['entradasaida'] || r['tipo'] || r['operacao'] || '-';
                const qtd = Number(r['quantidade'] || r['qtd']) || 0;

                if (tipo !== 'Entrada' && tipo !== 'Saída') return;

                const rawDate = r['data'] || r['datahora'] || '';
                const parsedDate = parseDateString(rawDate);

                historicoData.push({
                    _rowIndex: r._rowIndex,
                    data: parsedDate.formatted,
                    _timestamp: parsedDate.timestamp,
                    item: item || '-',
                    tipo: tipo,
                    qtd: qtd,
                    origem: r['origem'] || '-',
                    destino: r['destino'] || '-',
                    obs: r['observacoes'] || r['observacao'] || r['obs'] || r['projeto'] || '-'
                });
            });

            historicoData.sort((a, b) => {
                if (b._timestamp !== a._timestamp) return b._timestamp - a._timestamp;
                return b._rowIndex - a._rowIndex;
            });
        }

        function updateKPICards() {
            const totalCentral = estoqueData.reduce((acc, curr) => acc + curr.central, 0);
            const totalTecnico = estoqueData.reduce((acc, curr) => acc + curr.tecnico, 0);
            const totalOp = estoqueData.reduce((acc, curr) => acc + curr.op, 0);
            const totalAcervo = estoqueData.reduce((acc, curr) => acc + curr.acervo, 0);
            const totalFisico = estoqueData.reduce((acc, curr) => acc + curr.total, 0);

            document.getElementById('kpiCentral').innerText = totalCentral;
            document.getElementById('kpiTecnico').innerText = totalTecnico;
            document.getElementById('kpiOperacional').innerText = totalOp;
            document.getElementById('kpiAcervo').innerText = totalAcervo;
            document.getElementById('kpiTotal').innerText = totalFisico;
        }

        /* RENDERING DOS CARDS INDIVIDUAIS POR EQUIPAMENTO */
        function renderEquipmentCards() {
            const container = document.getElementById('gridEquipCards');
            if (!container) return;
            container.innerHTML = '';

            estoqueData.forEach(item => {
                const card = document.createElement('div');
                card.className = "bg-white p-3 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-indigo-600 cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 flex flex-col justify-between min-h-[85px] group";
                card.onclick = () => filterByEquipment(item.item);
                
                card.innerHTML = `
                    <div class="flex items-start justify-between pointer-events-none gap-2">
                        <span class="text-[11px] font-bold text-slate-800 leading-snug break-words pr-1 min-w-0" title="${item.item}">${item.item}</span>
                        <span class="text-sm font-black text-indigo-600 bg-indigo-50 px-2 py-0.5 rounded-md border border-indigo-100 shrink-0 self-start">${item.total}</span>
                    </div>
                    <p class="text-[10px] font-bold text-indigo-500 mt-2 pointer-events-none flex items-center justify-between border-t border-slate-100 pt-1.5">
                        <span>Ver detalhes</span>
                        <i class="fa-solid fa-arrow-down text-[9px] group-hover:translate-y-0.5 transition-transform"></i>
                    </p>
                `;
                container.appendChild(card);
            });
        }

        function toggleEquipCards() {
            const sec = document.getElementById('secEquipCards');
            const btnText = document.getElementById('textToggleEquip');
            const icon = document.getElementById('iconToggleEquip');

            if (sec.classList.contains('hidden')) {
                sec.classList.remove('hidden');
                btnText.innerText = "Ocultar Equipamentos";
                icon.className = "fa-solid fa-eye-slash";
            } else {
                sec.classList.add('hidden');
                btnText.innerText = "Listar Equipamentos";
                icon.className = "fa-solid fa-layer-group";
            }
        }

        function filterByEquipment(itemName) {
            switchTab('estoque', false);
            
            tableState.estoque.metricFilter = null;
            highlightActiveMetricCard(null);

            const searchInput = document.getElementById('searchEstoque');
            searchInput.value = itemName;
            filterEstoque();

            const badge = document.getElementById('activeFilterBadge');
            badge.innerHTML = `<span class="text-indigo-600 font-bold">Filtro ativo:</span> Exibindo apenas o equipamento <strong class="text-slate-800 font-bold">${itemName}</strong>`;

            const tableElement = document.getElementById('tblEstoque');
            if (tableElement) {
                setTimeout(() => {
                    const headerHeight = document.getElementById('mainHeader')?.offsetHeight || 80;
                    const elementPosition = tableElement.getBoundingClientRect().top;
                    const offsetPosition = elementPosition + window.pageYOffset - headerHeight - 16;

                    window.scrollTo({
                        top: offsetPosition,
                        behavior: 'smooth'
                    });
                }, 50);
            }
        }

        function openMovimentacaoModal() {
            populateMovimentacaoOptions();
            
            const today = new Date();
            const yyyy = today.getFullYear();
            const mm = String(today.getMonth() + 1).padStart(2, '0');
            const dd = String(today.getDate()).padStart(2, '0');
            document.getElementById('movData').value = `${yyyy}-${mm}-${dd}`;

            document.getElementById('modalMovimentacao').classList.remove('hidden');
        }

        function closeMovimentacaoModal() {
            document.getElementById('modalMovimentacao').classList.add('hidden');
        }

        function populateMovimentacaoOptions() {
            const selectEquip = document.getElementById('movEquipamento');
            selectEquip.innerHTML = '<option value="">Selecione um equipamento...</option>';
            estoqueData.forEach(e => {
                const opt = document.createElement('option');
                opt.value = e.item;
                opt.textContent = e.item;
                selectEquip.appendChild(opt);
            });

            const defaultLocations = ["Central", "Técnico Marcelo", "Estoque Operacional", "Acervo Operacional", "Cliente", "Fornecedor", "Levantamento"];

            const normalizeLocName = (loc) => {
                if (!loc) return '';
                const str = loc.toString().trim();
                if (str === 'Estoque Op.') return 'Estoque Operacional';
                if (str === 'Acervo Op.') return 'Acervo Operacional';
                return str;
            };

            const origensUnicas = [...new Set([...defaultLocations, ...historicoData.map(h => normalizeLocName(h.origem))])]
                .filter(v => v && v !== '-')
                .sort((a, b) => a.localeCompare(b, 'pt-BR'));

            const destinosUnicos = [...new Set([...defaultLocations, ...historicoData.map(h => normalizeLocName(h.destino))])]
                .filter(v => v && v !== '-')
                .sort((a, b) => a.localeCompare(b, 'pt-BR'));

            const selectOrigem = document.getElementById('movOrigem');
            selectOrigem.innerHTML = '<option value="">Selecione a origem...</option>';
            origensUnicas.forEach(loc => {
                const opt = document.createElement('option');
                opt.value = loc;
                opt.textContent = loc;
                selectOrigem.appendChild(opt);
            });

            const selectDestino = document.getElementById('movDestino');
            selectDestino.innerHTML = '<option value="">Selecione o destino...</option>';
            destinosUnicos.forEach(loc => {
                const opt = document.createElement('option');
                opt.value = loc;
                opt.textContent = loc;
                selectDestino.appendChild(opt);
            });
        }

        /* SALVA A MOVIMENTAÇÃO E REQUISITA SINCRONIZAÇÃO AUTOMÁTICA IMEDIATAMENTE */
        async function saveMovimentacao(e) {
            e.preventDefault();
            const btn = document.getElementById('btnSaveMov');
            btn.disabled = true;
            btn.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Registrando...`;

            const rawDate = document.getElementById('movData').value;
            let formattedDate = '';
            if (rawDate && rawDate.includes('-')) {
                const parts = rawDate.split('-');
                formattedDate = `${parts[2]}/${parts[1]}/${parts[0]}`;
            } else {
                const now = new Date();
                formattedDate = `${String(now.getDate()).padStart(2, '0')}/${String(now.getMonth() + 1).padStart(2, '0')}/${now.getFullYear()}`;
            }

            const payload = {
                data: formattedDate,
                equipamento: document.getElementById('movEquipamento').value,
                tipo: document.getElementById('movTipo').value,
                qtd: parseInt(document.getElementById('movQtd').value, 10),
                origem: document.getElementById('movOrigem').value,
                destino: document.getElementById('movDestino').value,
                obs: document.getElementById('movObs').value
            };

            try {
                await fetch(WEB_APP_URL, {
                    method: 'POST',
                    mode: 'no-cors',
                    headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                    body: JSON.stringify({ action: 'addHistorico', payload })
                });

                closeMovimentacaoModal();
                
                // Aguarda 1 segundo para a planilha do Google processar as fórmulas e efetua a sincronização automática imediata
                setTimeout(async () => {
                    await syncData();
                }, 1000);

            } catch (err) {
                alert('Erro ao registrar movimentação.');
            } finally {
                btn.disabled = false;
                btn.innerHTML = `<i class="fa-solid fa-plus"></i> Registrar Movimentação`;
            }
        }

        function showToast(msg) {
            const toast = document.getElementById('toastSync');
            document.getElementById('toastMsg').innerText = msg;
            toast.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
            }, 3000);
        }

        const tableState = {
            estoque: { filters: {}, sortCol: null, sortDir: null, metricFilter: null },
            historico: { filters: {}, sortCol: null, sortDir: null },
            compras: { filters: {}, sortCol: null, sortDir: null }
        };

        let currentActiveContext = { tableId: null, colKey: null };

        const estoqueColsDef = [
            { key: 'item', label: 'Equipamento', align: 'text-left', class: 'font-bold text-slate-900' },
            { key: 'central', label: 'Central', align: 'text-center', class: 'text-center font-bold text-slate-800' },
            { key: 'tecnico', label: 'Técnico Marcelo', align: 'text-center', class: 'text-center font-bold text-amber-700' },
            { key: 'op', label: 'Estoque Op.', align: 'text-center', class: 'text-center font-bold text-emerald-700' },
            { key: 'acervo', label: 'Acervo Op.', align: 'text-center', class: 'text-center font-bold text-purple-700' },
            { key: 'total', label: 'Total Geral', align: 'text-center', class: 'text-center font-bold text-indigo-600 text-base' }
        ];

        function renderEstoque(data) {
            const theadTr = document.getElementById('theadEstoqueTr');
            const tbody = document.getElementById('tbodyEstoque');
            const metricFilter = tableState.estoque.metricFilter;

            const activeCols = estoqueColsDef.filter(col => {
                if (col.key === 'item' || col.key === 'total') return true;
                if (!metricFilter) return true; 
                return col.key === metricFilter; 
            });

            theadTr.innerHTML = activeCols.map(col => `
                <th class="${col.align}">
                    <div class="th-container ${col.align === 'text-center' ? 'justify-center' : ''}">
                        <span>${col.label}</span>
                        <button class="filter-btn" id="fbtn-estoque-${col.key}" onclick="toggleFilterDropdown(event, 'estoque', '${col.key}')"><i class="fa-solid fa-filter"></i></button>
                    </div>
                </th>
            `).join('');

            tbody.innerHTML = '';
            
            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="${activeCols.length}" class="p-6 text-center text-slate-400">Nenhum equipamento encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.innerHTML = activeCols.map(col => {
                    let val = row[col.key];
                    if (val === 0 || val === null || val === undefined) val = '-';
                    return `<td class="${col.class}">${val}</td>`;
                }).join('');
                tbody.appendChild(tr);
            });
        }

        function renderHistoricoTable(data, tbodyId = 'tbodyHistorico') {
            const tbody = document.getElementById(tbodyId);
            if (!tbody) return;
            tbody.innerHTML = '';

            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-6 text-center text-slate-400">Nenhum registro encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const badgeClass = row.tipo === 'Entrada' 
                    ? 'bg-emerald-100 text-emerald-800 border-emerald-300' 
                    : 'bg-rose-100 text-rose-800 border-rose-300';
                
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="font-bold text-slate-900 font-mono text-xs whitespace-nowrap">${row.data}</td>
                    <td class="font-bold text-slate-800">${row.item}</td>
                    <td class="text-center">
                        <span class="px-2.5 py-0.5 text-xs rounded-full border ${badgeClass} font-bold">${row.tipo}</span>
                    </td>
                    <td class="text-center font-bold text-slate-800">${row.qtd}</td>
                    <td class="font-semibold text-slate-700">${row.origem}</td>
                    <td class="font-semibold text-slate-700">${row.destino}</td>
                    <td class="font-medium text-slate-600 italic">${row.obs || '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function processData(tableId) {
            let dataset;
            if (tableId === 'estoque') {
                dataset = [...estoqueData];
            } else if (tableId === 'historico') {
                dataset = [...historicoData];
            } else if (tableId === 'compras') {
                dataset = historicoData.filter(row => row.origem && row.origem.toString().toLowerCase().includes('fornecedor'));
            }

            const state = tableState[tableId];

            if (tableId === 'estoque' && state.metricFilter) {
                dataset = dataset.filter(row => row[state.metricFilter] > 0);
            }

            const searchInputId = tableId === 'estoque' ? 'searchEstoque' : (tableId === 'historico' ? 'searchHistorico' : 'searchCompras');
            const searchInputEl = document.getElementById(searchInputId);
            const searchInput = searchInputEl ? searchInputEl.value.toLowerCase() : '';

            if (searchInput) {
                dataset = dataset.filter(row => Object.values(row).some(v => String(v).toLowerCase().includes(searchInput)));
            }

            Object.keys(state.filters).forEach(col => {
                const allowedValues = state.filters[col];
                if (allowedValues && allowedValues.length > 0) {
                    dataset = dataset.filter(row => allowedValues.includes(String(row[col])));
                }
            });

            if (state.sortCol) {
                const col = state.sortCol;
                const dir = state.sortDir === 'asc' ? 1 : -1;
                dataset.sort((a, b) => {
                    let valA = col === 'data' ? a._timestamp : a[col];
                    let valB = col === 'data' ? b._timestamp : b[col];

                    if (typeof valA === 'number' && typeof valB === 'number') {
                        return (valA - valB) * dir;
                    }
                    return String(valA).localeCompare(String(valB), 'pt-BR', { numeric: true }) * dir;
                });
            } else if (tableId !== 'estoque') {
                dataset.sort((a, b) => {
                    if (b._timestamp !== a._timestamp) return b._timestamp - a._timestamp;
                    return b._rowIndex - a._rowIndex;
                });
            }

            if (tableId === 'estoque') renderEstoque(dataset);
            else if (tableId === 'historico') renderHistoricoTable(dataset, 'tbodyHistorico');
            else if (tableId === 'compras') renderHistoricoTable(dataset, 'tbodyCompras');

            updateFilterButtonStates(tableId);
        }

        function filterEstoque() { processData('estoque'); }
        function filterHistorico() { processData('historico'); }
        function filterCompras() { processData('compras'); }

        function filterByMetric(metricKey) {
            switchTab('estoque', false);
            const state = tableState.estoque;

            if (state.metricFilter === metricKey || metricKey === 'total') {
                state.metricFilter = null;
            } else {
                state.metricFilter = metricKey;
            }

            const badge = document.getElementById('activeFilterBadge');
            const metricNames = {
                central: 'Central',
                tecnico: 'Técnico Marcelo',
                op: 'Estoque Operacional',
                acervo: 'Acervo Operacional'
            };

            if (state.metricFilter) {
                badge.innerHTML = `<span class="text-indigo-600 font-bold">Filtro ativo:</span> Exibindo apenas a coluna da <strong class="text-slate-800">${metricNames[state.metricFilter]}</strong>`;
            } else {
                badge.innerText = 'Mostrando todas as localizações';
            }

            highlightActiveMetricCard(state.metricFilter);
            processData('estoque');

            const tableElement = document.getElementById('tblEstoque');
            if (tableElement) {
                setTimeout(() => {
                    const headerHeight = document.getElementById('mainHeader')?.offsetHeight || 80;
                    const elementPosition = tableElement.getBoundingClientRect().top;
                    const offsetPosition = elementPosition + window.pageYOffset - headerHeight - 16;

                    window.scrollTo({
                        top: offsetPosition,
                        behavior: 'smooth'
                    });
                }, 50);
            }
        }

        function highlightActiveMetricCard(activeMetric) {
            const cards = ['central', 'tecnico', 'op', 'acervo', 'total'];
            cards.forEach(key => {
                const card = document.getElementById(`kpi-card-${key}`);
                if (!card) return;

                if (activeMetric === key) {
                    card.classList.add('ring-2', 'ring-indigo-500', 'shadow-md');
                } else {
                    card.classList.remove('ring-2', 'ring-indigo-500', 'shadow-md');
                }
            });
        }

        function toggleFilterDropdown(event, tableId, colKey) {
            event.stopPropagation();
            currentActiveContext = { tableId, colKey };

            const dropdown = document.getElementById('excelFilterDropdown');
            const btn = event.currentTarget;
            const rect = btn.getBoundingClientRect();

            dropdown.style.top = `${rect.bottom + window.scrollY + 4}px`;
            dropdown.style.left = `${Math.min(rect.left + window.scrollX, window.innerWidth - 270)}px`;
            dropdown.style.display = 'block';

            let rawData;
            if (tableId === 'estoque') {
                rawData = estoqueData;
            } else if (tableId === 'historico') {
                rawData = historicoData;
            } else if (tableId === 'compras') {
                rawData = historicoData.filter(row => row.origem && row.origem.toString().toLowerCase().includes('fornecedor'));
            }

            const uniqueValues = [...new Set(rawData.map(r => String(r[colKey])))].sort((a, b) => a.localeCompare(b, 'pt-BR', { numeric: true }));

            const selectedFilter = tableState[tableId].filters[colKey];
            const checkboxContainer = document.getElementById('excelCheckboxList');
            checkboxContainer.innerHTML = '';

            document.getElementById('excelSearchBox').value = '';
            document.getElementById('chkSelectAll').checked = !selectedFilter || selectedFilter.length === uniqueValues.length;

            uniqueValues.forEach(val => {
                const isChecked = !selectedFilter || selectedFilter.includes(val);
                const itemDiv = document.createElement('div');
                itemDiv.className = 'excel-filter-item';
                itemDiv.innerHTML = `
                    <input type="checkbox" value="${val}" class="excel-chk-item rounded text-indigo-600 focus:ring-indigo-500" ${isChecked ? 'checked' : ''} onchange="updateSelectAllState()">
                    <span class="truncate">${val === '' ? '(Vazio)' : val}</span>
                `;
                checkboxContainer.appendChild(itemDiv);
            });
        }

        function closeExcelFilterMenu() {
            document.getElementById('excelFilterDropdown').style.display = 'none';
        }

        function closeAllFilterMenus(event) {
            closeExcelFilterMenu();
        }

        function filterExcelCheckboxList() {
            const q = document.getElementById('excelSearchBox').value.toLowerCase();
            const items = document.querySelectorAll('#excelCheckboxList .excel-filter-item');
            items.forEach(it => {
                const txt = it.textContent.toLowerCase();
                it.style.display = txt.includes(q) ? 'flex' : 'none';
            });
        }

        function toggleSelectAllCheckboxes(checked) {
            const chks = document.querySelectorAll('.excel-chk-item');
            chks.forEach(c => {
                if (c.offsetParent !== null) c.checked = checked;
            });
        }

        function updateSelectAllState() {
            const chks = Array.from(document.querySelectorAll('.excel-chk-item'));
            const allChecked = chks.every(c => c.checked);
            document.getElementById('chkSelectAll').checked = allChecked;
        }

        function applySort(dir) {
            const { tableId, colKey } = currentActiveContext;
            tableState[tableId].sortCol = colKey;
            tableState[tableId].sortDir = dir;
            processData(tableId);
            closeExcelFilterMenu();
        }

        function confirmColumnFilter() {
            const { tableId, colKey } = currentActiveContext;
            const chks = document.querySelectorAll('.excel-chk-item');
            const selected = [];

            chks.forEach(c => {
                if (c.checked) selected.push(c.value);
            });

            tableState[tableId].filters[colKey] = selected;
            processData(tableId);
            closeExcelFilterMenu();
        }

        function clearAllFilters(tableId) {
            tableState[tableId].filters = {};
            tableState[tableId].sortCol = null;
            tableState[tableId].sortDir = null;
            if (tableId === 'estoque') {
                tableState.estoque.metricFilter = null;
                highlightActiveMetricCard(null);
                document.getElementById('activeFilterBadge').innerText = 'Mostrando todas as localizações';
                document.getElementById('searchEstoque').value = '';
            } else if (tableId === 'historico') {
                document.getElementById('searchHistorico').value = '';
            } else if (tableId === 'compras') {
                document.getElementById('searchCompras').value = '';
            }
            processData(tableId);
        }

        function updateFilterButtonStates(tableId) {
            const state = tableState[tableId];
            const keys = tableId === 'estoque' 
                ? ['item', 'central', 'tecnico', 'op', 'acervo', 'total'] 
                : ['data', 'item', 'tipo', 'qtd', 'origem', 'destino', 'obs'];

            keys.forEach(k => {
                const btn = document.getElementById(`fbtn-${tableId}-${k}`);
                if (btn) {
                    const hasFilter = state.filters[k] && state.filters[k].length > 0;
                    const hasSort = state.sortCol === k;
                    if (hasFilter || hasSort) {
                        btn.classList.add('active-filter');
                    } else {
                        btn.classList.remove('active-filter');
                    }
                }
            });
        }

        /* TROCA DE ABAS COM ROLAGEM SUAVE (SMOOTH SCROLL) */
        function switchTab(tab, autoScroll = true) {
            const secEstoque = document.getElementById('secEstoque');
            const secHistorico = document.getElementById('secHistorico');
            const secCompras = document.getElementById('secCompras');

            const btnEstoque = document.getElementById('btnTabEstoque');
            const btnHistorico = document.getElementById('btnTabHistorico');
            const btnCompras = document.getElementById('btnTabCompras');

            const activeClass = 'px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-200 bg-indigo-600 text-white shadow-md flex items-center gap-2';
            const inactiveClass = 'px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';

            secEstoque.classList.add('hidden');
            secHistorico.classList.add('hidden');
            secCompras.classList.add('hidden');

            btnEstoque.className = inactiveClass;
            btnHistorico.className = inactiveClass;
            btnCompras.className = inactiveClass;

            let targetSec = null;

            if (tab === 'estoque') {
                secEstoque.classList.remove('hidden');
                btnEstoque.className = activeClass;
                targetSec = secEstoque;
            } else if (tab === 'historico') {
                secHistorico.classList.remove('hidden');
                btnHistorico.className = activeClass;
                targetSec = secHistorico;
            } else if (tab === 'compras') {
                secCompras.classList.remove('hidden');
                btnCompras.className = activeClass;
                targetSec = secCompras;
            }

            if (autoScroll && targetSec) {
                setTimeout(() => {
                    const headerHeight = document.getElementById('mainHeader')?.offsetHeight || 80;
                    const elementPosition = targetSec.getBoundingClientRect().top;
                    const offsetPosition = elementPosition + window.pageYOffset - headerHeight - 16;

                    window.scrollTo({
                        top: offsetPosition,
                        behavior: 'smooth'
                    });
                }, 50);
            }
        }

        async function syncData() {
            const btn = document.getElementById('btnSync');
            const icon = document.getElementById('iconSync');
            const lastUpdateText = document.getElementById('lastUpdateText');

            if (icon) icon.classList.add('fa-spin');
            if (btn) btn.disabled = true;

            await loadDataFromSheet();

            const now = new Date();
            const dia = String(now.getDate()).padStart(2, '0');
            const mes = String(now.getMonth() + 1).padStart(2, '0');
            const ano = now.getFullYear();
            const horas = String(now.getHours()).padStart(2, '0');
            const minutos = String(now.getMinutes()).padStart(2, '0');

            if (lastUpdateText) {
                lastUpdateText.innerText = `Última atualização: ${dia}/${mes}/${ano} às ${horas}:${minutos}`;
            }

            if (icon) icon.classList.remove('fa-spin');
            if (btn) btn.disabled = false;
            showToast('Dados sincronizados com sucesso!');
        }

        document.addEventListener('DOMContentLoaded', () => {
            checkAuth();
            loadDataFromSheet();
        });
    </script>
</body>
</html>
