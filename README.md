<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque - Grupo Forte Protege</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Ícones -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    
    <style>
        body {
            background-color: #0b0f19 !important;
            color: #f1f5f9 !important;
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        .card-panel {
            background-color: #111827 !important;
            border: 1px solid #1f2937 !important;
        }

        /* TABELA COM ALTO CONTRASTE - TEXTO PRETO */
        table.custom-table {
            background-color: #ffffff !important;
            width: 100%;
            border-collapse: collapse;
        }

        table.custom-table th {
            background-color: #1e293b !important;
            color: #ffffff !important;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            padding: 0.75rem 0.5rem;
            border: 1px solid #334155 !important;
            user-select: none;
        }

        table.custom-table td {
            background-color: #ffffff !important;
            color: #000000 !important;
            font-weight: 600 !important;
            font-size: 0.875rem !important;
            padding: 0.65rem 0.85rem;
            border: 1px solid #e2e8f0 !important;
        }

        table.custom-table tr:hover td {
            background-color: #f1f5f9 !important;
        }

        .excel-filter-menu {
            position: absolute;
            z-index: 100;
            background-color: #ffffff;
            color: #1e293b;
            border: 1px solid #cbd5e1;
            border-radius: 8px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5);
            width: 250px;
            font-size: 0.8rem;
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
            color: #2563eb;
        }

        .excel-filter-list {
            max-height: 160px;
            overflow-y: auto;
            border: 1px solid #e2e8f0;
            border-radius: 4px;
            background-color: #f8fafc;
            padding: 4px;
        }

        .excel-filter-item {
            display: flex;
            align-items: center;
            gap: 6px;
            padding: 3px 6px;
            cursor: pointer;
            border-radius: 3px;
            font-weight: 500;
        }

        .excel-filter-item:hover {
            background-color: #e2e8f0;
        }

        .th-container {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 4px;
        }

        .filter-btn {
            background-color: #334155;
            color: #cbd5e1;
            border-radius: 4px;
            padding: 2px 6px;
            cursor: pointer;
            font-size: 0.65rem;
            transition: all 0.2s;
        }

        .filter-btn:hover {
            background-color: #2563eb;
            color: #ffffff;
        }

        .filter-btn.active-filter {
            background-color: #10b981 !important;
            color: #ffffff !important;
        }

        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #94a3b8; border-radius: 3px; }
    </style>
</head>
<body class="min-h-screen flex flex-col antialiased relative" onclick="closeAllFilterMenus(event)">

    <!-- TELA DE LOGIN OVERLAY -->
    <div id="loginScreen" class="fixed inset-0 z-50 bg-slate-950/95 backdrop-blur-md flex items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl shadow-2xl p-8 max-w-md w-full text-center space-y-6">
            <div class="flex justify-center mb-2">
                <img src="logo.png" alt="Grupo Forte Protege" class="h-16 w-auto object-contain max-w-[200px]" onerror="this.onerror=null; this.src='https://via.placeholder.com/200x60/111827/38bdf8?text=GF+PROTEGE'">
            </div>

            <div>
                <h2 class="text-xl font-bold text-white tracking-tight">Controle de Estoque</h2>
                <p class="text-xs text-slate-400 mt-1">Informe a senha corporativa para acessar o painel</p>
            </div>

            <form onsubmit="handleLogin(event)" class="space-y-4 text-left">
                <div>
                    <label for="inputPassword" class="block text-xs font-semibold uppercase text-slate-400 mb-1">Senha de Acesso</label>
                    <div class="relative">
                        <input type="password" id="inputPassword" placeholder="••••••••••••" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-sm text-white focus:outline-none focus:border-blue-500 transition pr-10">
                        <button type="button" onclick="togglePasswordVisibility()" class="absolute right-3 top-3.5 text-slate-500 hover:text-slate-300">
                            <i id="eyeIcon" class="fa-solid fa-eye"></i>
                        </button>
                    </div>
                    <p id="loginError" class="text-xs text-rose-500 mt-2 hidden font-semibold flex items-center gap-1">
                        <i class="fa-solid fa-circle-exclamation"></i> Senha incorreta. Tente novamente.
                    </p>
                </div>

                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-500 active:bg-blue-700 text-white font-bold py-3 px-4 rounded-xl text-sm transition shadow-lg shadow-blue-600/20 flex items-center justify-center gap-2">
                    <i class="fa-solid fa-right-to-bracket"></i> Entrar no Sistema
                </button>
            </form>

            <div class="text-[11px] text-slate-600 border-t border-slate-800/80 pt-4">
                Grupo Forte Protege &copy; 2026 — Acesso Seguro
            </div>
        </div>
    </div>

    <!-- MODAL DE EDIÇÃO DE ESTOQUE -->
    <div id="modalEditEstoque" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-4 hidden">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl shadow-2xl p-6 max-w-lg w-full space-y-4">
            <div class="flex justify-between items-center border-b border-slate-800 pb-3">
                <h3 class="text-base font-bold text-white flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square text-blue-500"></i> Editar Quantidades
                </h3>
                <button onclick="closeEditModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>

            <form onsubmit="saveEstoqueEdit(event)" class="space-y-4">
                <input type="hidden" id="editRowIndex">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Equipamento</label>
                    <input type="text" id="editItemName" readonly class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-slate-300 font-bold cursor-not-allowed">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Central</label>
                        <input type="number" id="editCentral" min="0" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Técnico Marcelo</label>
                        <input type="number" id="editTecnico" min="0" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Estoque Op.</label>
                        <input type="number" id="editOp" min="0" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Acervo Op.</label>
                        <input type="number" id="editAcervo" min="0" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                </div>

                <div class="flex justify-end gap-2 pt-2 border-t border-slate-800">
                    <button type="button" onclick="closeEditModal()" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-xs font-semibold rounded-lg text-slate-300">Cancelar</button>
                    <button type="submit" id="btnSaveEdit" class="px-4 py-2 bg-blue-600 hover:bg-blue-500 text-xs font-bold rounded-lg text-white flex items-center gap-2">
                        <i class="fa-solid fa-floppy-disk"></i> Salvar Alterações
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL DE NOVA MOVIMENTAÇÃO (HISTÓRICO) -->
    <div id="modalMovimentacao" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-4 hidden">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl shadow-2xl p-6 max-w-lg w-full space-y-4">
            <div class="flex justify-between items-center border-b border-slate-800 pb-3">
                <h3 class="text-base font-bold text-white flex items-center gap-2">
                    <i class="fa-solid fa-right-left text-emerald-500"></i> Registrar Nova Movimentação
                </h3>
                <button onclick="closeMovimentacaoModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>

            <form onsubmit="saveMovimentacao(event)" class="space-y-3">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Equipamento</label>
                    <select id="movEquipamento" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                        <option value="">Selecione um equipamento...</option>
                    </select>
                </div>

                <!-- LINHA COM DATA, TIPO DE OPERAÇÃO E QUANTIDADE -->
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Data</label>
                        <input type="date" id="movData" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Tipo de Operação</label>
                        <select id="movTipo" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                            <option value="Entrada">Entrada</option>
                            <option value="Saída">Saída</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Quantidade</label>
                        <input type="number" id="movQtd" min="1" value="1" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Origem</label>
                        <select id="movOrigem" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                            <option value="">Selecione a origem...</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Destino</label>
                        <select id="movDestino" required class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                            <option value="">Selecione o destino...</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Observações / Projeto</label>
                    <input type="text" id="movObs" placeholder="Ex: Instalação Cliente X" class="w-full bg-slate-950 border border-slate-800 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-blue-500">
                </div>

                <div class="flex justify-end gap-2 pt-2 border-t border-slate-800">
                    <button type="button" onclick="closeMovimentacaoModal()" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-xs font-semibold rounded-lg text-slate-300">Cancelar</button>
                    <button type="submit" id="btnSaveMov" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-xs font-bold rounded-lg text-white flex items-center gap-2">
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

    <!-- CABEÇALHO FIXO -->
    <header id="mainHeader" class="bg-slate-900 border-b border-slate-800 sticky top-0 z-40 shadow-xl">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col lg:flex-row items-center justify-between gap-4">
            <div class="flex items-center gap-4 w-full lg:w-auto justify-between lg:justify-start">
                <div class="flex items-center gap-3">
                    <img src="logo.png" alt="Grupo Forte Protege" class="h-10 w-auto object-contain max-w-[150px]" onerror="this.onerror=null; this.src='https://via.placeholder.com/150x40/111827/38bdf8?text=GF+PROTEGE'">
                    <div class="h-7 w-px bg-slate-700 hidden sm:block"></div>
                    <div>
                        <h1 class="text-lg font-bold text-white tracking-tight">Estoque & Operações</h1>
                        <p class="text-[11px] text-slate-400 font-medium">TI • CFTV • Conectividade</p>
                    </div>
                </div>
            </div>

            <div class="flex flex-wrap items-center justify-center lg:justify-end gap-3 w-full lg:w-auto">
                <button onclick="openMovimentacaoModal()" class="flex items-center gap-2 bg-emerald-600 hover:bg-emerald-500 text-white px-3 py-1.5 rounded-lg text-xs font-bold transition shadow-md">
                    <i class="fa-solid fa-plus"></i>
                    <span>Nova Movimentação</span>
                </button>

                <div class="bg-slate-950 border border-slate-800 px-3 py-1.5 rounded-lg text-xs text-slate-400 flex items-center gap-2">
                    <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span>Atualizado: <strong id="lastUpdateText" class="text-slate-200">Em tempo real</strong></span>
                </div>

                <button onclick="syncData()" id="btnSync" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 active:bg-slate-600 text-slate-200 border border-slate-700 px-3 py-1.5 rounded-lg text-xs font-semibold transition shadow-sm">
                    <i id="iconSync" class="fa-solid fa-rotate text-blue-400"></i>
                    <span>Sincronizar</span>
                </button>

                <div class="flex items-center bg-slate-950 p-1 rounded-lg border border-slate-800">
                    <button onclick="switchTab('estoque')" id="btnTabEstoque" class="px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2">
                        <i class="fa-solid fa-boxes-stacked"></i> Estoque
                    </button>
                    <button onclick="switchTab('historico')" id="btnTabHistorico" class="px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left"></i> Histórico
                    </button>
                </div>

                <button onclick="handleLogout()" title="Sair da sessão" class="px-2.5 py-1.5 bg-slate-800 hover:bg-rose-900/50 hover:text-rose-400 text-slate-400 rounded-lg text-xs font-semibold border border-slate-700 transition">
                    <i class="fa-solid fa-right-from-bracket"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- CONTEÚDO PRINCIPAL -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-1 w-full space-y-6">

        <!-- CARDS DE MÉTRICAS CLICÁVEIS (KPIs) -->
        <section class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 gap-4">
            <div onclick="filterByMetric('central')" id="kpi-card-central" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-blue-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-blue-400">
                    <span>Central</span>
                    <i class="fa-solid fa-warehouse text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2" id="kpiCentral">0</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <div onclick="filterByMetric('tecnico')" id="kpi-card-tecnico" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-amber-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-amber-400">
                    <span>Técnico Marcelo</span>
                    <i class="fa-solid fa-user-gear text-amber-400"></i>
                </div>
                <div class="text-2xl font-bold text-amber-400 mt-2" id="kpiTecnico">0</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <div onclick="filterByMetric('op')" id="kpi-card-op" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-emerald-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-emerald-400">
                    <span>Estoque Op.</span>
                    <i class="fa-solid fa-truck-ramp-box text-emerald-400"></i>
                </div>
                <div class="text-2xl font-bold text-emerald-400 mt-2" id="kpiOperacional">0</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <div onclick="filterByMetric('acervo')" id="kpi-card-acervo" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-purple-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-purple-400">
                    <span>Acervo</span>
                    <i class="fa-solid fa-laptop text-purple-400"></i>
                </div>
                <div class="text-2xl font-bold text-purple-400 mt-2" id="kpiAcervo">0</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <div onclick="filterByMetric('total')" id="kpi-card-total" class="col-span-2 sm:col-span-1 card-panel p-4 rounded-xl shadow-sm bg-gradient-to-br from-slate-900 to-blue-950 border-blue-900/50 cursor-pointer hover:border-blue-400 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-blue-300">
                    <span>Total Físico</span>
                    <i class="fa-solid fa-cubes text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-blue-300 mt-2" id="kpiTotal">0</div>
                <span class="text-[11px] text-blue-200/70">Exibir todas as colunas</span>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="card-panel rounded-xl shadow-lg overflow-hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-base font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-blue-500"></i> Disponibilidade por Equipamento
                    </h2>
                    <p class="text-xs text-slate-400" id="activeFilterBadge">Mostrando todas as localizações</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('estoque')" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-300 rounded-lg border border-slate-700 transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-400 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                        <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Pesquisa rápida..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblEstoque">
                    <thead>
                        <tr id="theadEstoqueTr"></tr>
                    </thead>
                    <tbody id="tbodyEstoque"></tbody>
                </table>
            </div>
        </section>

        <!-- ABA 2: HISTÓRICO DE MOVIMENTAÇÕES -->
        <section id="secHistorico" class="card-panel rounded-xl shadow-lg overflow-hidden hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-base font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-arrow-right-arrow-left text-blue-500"></i> Histórico de Entradas e Saídas
                    </h2>
                    <p class="text-xs text-slate-400">Filtre por datas, origem, destino ou projetos diretamente nas colunas</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('historico')" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-300 rounded-lg border border-slate-700 transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-400 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Pesquisa rápida..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
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
                    <tbody id="tbodyHistorico"></tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- MENU ESTILO EXCEL -->
    <div id="excelFilterDropdown" class="excel-filter-menu" onclick="event.stopPropagation()">
        <div class="filter-option border-b border-slate-100" onclick="applySort('asc')">
            <i class="fa-solid fa-arrow-down-a-z text-blue-600"></i> Classificar de A a Z
        </div>
        <div class="filter-option border-b border-slate-200" onclick="applySort('desc')">
            <i class="fa-solid fa-arrow-up-z-a text-blue-600"></i> Classificar de Z a A
        </div>

        <div class="p-2 border-b border-slate-200">
            <input type="text" id="excelSearchBox" oninput="filterExcelCheckboxList()" placeholder="Pesquisar itens..." class="w-full bg-slate-100 border border-slate-300 rounded px-2 py-1 text-xs focus:outline-none focus:border-blue-500">
        </div>

        <div class="p-2">
            <div class="excel-filter-item font-bold border-b border-slate-200 pb-1 mb-1">
                <input type="checkbox" id="chkSelectAll" onchange="toggleSelectAllCheckboxes(this.checked)" checked>
                <label for="chkSelectAll">(Selecionar Tudo)</label>
            </div>
            <div id="excelCheckboxList" class="excel-filter-list"></div>
        </div>

        <div class="p-2 bg-slate-50 border-t border-slate-200 flex justify-end gap-2 rounded-b-8 shadow-inner">
            <button onclick="confirmColumnFilter()" class="px-3 py-1 bg-blue-600 text-white rounded font-semibold hover:bg-blue-700 text-xs">OK</button>
            <button onclick="closeExcelFilterMenu()" class="px-3 py-1 bg-slate-200 text-slate-700 rounded font-semibold hover:bg-slate-300 text-xs">Cancelar</button>
        </div>
    </div>

    <!-- RODAPÉ -->
    <footer class="bg-slate-900 border-t border-slate-800 py-3 text-center text-xs text-slate-500">
        Grupo Forte Protege &copy; 2026 — Controle Interno Operacional
    </footer>

    <!-- SCRIPT DE INTEGRAÇÃO HÍBRIDA -->
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

        function formatDateBR(dateVal) {
            if (!dateVal || dateVal === '-') return '-';
            let str = dateVal.toString().trim();
            if (str.includes('T')) str = str.split('T')[0];

            if (str.includes('/')) {
                const parts = str.split('/');
                if (parts.length === 3) {
                    let p1 = parseInt(parts[0], 10);
                    let p2 = parseInt(parts[1], 10);
                    let p3 = parts[2].trim();

                    if (parts[0].length === 4) return `${parts[2].padStart(2, '0')}/${parts[1].padStart(2, '0')}/${parts[0]}`;
                    if (p1 <= 12 && p2 <= 31) return `${String(p2).padStart(2, '0')}/${String(p1).padStart(2, '0')}/${p3}`;
                    else if (p1 > 12) return `${String(p1).padStart(2, '0')}/${String(p2).padStart(2, '0')}/${p3}`;
                }
            }

            if (str.includes('-')) {
                const parts = str.split('-');
                if (parts.length === 3 && parts[0].length === 4) return `${parts[2].padStart(2, '0')}/${parts[1].padStart(2, '0')}/${parts[0]}`;
            }

            return str;
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
                const rawEstoque = await fetchGoogleSheetCSV(TAB_ESTOQUE_NAME);
                estoqueData = [];

                rawEstoque.forEach(r => {
                    const item = r['equipamento'] || r['item'] || r['descricao'] || r['nome'] || '';
                    const itemLower = item.toString().toLowerCase().trim();

                    if (!item || itemLower.includes('total') || itemLower === 'total geral' || itemLower === 'subtotal') {
                        return;
                    }

                    const central = Number(r['central']) || 0;
                    const tecnico = Number(r['tecnicomarcelo'] || r['tecnico'] || r['marcelo']) || 0;
                    const op = Number(r['estoqueop'] || r['op'] || r['estoqueoperacional']) || 0;
                    const acervo = Number(r['acervoop'] || r['acervo'] || r['acervooperacional']) || 0;
                    
                    const totalGeral = Number(r['totalgeral'] || r['total']) || (central + tecnico + op + acervo);

                    estoqueData.push({
                        _rowIndex: r._rowIndex,
                        item: item,
                        central,
                        tecnico,
                        op,
                        acervo,
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

                    historicoData.push({
                        data: formatDateBR(r['data'] || r['datahora'] || '-'),
                        item: item || '-',
                        tipo: r['entradasaida'] || r['tipo'] || r['operacao'] || '-',
                        qtd: Number(r['quantidade'] || r['qtd']) || 0,
                        origem: r['origem'] || '-',
                        destino: r['destino'] || '-',
                        obs: r['observacoes'] || r['observacao'] || r['obs'] || r['projeto'] || '-'
                    });
                });

                updateKPICards();
                processData('estoque');
                processData('historico');
                populateMovimentacaoOptions();

            } catch (err) {
                console.error('Erro ao conectar com o Google Sheets:', err);
            }
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

        function openEditModal(rowIndex) {
            const item = estoqueData.find(i => i._rowIndex === rowIndex);
            if (!item) return;

            document.getElementById('editRowIndex').value = item._rowIndex;
            document.getElementById('editItemName').value = item.item;
            document.getElementById('editCentral').value = item.central;
            document.getElementById('editTecnico').value = item.tecnico;
            document.getElementById('editOp').value = item.op;
            document.getElementById('editAcervo').value = item.acervo;

            document.getElementById('modalEditEstoque').classList.remove('hidden');
        }

        function closeEditModal() {
            document.getElementById('modalEditEstoque').classList.add('hidden');
        }

        async function saveEstoqueEdit(e) {
            e.preventDefault();
            const btn = document.getElementById('btnSaveEdit');
            btn.disabled = true;
            btn.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Salvando...`;

            const payload = {
                rowIndex: parseInt(document.getElementById('editRowIndex').value, 10),
                central: parseInt(document.getElementById('editCentral').value, 10),
                tecnico: parseInt(document.getElementById('editTecnico').value, 10),
                op: parseInt(document.getElementById('editOp').value, 10),
                acervo: parseInt(document.getElementById('editAcervo').value, 10)
            };

            try {
                await fetch(WEB_APP_URL, {
                    method: 'POST',
                    mode: 'no-cors',
                    headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                    body: JSON.stringify({ action: 'updateEstoque', payload })
                });

                showToast('Saldo atualizado na planilha!');
                closeEditModal();
                setTimeout(() => { loadDataFromSheet(); }, 1200);
            } catch (err) {
                alert('Erro ao salvar alterações no estoque.');
            } finally {
                btn.disabled = false;
                btn.innerHTML = `<i class="fa-solid fa-floppy-disk"></i> Salvar Alterações`;
            }
        }

        function openMovimentacaoModal() {
            populateMovimentacaoOptions();
            
            // PREENCHE A DATA ATUAL NO FORMATO DO INPUT DATE (YYYY-MM-DD)
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

            const defaultLocations = ["Central", "Técnico Marcelo", "Estoque Op.", "Acervo Op.", "Cliente", "Fornecedor", "Levantamento"];

            const origensUnicas = [...new Set([...defaultLocations, ...historicoData.map(h => h.origem)])].filter(v => v && v !== '-').sort((a, b) => a.localeCompare(b, 'pt-BR'));
            const destinosUnicos = [...new Set([...defaultLocations, ...historicoData.map(h => h.destino)])].filter(v => v && v !== '-').sort((a, b) => a.localeCompare(b, 'pt-BR'));

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

        async function saveMovimentacao(e) {
            e.preventDefault();
            const btn = document.getElementById('btnSaveMov');
            btn.disabled = true;
            btn.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Registrando...`;

            // CONVERTE DATA DE YYYY-MM-DD PARA DD/MM/YYYY
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

                showToast('Movimentação registrada com sucesso!');
                closeMovimentacaoModal();
                setTimeout(() => { loadDataFromSheet(); }, 1200);
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
            historico: { filters: {}, sortCol: null, sortDir: null }
        };

        let currentActiveContext = { tableId: null, colKey: null };

        const estoqueColsDef = [
            { key: 'item', label: 'Equipamento', align: 'text-left', class: 'font-bold text-black' },
            { key: 'central', label: 'Central', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'tecnico', label: 'Técnico Marcelo', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'op', label: 'Estoque Op.', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'acervo', label: 'Acervo Op.', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'total', label: 'Total Geral', align: 'text-center', class: 'text-center font-bold text-blue-600 text-base' },
            { key: 'actions', label: 'Ações', align: 'text-center', class: 'text-center font-bold' }
        ];

        function renderEstoque(data) {
            const theadTr = document.getElementById('theadEstoqueTr');
            const tbody = document.getElementById('tbodyEstoque');
            const metricFilter = tableState.estoque.metricFilter;

            const activeCols = estoqueColsDef.filter(col => {
                if (col.key === 'item' || col.key === 'total' || col.key === 'actions') return true;
                if (!metricFilter) return true; 
                return col.key === metricFilter; 
            });

            theadTr.innerHTML = activeCols.map(col => `
                <th class="${col.align}">
                    <div class="th-container ${col.align === 'text-center' ? 'justify-center' : ''}">
                        <span>${col.label}</span>
                        ${col.key !== 'actions' ? `<button class="filter-btn" id="fbtn-estoque-${col.key}" onclick="toggleFilterDropdown(event, 'estoque', '${col.key}')"><i class="fa-solid fa-filter"></i></button>` : ''}
                    </div>
                </th>
            `).join('');

            tbody.innerHTML = '';
            
            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="${activeCols.length}" class="p-6 text-center text-slate-500">Nenhum equipamento encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.innerHTML = activeCols.map(col => {
                    if (col.key === 'actions') {
                        return `<td class="text-center">
                            <button onclick="openEditModal(${row._rowIndex})" title="Editar quantidades" class="px-2.5 py-1 bg-slate-800 hover:bg-blue-600 text-slate-300 hover:text-white rounded text-xs transition border border-slate-700">
                                <i class="fa-solid fa-pen-to-square mr-1"></i> Editar
                            </button>
                        </td>`;
                    }
                    let val = row[col.key];
                    if (val === 0 || val === null || val === undefined) val = '-';
                    return `<td class="${col.class}">${val}</td>`;
                }).join('');
                tbody.appendChild(tr);
            });
        }

        function renderHistorico(data) {
            const tbody = document.getElementById('tbodyHistorico');
            tbody.innerHTML = '';

            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-6 text-center text-slate-500">Nenhum registro encontrado no Histórico.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const badgeClass = row.tipo === 'Entrada' 
                    ? 'bg-emerald-100 text-emerald-800 border-emerald-300' 
                    : 'bg-rose-100 text-rose-800 border-rose-300';
                
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="font-bold text-black font-mono text-xs whitespace-nowrap">${row.data}</td>
                    <td class="font-bold text-black">${row.item}</td>
                    <td class="text-center">
                        <span class="px-2 py-0.5 text-xs rounded border ${badgeClass} font-bold">${row.tipo}</span>
                    </td>
                    <td class="text-center font-bold text-black">${row.qtd}</td>
                    <td class="font-bold text-black">${row.origem}</td>
                    <td class="font-bold text-black">${row.destino}</td>
                    <td class="font-bold text-black italic">${row.obs || '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function processData(tableId) {
            const isEstoque = tableId === 'estoque';
            let dataset = isEstoque ? [...estoqueData] : [...historicoData];
            const state = tableState[tableId];

            if (isEstoque && state.metricFilter) {
                dataset = dataset.filter(row => row[state.metricFilter] > 0);
            }

            const searchInput = document.getElementById(isEstoque ? 'searchEstoque' : 'searchHistorico').value.toLowerCase();
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
                    let valA = a[col];
                    let valB = b[col];

                    if (typeof valA === 'number' && typeof valB === 'number') {
                        return (valA - valB) * dir;
                    }
                    return String(valA).localeCompare(String(valB), 'pt-BR', { numeric: true }) * dir;
                });
            }

            if (isEstoque) renderEstoque(dataset);
            else renderHistorico(dataset);

            updateFilterButtonStates(tableId);
        }

        function filterEstoque() { processData('estoque'); }
        function filterHistorico() { processData('historico'); }

        function filterByMetric(metricKey) {
            switchTab('estoque');
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
                badge.innerHTML = `<span class="text-blue-400 font-bold">Filtro ativo:</span> Exibindo apenas a coluna da <strong class="text-white">${metricNames[state.metricFilter]}</strong>`;
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
                    card.classList.add('border-blue-500', 'bg-slate-800/90', 'ring-2', 'ring-blue-500/50');
                } else {
                    card.classList.remove('border-blue-500', 'bg-slate-800/90', 'ring-2', 'ring-blue-500/50');
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
            dropdown.style.left = `${Math.min(rect.left + window.scrollX, window.innerWidth - 260)}px`;
            dropdown.style.display = 'block';

            const isEstoque = tableId === 'estoque';
            const rawData = isEstoque ? estoqueData : historicoData;
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
                    <input type="checkbox" value="${val}" class="excel-chk-item" ${isChecked ? 'checked' : ''} onchange="updateSelectAllState()">
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
            }
            document.getElementById(tableId === 'estoque' ? 'searchEstoque' : 'searchHistorico').value = '';
            processData(tableId);
        }

        function updateFilterButtonStates(tableId) {
            const state = tableState[tableId];
            const isEstoque = tableId === 'estoque';
            const keys = isEstoque 
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

        function switchTab(tab) {
            const secEstoque = document.getElementById('secEstoque');
            const secHistorico = document.getElementById('secHistorico');
            const btnEstoque = document.getElementById('btnTabEstoque');
            const btnHistorico = document.getElementById('btnTabHistorico');

            if (tab === 'estoque') {
                secEstoque.classList.remove('hidden');
                secHistorico.classList.add('hidden');
                btnEstoque.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnHistorico.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            } else {
                secEstoque.classList.add('hidden');
                secHistorico.classList.remove('hidden');
                btnHistorico.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnEstoque.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            }
        }

        function syncData() {
            const btn = document.getElementById('btnSync');
            const icon = document.getElementById('iconSync');
            const lastUpdateText = document.getElementById('lastUpdateText');

            icon.classList.add('fa-spin');
            btn.disabled = true;

            loadDataFromSheet().then(() => {
                const now = new Date();
                const dia = String(now.getDate()).padStart(2, '0');
                const mes = String(now.getMonth() + 1).padStart(2, '0');
                const ano = now.getFullYear();
                const horas = String(now.getHours()).padStart(2, '0');
                const minutos = String(now.getMinutes()).padStart(2, '0');

                lastUpdateText.innerText = `${dia}/${mes}/${ano} ${horas}:${minutos}`;

                icon.classList.remove('fa-spin');
                btn.disabled = false;
                showToast('Dados sincronizados!');
            });
        }

        document.addEventListener('DOMContentLoaded', () => {
            checkAuth();
            loadDataFromSheet();
        });
    </script>
</body>
</html>
