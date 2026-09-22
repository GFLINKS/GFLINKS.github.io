<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Controle de Estoque & Movimentações - Grupo Forte Protege</title>
    <!-- Tailwind CSS CDN para layout moderno -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome para ícones -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
</head>
<body class="bg-slate-900 text-slate-100 font-sans min-h-screen">

    <!-- Cabeçalho -->
    <header class="bg-slate-800 border-b border-slate-700 p-6 sticky top-0 z-50 shadow-lg">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-blue-400 flex items-center gap-2">
                    <i class="fa-solid fa-boxes-stacked"></i> Controle de Estoque & Operações
                </h1>
                <p class="text-xs text-slate-400 mt-1">Grupo Forte Protege — TI, CFTV e Conectividade</p>
            </div>
            <div class="flex gap-2">
                <button onclick="switchTab('estoque')" id="btnTabEstoque" class="px-4 py-2 rounded-lg text-sm font-semibold transition bg-blue-600 text-white shadow">
                    <i class="fa-solid fa-list mr-1"></i> Saldo de Estoque
                </button>
                <button onclick="switchTab('historico')" id="btnTabHistorico" class="px-4 py-2 rounded-lg text-sm font-semibold transition bg-slate-700 text-slate-300 hover:bg-slate-600">
                    <i class="fa-solid fa-clock-rotate-left mr-1"></i> Histórico de Movimentações
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-6 space-y-6">

        <!-- Cards de Métricas e KPIs (Consolidado) -->
        <section class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">
            <div class="bg-slate-800 border border-slate-700 p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Central</span>
                    <i class="fa-solid fa-warehouse text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2">96</div>
                <span class="text-xs text-slate-500">Equipamentos no Galpão</span>
            </div>

            <div class="bg-slate-800 border border-slate-700 p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Técnico Marcelo</span>
                    <i class="fa-solid fa-user-gear text-amber-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2">45</div>
                <span class="text-xs text-slate-500">Em posse para campo</span>
            </div>

            <div class="bg-slate-800 border border-slate-700 p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Estoque Operacional</span>
                    <i class="fa-solid fa-truck-ramp-box text-emerald-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2">15</div>
                <span class="text-xs text-slate-500">Pronta entrega</span>
            </div>

            <div class="bg-slate-800 border border-slate-700 p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Acervo Operacional</span>
                    <i class="fa-solid fa-laptop text-purple-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2">5</div>
                <span class="text-xs text-slate-500">Ativos corporativos</span>
            </div>

            <div class="bg-slate-800 border border-slate-700 p-4 rounded-xl shadow-sm bg-gradient-to-br from-slate-800 to-slate-800">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Total Físico</span>
                    <i class="fa-solid fa-cubes text-indigo-400"></i>
                </div>
                <div class="text-2xl font-bold text-indigo-300 mt-2">161</div>
                <span class="text-xs text-slate-500">Itens rastreados</span>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="bg-slate-800 border border-slate-700 rounded-xl p-6 shadow-md">
            <div class="flex flex-col sm:flex-row justify-between items-center gap-4 mb-6">
                <h2 class="text-lg font-bold text-white flex items-center gap-2">
                    <i class="fa-solid fa-box text-blue-400"></i> Saldo por Equipamento
                </h2>
                <div class="relative w-full sm:w-72">
                    <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-slate-400 text-sm"></i>
                    <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Buscar equipamento..." class="w-full bg-slate-900 border border-slate-700 rounded-lg pl-9 pr-4 py-2 text-sm text-white focus:outline-none focus:border-blue-500">
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left text-sm text-slate-300">
                    <thead class="bg-slate-900/60 text-slate-400 uppercase text-xs font-semibold">
                        <tr>
                            <th class="p-3 rounded-l-lg">Equipamento</th>
                            <th class="p-3 text-center">Central</th>
                            <th class="p-3 text-center">Técnico Marcelo</th>
                            <th class="p-3 text-center">Estoque Operacional</th>
                            <th class="p-3 text-center">Acervo Operacional</th>
                            <th class="p-3 text-center rounded-r-lg">Total Consolidado</th>
                        </tr>
                    </thead>
                    <tbody id="tbodyEstoque" class="divide-y divide-slate-700/50">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- ABA 2: HISTÓRICO DE MOVIMENTAÇÕES -->
        <section id="secHistorico" class="bg-slate-800 border border-slate-700 rounded-xl p-6 shadow-md hidden">
            <div class="flex flex-col sm:flex-row justify-between items-center gap-4 mb-6">
                <h2 class="text-lg font-bold text-white flex items-center gap-2">
                    <i class="fa-solid fa-arrow-right-arrow-left text-blue-400"></i> Registro de Entradas e Saídas
                </h2>
                <div class="flex flex-col sm:flex-row gap-2 w-full sm:w-auto">
                    <select id="filterTipo" onchange="filterHistorico()" class="bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white focus:outline-none focus:border-blue-500">
                        <option value="todos">Todos os Tipos</option>
                        <option value="Entrada">Entradas</option>
                        <option value="Saída">Saídas</option>
                    </select>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-slate-400 text-sm"></i>
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Buscar origem, destino, OS..." class="w-full bg-slate-900 border border-slate-700 rounded-lg pl-9 pr-4 py-2 text-sm text-white focus:outline-none focus:border-blue-500">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left text-sm text-slate-300">
                    <thead class="bg-slate-900/60 text-slate-400 uppercase text-xs font-semibold">
                        <tr>
                            <th class="p-3 rounded-l-lg">Data</th>
                            <th class="p-3">Equipamento</th>
                            <th class="p-3 text-center">Tipo</th>
                            <th class="p-3 text-center">Qtd</th>
                            <th class="p-3">Origem</th>
                            <th class="p-3">Destino</th>
                            <th class="p-3 rounded-r-lg">Observações / Projeto</th>
                        </tr>
                    </thead>
                    <tbody id="tbodyHistorico" class="divide-y divide-slate-700/50">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- Scripts de Dados e Comportamento -->
    <script>
        // Dados sincronizados com a planilha ESTOQUE
        const estoqueData = [
            { item: "Mikrotik Hap", central: 0, tecnico: 17, op: 5, acervo: 0, total: 22 },
            { item: "Mikrotik LTE6", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Antena Elsys", central: 0, tecnico: 2, op: 1, acervo: 0, total: 3 },
            { item: "Roteador Intelbras 5G", central: 0, tecnico: 8, op: 8, acervo: 0, total: 16 },
            { item: "Suporte Monitor", central: 0, tecnico: 0, op: 1, acervo: 0, total: 1 },
            { item: "Monitor Mnbox", central: 8, tecnico: 0, op: 0, acervo: 1, total: 9 },
            { item: "HD 4TB", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Notebook Dell i5 11th Gen", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Celular Realme Note 50", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Nobreak 1200VA", central: 0, tecnico: 1, op: 0, acervo: 0, total: 1 },
            { item: "Régua Intelbras", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Antena Hikvision", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Switch Intelbras 8 Portas (SF 1008 F)", central: 6, tecnico: 0, op: 0, acervo: 0, total: 6 },
            { item: "Switch Intelbras 8P PoE (SF 1108 F-P)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Telefone IP Intelbras (TIP 125 I)", central: 2, tecnico: 0, op: 0, acervo: 0, total: 2 },
            { item: "Terminal Interfonia IP (TDMI 400 IP)", central: 8, tecnico: 0, op: 0, acervo: 0, total: 8 },
            { item: "Cabo HDMI High Speed (1m)", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Câmera IP Dome Full HD (VIP 1230 D G2)", central: 10, tecnico: 2, op: 0, acervo: 0, total: 12 },
            { item: "Câmera IP Bullet Full HD (VIP 1230 B G2)", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Câmera IP Bullet Full HD (VIPC 1230 B)", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Câmera Multi HD Bullet (VHL 1120 B G2)", central: 4, tecnico: 3, op: 0, acervo: 0, total: 7 },
            { item: "Câmera Multi HD Dome (VHL 1120 D G2)", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Fonte Chaveada 12.8V/5A (EFM 1205 G2)", central: 7, tecnico: 1, op: 0, acervo: 0, total: 8 },
            { item: "Fonte Chaveada 12.8V/2A (EF 1202)", central: 1, tecnico: 2, op: 0, acervo: 0, total: 3 },
            { item: "Mouse Óptico USB", central: 4, tecnico: 1, op: 0, acervo: 0, total: 5 },
            { item: "Protetor Eletrônico / Filtro de Linha (EPE 205)", central: 3, tecnico: 1, op: 0, acervo: 0, total: 4 },
            { item: "Gravador NVR 32 Canais (NVD 1432)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Caixa Cabo de Rede UTP CAT 5e (305m)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Caixa de Passagem VBOX 1100", central: 23, tecnico: 0, op: 0, acervo: 0, total: 23 },
            { item: "Rolo Cabo Coaxial / Par Trançado (100m)", central: 2, tecnico: 1, op: 0, acervo: 0, total: 3 },
            { item: "Bandeja para Rack 19\"", central: 6, tecnico: 0, op: 0, acervo: 0, total: 6 }
        ];

        const historicoData = [
            { data: "23/09/2026", item: "HD 4TB", tipo: "Entrada", qtd: 2, origem: "Fornecedor Voice", destino: "Técnico Marcelo", obs: "SERA RETIRADO EM 23/09/2026" },
            { data: "21/09/2026", item: "Mikrotik Hap", tipo: "Entrada", qtd: 7, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Antena Elsys", tipo: "Entrada", qtd: 1, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Roteador Intelbras 5G", tipo: "Entrada", qtd: 6, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Rolo Cabo Coaxial 100m", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Câmera Multi HD Dome", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Câmera Multi HD Bullet", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Fonte Chaveada 12V 2A", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "18/09/2026", item: "Fonte Chaveada 12V 5A", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação Cold Pac" },
            { data: "18/09/2026", item: "Nobreak 1200VA", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Substituição Cold da COS" },
            { data: "18/09/2026", item: "Nobreak 1200VA", tipo: "Entrada", qtd: 2, origem: "Fornecedor Voice", destino: "Técnico Marcelo", obs: "Acervo técnico" },
            { data: "15/09/2026", item: "Nobreak 1200VA", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de nobreak ADS" },
            { data: "15/09/2026", item: "Câmera Multi HD Dome", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de camera ROM" },
            { data: "15/09/2026", item: "Monitor Mnbox", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de monitor ROM" },
            { data: "15/09/2026", item: "Monitor Mnbox", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "Atendimentos 15/09" },
            { data: "14/09/2026", item: "Antena Hikvision", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação na JUK" },
            { data: "10/09/2026", item: "DVR INTELBRAS 4 Ch", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação GRV Projeto KVS" },
            { data: "09/09/2026", item: "Câmera IP Bullet Full HD", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "Acervo para o técnico" },
            { data: "03/09/2026", item: "Antena Elsys", tipo: "Saída", qtd: 2, origem: "Estoque Operacional", destino: "Técnico Marcelo", obs: "Instalação MKC e Reserva técnica" },
            { data: "02/09/2026", item: "Caixa de Passagem VBOX 1100", tipo: "Entrada", qtd: 23, origem: "Levantamento", destino: "Central", obs: "Levantamento Ian" },
            { data: "02/09/2026", item: "Câmera IP Dome Full HD", tipo: "Entrada", qtd: 12, origem: "Levantamento", destino: "Central", obs: "Levantamento Ian" },
            { data: "03/08/2026", item: "Mikrotik Hap", tipo: "Saída", qtd: 7, origem: "Estoque Operacional", destino: "Técnico Marcelo", obs: "Substituição INT e Instalação OSA" },
            { data: "30/07/2026", item: "Mikrotik Hap", tipo: "Entrada", qtd: 9, origem: "Central", destino: "Estoque Operacional", obs: "Alocação inicial" }
        ];

        function renderEstoque(data) {
            const tbody = document.getElementById('tbodyEstoque');
            tbody.innerHTML = '';
            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-700/30 transition';
                tr.innerHTML = `
                    <td class="p-3 font-medium text-white">${row.item}</td>
                    <td class="p-3 text-center">${row.central}</td>
                    <td class="p-3 text-center">${row.tecnico}</td>
                    <td class="p-3 text-center">${row.op}</td>
                    <td class="p-3 text-center">${row.acervo}</td>
                    <td class="p-3 text-center font-bold text-blue-400">${row.total}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderHistorico(data) {
            const tbody = document.getElementById('tbodyHistorico');
            tbody.innerHTML = '';
            data.forEach(row => {
                const badgeClass = row.tipo === 'Entrada' 
                    ? 'bg-emerald-500/10 text-emerald-400 border-emerald-500/20' 
                    : 'bg-rose-500/10 text-rose-400 border-rose-500/20';
                
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-700/30 transition';
                tr.innerHTML = `
                    <td class="p-3 text-slate-400 whitespace-nowrap">${row.data}</td>
                    <td class="p-3 font-medium text-white">${row.item}</td>
                    <td class="p-3 text-center">
                        <span class="px-2 py-1 text-xs rounded-full border ${badgeClass} font-semibold">${row.tipo}</span>
                    </td>
                    <td class="p-3 text-center font-semibold text-white">${row.qtd}</td>
                    <td class="p-3 text-slate-300">${row.origem}</td>
                    <td class="p-3 text-slate-300">${row.destino}</td>
                    <td class="p-3 text-slate-400 italic">${row.obs || '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterEstoque() {
            const query = document.getElementById('searchEstoque').value.toLowerCase();
            const filtered = estoqueData.filter(d => d.item.toLowerCase().includes(query));
            renderEstoque(filtered);
        }

        function filterHistorico() {
            const query = document.getElementById('searchHistorico').value.toLowerCase();
            const tipo = document.getElementById('filterTipo').value;

            const filtered = historicoData.filter(d => {
                const matchesSearch = d.item.toLowerCase().includes(query) || 
                                      d.origem.toLowerCase().includes(query) || 
                                      d.destino.toLowerCase().includes(query) ||
                                      d.obs.toLowerCase().includes(query);
                const matchesTipo = tipo === 'todos' || d.tipo === tipo;
                return matchesSearch && matchesTipo;
            });
            renderHistorico(filtered);
        }

        function switchTab(tab) {
            const secEstoque = document.getElementById('secEstoque');
            const secHistorico = document.getElementById('secHistorico');
            const btnEstoque = document.getElementById('btnTabEstoque');
            const btnHistorico = document.getElementById('btnTabHistorico');

            if (tab === 'estoque') {
                secEstoque.classList.remove('hidden');
                secHistorico.classList.add('hidden');
                btnEstoque.className = 'px-4 py-2 rounded-lg text-sm font-semibold transition bg-blue-600 text-white shadow';
                btnHistorico.className = 'px-4 py-2 rounded-lg text-sm font-semibold transition bg-slate-700 text-slate-300 hover:bg-slate-600';
            } else {
                secEstoque.classList.add('hidden');
                secHistorico.classList.remove('hidden');
                btnHistorico.className = 'px-4 py-2 rounded-lg text-sm font-semibold transition bg-blue-600 text-white shadow';
                btnEstoque.className = 'px-4 py-2 rounded-lg text-sm font-semibold transition bg-slate-700 text-slate-300 hover:bg-slate-600';
            }
        }

        document.addEventListener('DOMContentLoaded', () => {
            renderEstoque(estoqueData);
            renderHistorico(historicoData);
        });
    </script>
</body>
</html>
