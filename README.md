<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciamento de Alunos - Calendário de Presença</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
    
    <style>
        body {
            background: linear-gradient(135deg, #4e54c8, #8f94fb);
            color: #333;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Arial', sans-serif;
            padding: 20px;
        }

        .container-admin {
            background: rgba(255, 255, 255, 0.9);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 1200px;
        }

        .btn-remove, .btn-add-calendar, .btn-presenca, .btn-falta {
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 12px;
        }

        .btn-remove {
            background: red;
            color: white;
        }

        .btn-presenca {
            background: green;
            color: white;
        }

        .btn-falta {
            background: red;
            color: white;
        }

        .aluno-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 10px;
            padding: 10px;
            background: white;
            border-radius: 5px;
            box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
        }

        .btn-clear-all {
            background-color: #dc3545;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 20px;
            display: block;
            width: 100%;
        }

        .hidden {
            display: none;
        }

        /* Melhorar visualização do calendário */
        .calendar {
            display: grid;
            grid-template-columns: repeat(5, 1fr);  /* 5 colunas para os dias da semana */
            gap: 10px;
            margin-top: 20px;
        }

        .calendar .calendar-day {
            padding: 20px;
            background: #f5f5f5;
            text-align: center;
            border-radius: 5px;
            min-height: 120px;
            display: flex;
            flex-direction: column;
            justify-content: flex-start;
            border: 1px solid #ddd;
            position: relative;
        }

        .calendar .calendar-day span {
            font-weight: bold;
            display: block;
            margin-bottom: 10px;
        }

        .calendar .occupied {
            background-color: #ffffff;
            color: rgb(0, 0, 0);
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }

        .calendar .calendar-day .students {
            font-size: 12px;
            margin-top: 10px;
            overflow-y: auto;
        }

        .calendar .calendar-day .students .student {
            margin: 5px 0;
            padding: 2px 0;
        }

        .calendar .calendar-day .students .student span {
            background-color: #fff;
            padding: 5px;
            border-radius: 5px;
            box-shadow: 0 0 5px rgba(0,0,0,0.1);
        }

        /* Estilo para os períodos */
        .periodo {
            font-weight: bold;
            margin-top: 5px;
        }

        /* Relatório */
        .relatorio {
            margin-top: 20px;
            padding: 20px;
            background-color: #f5f5f5;
            border-radius: 5px;
            box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
            font-size: 14px;
        }
    </style>
</head>
<body>

    <div class="container-admin">
        <h2>Gerenciar Alunos - Sistema de Chamada</h2>

        <!-- Filtro de Disciplina -->
        <div class="mb-3">
            <label for="filtroDisciplina" class="form-label">Filtrar por Disciplina</label>
            <select id="filtroDisciplina" class="form-select" onchange="filtrarAlunos()">
                <option value="">Todas as Disciplinas</option>
                <option value="Voleibol">Voleibol</option>
                <option value="Futebol">Futebol</option>
                <option value="Street">Street</option>
                <option value="Teatro">Teatro</option>
                <option value="Ludoteca">Ludoteca</option>
                <option value="Eureca">Eureca</option>
                <option value="Música">Música</option>
            </select>
        </div>

        <!-- Formulário para adicionar aluno -->
        <div class="mb-3">
            <input type="text" id="nomeAluno" class="form-control" placeholder="Nome do Aluno">
        </div>

        <div class="mb-3">
            <select id="disciplina" class="form-select">
                <option value="">Disciplina</option>
                <option value="Voleibol">Voleibol</option>
                <option value="Futebol">Futebol</option>
                <option value="Street">Street</option>
                <option value="Teatro">Teatro</option>
                <option value="Ludoteca">Ludoteca</option>
                <option value="Eureca">Eureca</option>
                <option value="Música">Música</option>
            </select>
        </div>

        <div class="mb-3">
            <select id="periodo" class="form-select">
                <option value="">Período</option>
                <option value="PeriodoM1">Periodo 1</option>
                <option value="PeriodoM2">Periodo 2</option>
                <option value="PeriodoT1">Periodo 1</option>
                <option value="PeriodoT2">Periodo 2</option>
            </select>
        </div>

        <div class="mb-3">
            <select id="diaSemana" class="form-select">
                <option value="">Dia da Semana</option>
                <option value="segunda">Segunda</option>
                <option value="terca">Terça</option>
                <option value="quarta">Quarta</option>
                <option value="quinta">Quinta</option>
                <option value="sexta">Sexta</option>
            </select>
        </div>

        <button class="btn btn-primary w-100" onclick="adicionarAluno()">Adicionar Aluno</button>

        <!-- Lista de Alunos -->
        <h4 class="mt-4">Lista de Alunos</h4>
        <button class="btn btn-secondary w-100 mb-3" onclick="toggleAlunos()">Mostrar/Esconder Alunos</button>
        <ul id="listaAlunos"></ul>

        <!-- Botão para limpar todos os dados -->
        <button class="btn-clear-all" onclick="limparTodosDados()">Limpar Todos os Alunos</button>

        <!-- Calendário -->
        <h4 class="mt-4">Calendário</h4>
        <div class="calendar" id="calendar"></div>

        <!-- Botão para gerar relatório de presença -->
        <button class="btn btn-success w-100 mt-3" onclick="gerarRelatorio()">Gerar Relatório de Presença</button>

        <!-- Relatório de presença -->
        <div id="relatorio" class="relatorio hidden"></div>
    </div>

    <script>
        let alunos = JSON.parse(localStorage.getItem("alunos")) || [];
        let filtroDisciplina = "";
        let calendario = JSON.parse(localStorage.getItem("calendario")) || {};

        // Função para adicionar aluno
        function adicionarAluno() {
            const nome = document.getElementById('nomeAluno').value;
            const disciplina = document.getElementById('disciplina').value;
            const periodo = document.getElementById('periodo').value;
            const diaSemana = document.getElementById('diaSemana').value;

            if (!nome || !disciplina || !periodo || !diaSemana) {
                alert("Preencha todos os campos.");
                return;
            }

            const aluno = { nome, disciplina, periodo, diaSemana };
            alunos.push(aluno);
            localStorage.setItem("alunos", JSON.stringify(alunos));
            adicionarAoCalendario(aluno);
            renderizarListaAlunos();
        }

        // Função para renderizar a lista de alunos
        function renderizarListaAlunos() {
            const listaAlunos = document.getElementById("listaAlunos");
            listaAlunos.innerHTML = "";

            alunos.filter(aluno => filtroDisciplina === "" || aluno.disciplina === filtroDisciplina)
                  .forEach((aluno, index) => {
                const li = document.createElement("li");
                li.classList.add("aluno-item");
                li.innerHTML = `${aluno.nome} - ${aluno.disciplina} 
                <button class="btn-remove" onclick="removerAluno(${index})">Remover</button>`;
                listaAlunos.appendChild(li);
            });
        }

        // Função para adicionar aluno ao calendário
        function adicionarAoCalendario(aluno) {
            const key = `${aluno.diaSemana}_${aluno.periodo}`;
            if (!calendario[key]) {
                calendario[key] = [];
            }
            calendario[key].push({ nome: aluno.nome, presente: null });
            localStorage.setItem("calendario", JSON.stringify(calendario));
            renderizarCalendario();
        }

        // Função para marcar presença
        function marcarPresenca(dia, periodo, nome) {
            const key = `${dia}_${periodo}`;
            const aluno = calendario[key].find(aluno => aluno.nome === nome);
            if (aluno) {
                aluno.presente = true;
                localStorage.setItem("calendario", JSON.stringify(calendario));
                renderizarCalendario();
            }
        }

        // Função para marcar falta
        function marcarFalta(dia, periodo, nome) {
            const key = `${dia}_${periodo}`;
            const aluno = calendario[key].find(aluno => aluno.nome === nome);
            if (aluno) {
                aluno.presente = false;
                localStorage.setItem("calendario", JSON.stringify(calendario));
                renderizarCalendario();
            }
        }

        // Função para remover aluno
        function removerAluno(index) {
            alunos.splice(index, 1);
            localStorage.setItem("alunos", JSON.stringify(alunos));
            renderizarListaAlunos();
        }

        // Função para limpar todos os dados
        function limparTodosDados() {
            if (confirm("Você tem certeza que deseja apagar todos os dados?")) {
                localStorage.removeItem("alunos");
                alunos = [];
                localStorage.removeItem("calendario");
                calendario = {};
                renderizarListaAlunos();
                renderizarCalendario();
            }
        }

        // Função para mostrar/esconder a lista de alunos
        function toggleAlunos() {
            const listaAlunos = document.getElementById("listaAlunos");
            listaAlunos.classList.toggle("hidden");
        }

        // Função para filtrar alunos
        function filtrarAlunos() {
            filtroDisciplina = document.getElementById("filtroDisciplina").value;
            renderizarListaAlunos();
        }

        // Função para renderizar o calendário
        function renderizarCalendario() {
            const calendarElement = document.getElementById("calendar");
            calendarElement.innerHTML = "";

            const diasSemana = ["segunda", "terca", "quarta", "quinta", "sexta"];
            const periodos = ["PeriodoM1", "PeriodoM2", "periodoT1", "periodoT2"];

            diasSemana.forEach(dia => {
                periodos.forEach(periodo => {
                    const key = `${dia}_${periodo}`;
                    const div = document.createElement("div");
                    div.classList.add("calendar-day");
                    div.innerHTML = `<span>${dia.charAt(0).toUpperCase() + dia.slice(1)}</span><span class="periodo">${periodo.charAt(0).toUpperCase() + periodo.slice(1)}</span>`;

                    if (calendario[key]) {
                        div.classList.add("occupied");
                        const studentsDiv = document.createElement("div");
                        studentsDiv.classList.add("students");
                        calendario[key].forEach(aluno => {
                            const studentDiv = document.createElement("div");
                            studentDiv.classList.add("student");
                            studentDiv.innerHTML = `<span>${aluno.nome}</span>
                                                    <button class="btn-presenca" onclick="marcarPresenca('${dia}', '${periodo}', '${aluno.nome}')">Presente</button>
                                                    <button class="btn-falta" onclick="marcarFalta('${dia}', '${periodo}', '${aluno.nome}')">Falta</button>`;
                            studentsDiv.appendChild(studentDiv);
                        });
                        div.appendChild(studentsDiv);
                    }

                    calendarElement.appendChild(div);
                });
            });
        }

        // Função para gerar o relatório de presença
        function gerarRelatorio() {
            const relatorioDiv = document.getElementById("relatorio");
            relatorioDiv.innerHTML = ""; // Limpar relatório anterior

            const hoje = new Date();
            const dataHoje = hoje.toLocaleDateString("pt-BR");

            let relatorioContent = `<h5>Relatório de Presença - ${dataHoje}</h5>`;

            Object.keys(calendario).forEach(key => {
                const [dia, periodo] = key.split("_");
                const alunosDiaPeriodo = calendario[key];

                relatorioContent += `<h6>${dia.charAt(0).toUpperCase() + dia.slice(1)} - ${periodo.charAt(0).toUpperCase() + periodo.slice(1)}</h6><ul>`;

                alunosDiaPeriodo.forEach(aluno => {
                    relatorioContent += `<li>${aluno.nome} - ${aluno.presente === null ? 'Não registrado' : aluno.presente ? 'Presente' : 'Faltou'}</li>`;
                });

                relatorioContent += "</ul>";
            });

            relatorioDiv.innerHTML = relatorioContent;
            relatorioDiv.classList.remove("hidden");
        }

        // Inicializar a renderização dos alunos e calendário
        renderizarListaAlunos();
        renderizarCalendario();
    </script>
</body>
</html>
