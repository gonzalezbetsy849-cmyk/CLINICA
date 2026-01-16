
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Sistema Profesional de IMC</title>

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
    margin-bottom:25px;
    font-size:2.6em;
}

.form{
    width:65%;
    margin:auto;
    background:#fff;
    padding:35px;
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
    margin-top:18px;
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
    margin:45px auto;
    border-collapse:collapse;
    background:#fff;
    box-shadow:0 5px 20px rgba(0,0,0,.2);
}

th,td{
    padding:12px;
    text-align:center;
    border:1px solid #ccc;
}

th{
    background:#A7E9AF;
}

.recomendacion{
    width:85%;
    margin:40px auto;
    background:#fff;
    padding:30px;
    border-radius:20px;
    box-shadow:0 5px 20px rgba(0,0,0,.2);
}

.recomendacion h2{
    color:#004E64;
    margin-bottom:15px;
}

.recomendacion ul{
    text-align:left;
    margin-top:15px;
}

.recomendacion li{
    margin-bottom:10px;
    line-height:1.6;
}

.links{
    text-align:center;
    margin-top:35px;
}

.links a{
    display:block;
    margin:8px;
    color:#006D5B;
    font-weight:600;
}
</style>
</head>

<body>

<h1>Sistema Inteligente de Evaluación IMC</h1>

<div class="form">
<input type="hidden" id="editIndex">

<label class="form_label">Alumno ID</label>
<input class="form_input" id="alumno_id">

<label class="form_label">Nombre</label>
<input class="form_input" id="nombre">

<label class="form_label">Apellido</label>
<input class="form_input" id="apellido">

<label class="form_label">Peso (kg)</label>
<input class="form_input" id="peso" type="number">

<label class="form_label">Altura (m)</label>
<input class="form_input" id="altura" type="number" step="0.01">

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
<p id="textoBase"></p>
<ul id="listaRec"></ul>
</div>

<div class="links">
<h3>🔗 Recursos de apoyo confiables</h3>
<a href="https://www.cdc.gov/healthyweight/spanish/index.html" target="_blank">✔ CDC – Peso saludable</a>
<a href="https://www.imss.gob.mx/salud-en-linea/nutricion" target="_blank">✔ IMSS – Nutrición</a>
<a href="https://www.healthline.com/nutrition/how-to-gain-weight" target="_blank">✔ Cómo subir de peso saludablemente</a>
<a href="https://www.mayoclinic.org/healthy-lifestyle/weight-loss" target="_blank">✔ Guía para bajar de peso – Mayo Clinic</a>
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
    recomendacion.style.display="block";
    listaRec.innerHTML="";

    if(imc<18.5){
        tituloRec.innerText="Resultado: Bajo peso";
        textoBase.innerText="Tu IMC indica que estás por debajo del rango saludable. Es importante mejorar tu nutrición de forma segura.";
        agregar([
            "Aumenta la ingesta de calorías saludables de forma gradual.",
            "Incluye proteínas de calidad: pollo, pescado, huevo y legumbres.",
            "Consume grasas buenas como aguacate, frutos secos y aceite de oliva.",
            "Realiza ejercicio de fuerza para ganar masa muscular.",
            "Mantén horarios regulares de comida.",
            "Consulta a un nutricionista para un plan personalizado."
        ]);
    }else if(imc<25){
        tituloRec.innerText="Resultado: Peso saludable";
        textoBase.innerText="Tu IMC se encuentra dentro del rango recomendado. Continúa con hábitos saludables.";
        agregar([
            "Mantén una alimentación balanceada.",
            "Realiza actividad física al menos 150 minutos por semana.",
            "Evita el exceso de azúcares y alimentos ultraprocesados.",
            "Hidrátate adecuadamente.",
            "Duerme entre 7 y 8 horas diarias.",
            "Realiza chequeos médicos periódicos."
        ]);
    }else{
        tituloRec.innerText="Resultado: Sobrepeso";
        textoBase.innerText="Tu IMC indica un peso por encima del rango saludable. Se recomienda adoptar cambios progresivos.";
        agregar([
            "Reduce el consumo de azúcares y grasas saturadas.",
            "Aumenta el consumo de frutas, verduras y fibra.",
            "Realiza actividad física aeróbica regularmente.",
            "Controla las porciones de comida.",
            "Evita bebidas azucaradas.",
            "Busca orientación profesional si es necesario."
        ]);
    }
}

function agregar(arr){
    arr.forEach(t=>{
        let li=document.createElement("li");
        li.innerText=t;
        listaRec.appendChild(li);
    });
}

function limpiar(){
    document.querySelectorAll(".form_input").forEach(i=>i.value="");
}

mostrar();
</script>

</body>
</html>
