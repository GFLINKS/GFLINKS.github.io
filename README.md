<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel Web - Chamados & Rotas 2026</title>
  <!-- Bootstrap 5 CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <!-- FontAwesome Icons -->
  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
  <style>
    :root {
      --sidebar-bg: #0f172a;
      --card-bg: #ffffff;
      --primary-color: #0284c7;
    }
    body { background-color: #f8fafc; font-family: 'Segoe UI', system-ui, -apple-system, sans-serif; }
    .sidebar { background-color: var(--sidebar-bg); color: #fff; min-height: 100vh; }
    .nav-link { color: #94a3b8; font-weight: 500; border-radius: 8px; margin-bottom: 6px; padding: 12px 16px; transition: all 0.2s; }
    .nav-link:hover, .nav-link.active { background-color: #1e293b; color: #38bdf8; }
    .card-kpi { background: var(--card-bg); border-radius: 12px; border: 1px solid #e2e8f0; padding: 20px; transition: transform 0.2s; }
    .card-kpi:hover { transform: translateY(-3px); }
    .card-panel { background: var(--card-bg); border-radius: 12px; border: 1px solid #e2e8f0; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); padding: 24px; }
    .btn-sync { background-color: var(--primary-color); color: white; font-weight: 600; border-radius: 8px; padding: 12px 16px; border: none; width: 100%; }
    .btn-sync:hover { background-color: #0369a1; color: white; }
    .badge-atrasada { background-color: #ef4444; color: white; }
    .badge-aberta { background-color: #f59e0b; color: white; }
    .badge-concluida { background-color: #10b981; color: white; }
    .badge-andamento { background-color: #3b82f6; color: white; }
    .table thead { background-color: #f1f5f9; }
    .table-responsive { max-height: 600px; overflow-y: auto; }
  </style>
</head>
<body>

<div class="container-fluid">
  <div class="row">
    
    <!-- Sidebar / Menu Lateral -->
    <div class="col-md-3 col-lg-2 sidebar p-3 d-flex flex-column">
      <div class="d-flex align-items-center my-3 px-2">
        <i class="fa-solid fa-headset text-info fs-3 me-2"></i>
        <span class="fs-5 fw-bold text-white">Chamados 2026</span>
      </div>
      <hr class="text-secondary">
      
      <ul class="nav nav-pills flex-column mb-auto">
        <li class="nav-item">
          <a href="#" class="nav-link active" onclick="alternarAba('abertos', this)"><i class="fa-solid fa-folder-open me-2"></i>Chamados Abertos</a>
        </li>
        <li>
          <a href="#" class="nav-link" onclick="alternarAba('rota', this)"><i class="fa-solid fa-route me-2"></i>Agendamento Rota</a>
        </li>
        <li>
          <a href="#" class="nav-link" onclick="alternarAba('finalizados', this)"><i class="fa-solid fa-clock-rotate-left me-2"></i>Histórico Finalizados</a>
        </li>
      </ul>
      
      <hr class="text-secondary">
      
      <button class="btn btn-sync shadow-sm" onclick="sincronizarDrive()">
        <i class="fa-solid fa-arrows-rotate me-2"></i>Sincronizar Drive
      </button>
    </div>

    <!-- Área de Conteúdo Principal -->
    <div class="col-md-9 col-lg-10 p-4">
      
      <!-- Linha de KPIs (Resumo Geral) -->
      <div class="row g-3 mb-4">
        <div class="col-md-3">
          <div class="card-kpi d-flex align-items-center">
            <div class="bg-primary bg-opacity-10 p-3 rounded-3 me-3 text-primary"><i class="fa-solid fa-list-check fs-3"></i></div>
            <div>
              <div class="text-muted small fw-semibold">Chamados Abertos</div>
              <div class="fs-4 fw-bold text-dark" id="kpi-abertos">0</div>
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card-kpi d-flex align-items-center">
            <div class="bg-danger bg-opacity-10 p-3 rounded-3 me-3 text-danger"><i class="fa-solid fa-triangle-exclamation fs-3"></i></div>
            <div>
              <div class="text-muted small fw-semibold">Em Atraso</div>
              <div class="fs-4 fw-bold text-danger" id="kpi-atrasados">0</div>
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card-kpi d-flex align-items-center">
            <div class="bg-info bg-opacity-10 p-3 rounded-3 me-3 text-info"><i class="fa-solid fa-truck-fast fs-3"></i></div>
            <div>
              <div class="text-muted small fw-semibold">Rotas de Hoje</div>
              <div class="fs-4 fw-bold text-dark" id="kpi-rota">0</div>
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card-kpi d-flex align-items-center">
            <div class="bg-success bg-opacity-10 p-3 rounded-3 me-3 text-success"><i class="fa-solid fa-circle-check fs-3"></i></div>
            <div>
              <div class="text-muted small fw-semibold">Concluídos</div>
              <div class="fs-4 fw-bold text-success" id="kpi-finalizados">0</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Barra de Filtro e Busca -->
      <div class="card-panel mb-4 py-3">
        <div class="row g-3 align-items-center">
          <div class="col-md-5">
            <div class="input-group">
              <span class="input-group-text bg-light border-end-0"><i class="fa-solid fa-magnifying-glass text-muted"></i></span>
              <input type="text" id="input-busca" class="form-control border-start-0" placeholder="Pesquisar por Cliente, OS ou Técnico..." onkeyup="filtrarTabelas()">
            </div>
          </div>
          <div class="col-md-4">
            <select id="select-tecnico" class="form-select" onchange="filtrarTabelas()">
              <option value="">Todos os Técnicos</option>
            </select>
          </div>
          <div class="col-md-3 text-end">
            <button class="btn btn-outline-secondary w-100" onclick="atualizarPainel()"><i class="fa-solid fa-rotate me-1"></i> Atualizar Tela</button>
          </div>
        </div>
      </div>

      <!-- Indicador de Processamento -->
      <div id="loader" class="text-center my-5 d-none">
        <div class="spinner-border text-info" style="width: 3rem; height: 3rem;" role="status"></div>
        <p class="mt-3 text-secondary fw-semibold">Processando arquivos do Google Drive e calculando rotas...</p>
      </div>

      <!-- Seção: Chamados Abertos -->
      <div id="secao-abertos" class="secao-aba">
        <div class="d-flex justify-content-between align-items-center mb-3">
          <h4 class="fw-bold text-dark">Chamados em Aberto</h4>
        </div>
        <div class="card-panel">
          <div class="table-responsive">
            <table class="table table-hover align-middle" id="tb-abertos">
              <thead>
                <tr>
                  <th>Nº OS</th><th>Status</th><th>Cliente</th><th>Data Abertura</th><th>Técnico</th><th>Tipo OS</th><th>Data/Hora Prevista</th><th>Observações</th>
                </tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Seção: Rota / Agendamento -->
      <div id="secao-rota" class="secao-aba d-none">
        <div class="d-flex justify-content-between align-items-center mb-3">
          <h4 class="fw-bold text-dark"><i class="fa-solid fa-calendar-days text-info me-2"></i>Agendamento de Rota</h4>
        </div>
        <div class="card-panel">
          <div class="table-responsive">
            <table class="table table-striped align-middle" id="tb-rota">
              <thead>
                <tr>
                  <th>Data/Hora Prevista</th><th>Técnico</th><th>Nº OS</th><th>Cliente</th><th>Tipo Serviço</th><th>Status</th><th>Motivo/Defeito</th>
                </tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Seção: Finalizados -->
      <div id="secao-finalizados" class="secao-aba d-none">
        <div class="d-flex justify-content-between align-items-center mb-3">
          <h4 class="fw-bold text-dark"><i class="fa-solid fa-box-archive text-secondary me-2"></i>Histórico de Concluídos</h4>
        </div>
        <div class="card-panel">
          <div class="table-responsive">
            <table class="table table-hover align-middle" id="tb-finalizados">
              <thead>
                <tr>
                  <th>Nº OS</th><th>Status</th><th>Cliente</th><th>Data Abertura</th><th>Técnico</th><th>Tipo OS</th><th>Motivo</th><th>Data Arquivamento</th>
                </tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </div>

    </div>
  </div>
</div>

<script>
  let cacheDados = null;

  window.onload = function() {
    atualizarPainel();
  };

  function alternarAba(nomeAba, elemento) {
    document.querySelectorAll('.secao-aba').forEach(el => el.classList.add('d-none'));
    document.querySelectorAll('.nav-link').forEach(el => el.classList.remove('active'));
    document.getElementById('secao-' + nomeAba).classList.remove('d-none');
    elemento.classList.add('active');
  }

  function atualizarPainel() {
    google.script.run.withSuccessHandler(renderizarDados).obterDadosPainel();
  }

  function renderizarDados(dados) {
    cacheDados = dados;
    
    // Atualiza KPIs
    if (dados.kpis) {
      document.getElementById('kpi-abertos').innerText = dados.kpis.totalAbertos;
      document.getElementById('kpi-atrasados').innerText = dados.kpis.totalAtrasados;
      document.getElementById('kpi-finalizados').innerText = dados.kpis.totalFinalizados;
      document.getElementById('kpi-rota').innerText = dados.kpis.rotaHoje;
    }

    // Preenche combo de técnicos
    preencherComboTecnicos(dados.abertos);

    // Desenha tabelas
    desenharTabela('tb-abertos', dados.abertos);
    desenharTabela('tb-rota', dados.rota);
    desenharTabela('tb-finalizados', dados.finalizados);
  }

  function preencherComboTecnicos(matriz) {
    const select = document.getElementById('select-tecnico');
    select.innerHTML = '<option value="">Todos os Técnicos</option>';
    if (!matriz || matriz.length <= 1) return;

    const tecnicos = new Set();
    for (let i = 1; i < matriz.length; i++) {
      const tec = matriz[i][4];
      if (tec && tec.trim() !== "") tecnicos.add(tec.trim());
    }

    tecnicos.forEach(t => {
      const opt = document.createElement('option');
      opt.value = t;
      opt.innerText = t;
      select.appendChild(opt);
    });
  }

  function formatarBadgeStatus(val) {
    if (!val) return '';
    const txt = String(val).trim();
    const st = txt.toLowerCase();
    
    let classe = 'badge-aberta';
    if (st.includes('atrasad')) classe = 'badge-atrasada';
    else if (st.includes('conclu') || st.includes('finaliz')) classe = 'badge-concluida';
    else if (st.includes('andamento')) classe = 'badge-andamento';

    return `<span class="badge ${classe}">${txt}</span>`;
  }

  function desenharTabela(idTabela, matriz) {
    const tbody = document.getElementById(idTabela).querySelector('tbody');
    tbody.innerHTML = '';

    if (!matriz || matriz.length <= 1) {
      tbody.innerHTML = '<tr><td colspan="8" class="text-center text-muted py-4">Nenhum registro encontrado.</td></tr>';
      return;
    }

    for (let i = 1; i < matriz.length; i++) {
      const tr = document.createElement('tr');
      const linha = matriz[i];

      linha.forEach((col, idx) => {
        const td = document.createElement('td');
        // Se for coluna de Status (índice 1 no padrão)
        if (idx === 1 && idTabela !== 'tb-rota') {
          td.innerHTML = formatarBadgeStatus(col);
        } else if (idx === 5 && idTabela === 'tb-rota') {
          td.innerHTML = formatarBadgeStatus(col);
        } else {
          td.innerText = col !== undefined ? col : '';
        }
        tr.appendChild(td);
      });

      tbody.appendChild(tr);
    }
  }

  function filtrarTabelas() {
    const busca = document.getElementById('input-busca').value.toLowerCase();
    const tecnico = document.getElementById('select-tecnico').value.toLowerCase();

    ['tb-abertos', 'tb-rota', 'tb-finalizados'].forEach(idTb => {
      const linhas = document.getElementById(idTb).querySelectorAll('tbody tr');
      linhas.forEach(row => {
        const text = row.innerText.toLowerCase();
        const atendeBusca = !busca || text.includes(busca);
        const atendeTecnico = !tecnico || text.includes(tecnico);
        row.style.display = (atendeBusca && atendeTecnico) ? '' : 'none';
      });
    });
  }

  function sincronizarDrive() {
    document.getElementById('loader').classList.remove('d-none');
    google.script.run
      .withSuccessHandler(function(res) {
        document.getElementById('loader').classList.add('d-none');
        if (res.status === "sucesso") {
          alert(res.mensagem);
          atualizarPainel();
        } else {
          alert("Atenção: " + res.mensagem);
        }
      })
      .withFailureHandler(function(err) {
        document.getElementById('loader').classList.add('d-none');
        alert("Erro na execução: " + err.message);
      })
      .sincronizarChamados();
  }
</script>
</body>
</html>
