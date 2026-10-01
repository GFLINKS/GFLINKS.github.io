<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>APP ORÇAMENTO - Emissão Técnica</title>
    <style>
        :root {
            --primary: #1e3a8a;
            --primary-hover: #1d4ed8;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --border: #cbd5e1;
            --text: #0f172a;
            --danger: #ef4444;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
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
            background-color: #10b981;
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

        .btn-small:hover {
            background-color: #475569;
        }

        .drop-zone {
            border: 2px dashed var(--primary);
            border-radius: 8px;
            padding: 20px;
            text-align: center;
            background-color: #f1f5f9;
            cursor: pointer;
            transition: background-color 0.2s, border-color 0.2s;
        }

        .drop-zone:hover, .drop-zone.dragover {
            background-color: #e2e8f0;
            border-color: var(--primary-hover);
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
            position: relative;
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

        @media print {
            body { background: white; padding: 0; }
            .container { box-shadow: none; border: none; padding: 0; max-width: 100%; }
            .no-print { display: none !important; }
            input, textarea { border: none !important; background: transparent !important; padding: 0 !important; }
            .photo-card img { height: auto; max-height: 200px; }
            .btn-remove-photo { display: none !important; }
            .req { display: none !important; }
        }
    </style>
</head>
<body>

<div class="container">
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

<script>
    const CHAVE_BANCO_NUMERO = 'app_orcamento_ultimo_numero';
    const URL_GOOGLE_SHEETS = "https://script.google.com/macros/s/AKfycbx_CTDs1fCmZ1PmS5ZKstISqJ5nk0Aay0xJTia8AaWowMIh4zqXFBSXRYJu71qPeOY_Jw/exec";

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
</script>

</body>
</html>
