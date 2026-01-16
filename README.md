
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Sistema Profesional IMC</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:'Poppins',sans-serif;
}

body{
    background:linear-gradient(to bottom right,#DFFFD8,#BDE0FE);
    padding:30px;
}

h1{
    text-align:center;
    color:#004E64;
    margin-bottom:20px;
    font-size:2.5em;
}

.form{
    width:65%;
    margin:auto;
    background:#fff;
    padding:30px;
    border-radius:20px;
    box-shadow:0 10px 30px rgba(0,0,0,.2);
}

.form_label{
    font-weight:600;
    margin-top:12px;
    display:block;
}

.form_input{
    width:100%;
    padding:10px;
    border-radius:8px;
    border:1px solid #A7E9AF;
    margin-top:5px;
}

button{
    margin-top:15px;
    padding:12px 25px;
    background:#00C897;
    border:none;
    border-radius:10px;
    color:white;
    font-size:16px;
    cursor:pointer;
}

button:hover{
    background:#00916E;
}

table{
    width:95%;
    margin:40px auto;
    border-collapse:collapse;
    background:#fff;
    box-shadow:0 5px 20px rgba(0,0,0,.2);
}

th,td{
    padding:10px;
    text-align:center;
    border:1px solid #ccc;
}

th{
    background:#A7E9AF;
}

.recomendacion{
    width:80%;
    margin:30px auto;
    background:#fff;
    padding:25px;
    border-radius:20px;
    box-shadow:0 5px 20px rgba(0,0,0,.2);
    text-align:center;
}

.recomendacion img{
    width:200px;
    margin:15px 0;
}

.links{
    text-align:center;
    margin-top:30px;
}

.links a{
    display:block;
    margin:8px;
    color:#006D5B;
    font-weight:bold;
}
</style>
</head>

<body>

<h1>Sistema Inteligente de IMC</h1>

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

<button onclick="guardar()">GUARDAR / ACTUALIZAR</button>
</div>

<table>
<thead>
<tr>
<th>ID</th><th>Nombre</th><th>Apellido</th><th>IMC</th>
<th>Editar</th><th>Eliminar</th>
</tr>
</thead>
<tbody id="tabla"></tbody>
</table>

<div class="recomendacion" id="recomendacion" style="display:none;">
<h2 id="tituloRec"></h2>
<img id="imgIMC">
<p id="textoRec"></p>
</div>

<div class="links">
<h3>🔗 Recursos de Ayuda</h3>
<a href="https://www.cdc.gov/healthyweight/spanish/index.html" target="_blank">✔ CDC – Peso saludable</a>
<a href="https://www.imss.gob.mx/salud-en-linea/nutricion" target="_blank">✔ IMSS – Nutrición</a>
<a href="https://www.healthline.com/nutrition/how-to-gain-weight" target="_blank">✔ Subir de peso saludablemente</a>
<a href="https://www.mayoclinic.org/healthy-lifestyle/weight-loss" target="_blank">✔ Bajar de peso – Mayo Clinic</a>
</div>

<script>
let alumnos = JSON.parse(localStorage.getItem("alumnos")) || [];

function guardar(){
    let alumno={
        id:alumno_id.value,
        nombre:nombre.value,
        apellido:apellido.value,
        peso:parseFloat(peso.value),
        altura:parseFloat(altura.value)
    };

    alumno.imc=(alumno.peso/(alumno.altura*alumno.altura)).toFixed(2);

    if(editIndex.value===""){
        alumnos.push(alumno);
    }else{
        alumnos[editIndex.value]=alumno;
        editIndex.value="";
    }

    localStorage.setItem("alumnos",JSON.stringify(alumnos));
    mostrar();
    mostrarRecomendacion(alumno.imc);
    limpiar();
}

function mostrar(){
    tabla.innerHTML="";
    alumnos.forEach((a,i)=>{
        tabla.innerHTML+=`
        <tr>
        <td>${a.id}</td>
        <td>${a.nombre}</td>
        <td>${a.apellido}</td>
        <td>${a.imc}</td>
        <td><button onclick="editar(${i})">✏</button></td>
        <td><button onclick="eliminar(${i})">❌</button></td>
        </tr>`;
    });
}

function editar(i){
    let a=alumnos[i];
    alumno_id.value=a.id;
    nombre.value=a.nombre;
    apellido.value=a.apellido;
    peso.value=a.peso;
    altura.value=a.altura;
    editIndex.value=i;
}

function eliminar(i){
    if(confirm("¿Eliminar alumno?")){
        alumnos.splice(i,1);
        localStorage.setItem("alumnos",JSON.stringify(alumnos));
        mostrar();
    }
}

function mostrarRecomendacion(imc){
    let t=document.getElementById("tituloRec");
    let txt=document.getElementById("textoRec");
    let img=document.getElementById("imgIMC");
    recomendacion.style.display="block";

    if(imc<18.5){
        t.innerText="Bajo peso";
        txt.innerText="Se recomienda aumentar calorías saludables y proteína.";
        img.src="https://upload.wikimedia.org/wikipedia/commons/1/1c/BMI_underweight.svg";
    }else if(imc<25){
        t.innerText="Peso saludable";
        txt.innerText="Excelente, mantén una dieta balanceada y ejercicio regular.";
        img.src="https://upload.wikimedia.org/wikipedia/commons/5/51/BMI_normal.svg";
    }else{
        t.innerText="Sobrepeso";
        txt.innerText="Incrementa actividad física y controla consumo calórico.";
        img.src="https://upload.wikimedia.org/wikipedia/commons/3/38/BMI_overweight.svg";
    }
}

function limpiar(){
    document.querySelectorAll(".form_input").forEach(i=>i.value="");
}

mostrar();
</script>

</body>
</html>
