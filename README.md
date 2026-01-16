
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Sistema de Alumnos - JS</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Poppins', sans-serif;
}

body {
    background: linear-gradient(to bottom right, #DFFFD8, #BDE0FE);
    min-height: 100vh;
    padding: 30px;
}

h1 {
    text-align: center;
    color: #004E64;
    margin-bottom: 20px;
}

.form {
    width: 60%;
    margin: auto;
    background: white;
    padding: 30px;
    border-radius: 15px;
    box-shadow: 0 5px 20px rgba(0,0,0,.2);
}

.form_label {
    display: block;
    margin-top: 10px;
    font-weight: bold;
}

.form_input {
    width: 100%;
    padding: 8px;
    margin-top: 5px;
    border-radius: 8px;
    border: 1px solid #A7E9AF;
}

button {
    margin-top: 15px;
    padding: 10px 20px;
    background: #00C897;
    border: none;
    color: white;
    font-size: 16px;
    border-radius: 8px;
    cursor: pointer;
}

button:hover {
    background: #00916E;
}

table {
    width: 95%;
    margin: 40px auto;
    border-collapse: collapse;
    background: white;
}

th, td {
    padding: 10px;
    border: 1px solid #ccc;
    text-align: center;
}

th {
    background: #A7E9AF;
}
</style>
</head>

<body>

<h1>REGISTRO DE ALUMNOS</h1>

<div class="form">
    <input type="hidden" id="editIndex">

    <label class="form_label">Alumno ID</label>
    <input class="form_input" id="alumno_id">

    <label class="form_label">Nombre</label>
    <input class="form_input" id="nombre">

    <label class="form_label">Apellido</label>
    <input class="form_input" id="apellido">

    <label class="form_label">Fecha</label>
    <input class="form_input" id="fecha">

    <label class="form_label">Peso (kg)</label>
    <input class="form_input" id="peso" type="number">

    <label class="form_label">Altura (m)</label>
    <input class="form_input" id="altura" type="number" step="0.01">

    <label class="form_label">Correo</label>
    <input class="form_input" id="correo">

    <label class="form_label">Género</label>
    <input class="form_input" id="genero">

    <button onclick="guardarAlumno()">GUARDAR / ACTUALIZAR</button>
</div>

<table>
<thead>
<tr>
<th>ID</th><th>Nombre</th><th>Apellido</th><th>Fecha</th>
<th>Peso</th><th>Altura</th><th>IMC</th>
<th>Correo</th><th>Género</th>
<th>Editar</th><th>Eliminar</th>
</tr>
</thead>
<tbody id="tabla"></tbody>
</table>

<script>
let alumnos = JSON.parse(localStorage.getItem("alumnos")) || [];

function guardarAlumno() {
    const alumno = {
        alumno_id: alumno_id.value,
        nombre: nombre.value,
        apellido: apellido.value,
        fecha: fecha.value,
        peso: parseFloat(peso.value),
        altura: parseFloat(altura.value),
        correo: correo.value,
        genero: genero.value
    };

    alumno.imc = (alumno.peso / (alumno.altura * alumno.altura)).toFixed(2);

    if (editIndex.value === "") {
        alumnos.push(alumno);
    } else {
        alumnos[editIndex.value] = alumno;
        editIndex.value = "";
    }

    localStorage.setItem("alumnos", JSON.stringify(alumnos));
    limpiarFormulario();
    mostrarAlumnos();
}

function mostrarAlumnos() {
    tabla.innerHTML = "";
    alumnos.forEach((a, i) => {
        tabla.innerHTML += `
        <tr>
            <td>${a.alumno_id}</td>
            <td>${a.nombre}</td>
            <td>${a.apellido}</td>
            <td>${a.fecha}</td>
            <td>${a.peso}</td>
            <td>${a.altura}</td>
            <td>${a.imc}</td>
            <td>${a.correo}</td>
            <td>${a.genero}</td>
            <td><button onclick="editarAlumno(${i})">✏</button></td>
            <td><button onclick="eliminarAlumno(${i})">❌</button></td>
        </tr>`;
    });
}

function editarAlumno(i) {
    const a = alumnos[i];
    alumno_id.value = a.alumno_id;
    nombre.value = a.nombre;
    apellido.value = a.apellido;
    fecha.value = a.fecha;
    peso.value = a.peso;
    altura.value = a.altura;
    correo.value = a.correo;
    genero.value = a.genero;
    editIndex.value = i;
}

function eliminarAlumno(i) {
    if (confirm("¿Eliminar alumno?")) {
        alumnos.splice(i, 1);
        localStorage.setItem("alumnos", JSON.stringify(alumnos));
        mostrarAlumnos();
    }
}

function limpiarFormulario() {
    document.querySelectorAll(".form_input").forEach(i => i.value = "");
}

mostrarAlumnos();
</script>

</body>
</html>
