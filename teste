<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciamento de Presença</title>
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

        .calendar {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 15px;
            margin-top: 30px;
            text-align: center;
        }

        .calendar-cell {
            background: #fff;
            padding: 15px;
            border-radius: 10px;
            min-height: 200px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }

        .hidden {
            display: none;
        }

        .btn-remove {
            background: red;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
        }

        .btn-add-calendar {
            background: green;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            margin-left: 5px;
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
    </style>
</head>
<body>

    <div class="container-admin">
        <h2>Gerenciar Alunos</h2>

        <input type="text" id="nomeAluno" class="form-control mb-2" placeholder="Nome do Aluno">
        
        <select id="disciplina" class="form-select mb-2">
            <option value="">Disciplina</option>
            <option value="voleibol">Voleibol</option>
            <option value="futebol">Futebol</option>
            <option value="street">Street</option>
            <option value="teatro">Teatro</option>
            <option value="ludoteca">Ludoteca</option>
            <option value="eureca">Eureca</option>
            <option value="musica">Música</option>
        </select>

        <select id="periodo" class="form-select mb-2">
            <option value="">Período</option>
            <option value="manha">Manhã</option>
            <option value="tarde">Tarde</option>
        </select>

        <select id="diaSemana" class="form-select mb-3">
            <option value="">Dia da Semana</option>
            <option value="segunda">Segunda</option>
            <option value="terca">Terça</option>
            <option value="quarta">Quarta</option>
            <option value="quinta">Quinta</option>
            <option value="sexta">Sexta</option>
        </select>

        <button class="btn btn-primary w-100" onclick="adicionarAluno()">Adicionar Aluno</button>
        <button class="btn btn-secondary w-100 mt-2" onclick="toggleLista()">Esconder Lista</button>

        <div class="alunos-lista" id="listaContainer">
            <h4>Lista de Alunos</h4>
            <ul id="listaAlunos"></ul>
        </div>

        <div class="calendar">
            <div class="calendar-cell" id="segunda"><strong>Segunda</strong><div id="manhaSegunda"></div><hr><div id="tardeSegunda"></div></div>
            <div class="calendar-cell" id="terca"><strong>Terça</strong><div id="manhaTerca"></div><hr><div id="tardeTerca"></div></div>
            <div class="calendar-cell" id="quarta"><strong>Quarta</strong><div id="manhaQuarta"></div><hr><div id="tardeQuarta"></div></div>
            <div class="calendar-cell" id="quinta"><strong>Quinta</strong><div id="manhaQuinta"></div><hr><div id="tardeQuinta"></div></div>
            <div class="calendar-cell" id="sexta"><strong>Sexta</strong><div id="manhaSexta"></div><hr><div id="tardeSexta"></div></div>
        </div>
    </div>

    <script>
        let alunos = JSON.parse(localStorage.getItem("alunos")) || [];
        let listaVisivel = true;

        function adicionarAluno() {
            const nome = document.getElementById('nomeAluno').value;
            const disciplina = document.getElementById('disciplina').value;
            const periodo = document.getElementById('periodo').value;
            const diaSemana = document.getElementById('diaSemana').value;

            if (!nome || !disciplina || !periodo || !diaSemana) {
                alert("Preencha todos os campos.");
                return;
            }

            alunos.push({ nome, disciplina, periodo, diaSemana, noCalendario: false });
            localStorage.setItem("alunos", JSON.stringify(alunos));

            renderizarListaAlunos();
        }

        function adicionarAoCalendario(index) {
            alunos[index].noCalendario = true;
            localStorage.setItem("alunos", JSON.stringify(alunos));
            renderizarCalendario();
        }

        function removerAluno(index) {
            alunos.splice(index, 1);
            localStorage.setItem("alunos", JSON.stringify(alunos));
            renderizarListaAlunos();
            renderizarCalendario();
        }

        function renderizarListaAlunos() {
            const listaAlunos = document.getElementById("listaAlunos");
            listaAlunos.innerHTML = "";

            alunos.forEach((aluno, index) => {
                const li = document.createElement("li");
                li.classList.add("aluno-item");
                li.innerHTML = `${aluno.nome} - ${aluno.disciplina}
                    <button class="btn-add-calendar" onclick="adicionarAoCalendario(${index})">Colocar no Calendário</button>
                    <button class="btn-remove" onclick="removerAluno(${index})">X</button>
                `;
                listaAlunos.appendChild(li);
            });
        }

        function renderizarCalendario() {
            document.querySelectorAll('.calendar-cell div[id^="manha"], .calendar-cell div[id^="tarde"]').forEach(el => el.innerHTML = '');

            alunos.filter(aluno => aluno.noCalendario).forEach(aluno => {
                document.getElementById(`${aluno.periodo}${aluno.diaSemana}`).innerHTML += `<div><strong>${aluno.nome}</strong> - ${aluno.disciplina}</div>`;
            });
        }

        function toggleLista() {
            document.getElementById("listaContainer").classList.toggle("hidden");
        }

        renderizarListaAlunos();
        renderizarCalendario();
    </script>

</body>
</html>
