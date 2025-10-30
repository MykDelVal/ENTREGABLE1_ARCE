[script.js](https://github.com/user-attachments/files/23222922/script.js)
// script.js - Simulador de Ahorros (Consola)
// Declaración de variables, constantes y arrays
const APP_NAME = "Simulador Ahorros";
let nombreUsuario = "";
let meses = 12;
let aportesMensuales = [];
const escenarios = [
  {nombre: "Optimista", ajuste: 1.02},
  {nombre: "Base", ajuste: 1.00},
  {nombre: "Pesimista", ajuste: 0.98}
];

// Función 1: entrada de datos (prompt) -> obtiene nombre, meses y aporte base
function obtenerDatosUsuario() {
  console.log("Iniciando " + APP_NAME);
  nombreUsuario = prompt("Ingrese su nombre:");
  if (!nombreUsuario) {
    nombreUsuario = "Usuario anónimo";
  }

  // pedir meses (validar con bucle while)
  let entradaMeses = prompt("¿Por cuántos meses quiere simular? (ej: 12)");
  while (!entradaMeses || isNaN(entradaMeses) || Number(entradaMeses) <= 0) {
    entradaMeses = prompt("Valor inválido. Ingrese un número de meses válido (ej: 12):");
  }
  meses = Math.floor(Number(entradaMeses));

  // pedir aporte base mensual
  let aporte = prompt("Ingrese el aporte mensual base en pesos (ej: 10000):");
  while (!aporte || isNaN(aporte) || Number(aporte) < 0) {
    aporte = prompt("Valor inválido. Ingrese un aporte mensual en números (ej: 10000):");
  }
  aporte = Number(aporte);

  // Llenar array de aportesMensuales (puede variar por mes)
  aportesMensuales = [];
  for (let i = 1; i <= meses; i++) {
    // ejemplo: aumento del aporte cada 3 meses
    let ajuste = 1 + Math.floor((i-1)/3) * 0.02; // +2% cada 3 meses
    aportesMensuales.push(Math.round(aporte * ajuste));
  }

  console.log("Datos cargados: Nombre:", nombreUsuario, "Meses:", meses, "Aportes ejemplo:", aportesMensuales.slice(0,4));
}

// Función 2: procesamiento - simula el ahorro para un escenario (entrada: tasa mensual, array de aportes)
function procesarSimulacion(tasaMensual, aportes) {
  // devuelve un objeto con detalle mensual y total
  let saldo = 0;
  const detalle = [];

  for (let mes = 0; mes < aportes.length; mes++) {
    // Interés aplicado al saldo previo
    let interes = saldo * tasaMensual;
    // aporte del mes
    let aporte = aportes[mes];
    // posible gasto inesperado (10% de probabilidad)
    let gasto = 0;
    if (Math.random() < 0.10) {
      gasto = Math.round(aporte * (0.3 + Math.random()*0.7)); // gasto entre 30% y 100% del aporte
    }
    // actualización de saldo con condicional
    saldo = saldo + interes + aporte - gasto;
    detalle.push({
      mes: mes + 1,
      aporte: aporte,
      interes: Math.round(interes),
      gasto: gasto,
      saldo: Math.round(saldo)
    });
  }

  return {saldoFinal: Math.round(saldo), detalle: detalle};
}

// Función 3: salida - mostrar resultados por consola y alert
function mostrarResultados(encabezado, resultado) {
  console.log("=== Resultado - " + encabezado + " ===");
  console.table(resultado.detalle);
  console.log("Saldo final:", resultado.saldoFinal);
  alert("[" + encabezado + "] " + nombreUsuario + ", saldo final aproximado: $" + resultado.saldoFinal + "\nRevisa la consola para ver el detalle mensual.");
}

// Función auxiliar: pedir tasa anual y convertir a mensual
function pedirTasaMensual() {
  let tasaAnual = prompt("Ingrese la tasa de interés anual esperada en % (ej: 36):");
  while (!tasaAnual || isNaN(tasaAnual)) {
    tasaAnual = prompt("Valor inválido. Ingrese la tasa anual en números (ej: 36):");
  }
  tasaAnual = Number(tasaAnual);
  // conversión a tasa mensual simple aproximada
  let tasaMensual = Math.pow(1 + tasaAnual/100, 1/12) - 1;
  return tasaMensual;
}

// Invocar las funciones principales y realizar simulaciones con ciclo y condicionales
function ejecutarSimulador() {
  obtenerDatosUsuario();

  // pedir si quiere probar varios escenarios
  let probarEscenarios = confirm("¿Desea simular automáticamente los escenarios (Optimista/Base/Pesimista)?");
  let tasaMensual = pedirTasaMensual();

  if (probarEscenarios) {
    for (let i = 0; i < escenarios.length; i++) {
      let esc = escenarios[i];
      // ajustar tasa según escenario
      let tasaAjustada = tasaMensual * esc.ajuste;
      let resultado = procesarSimulacion(tasaAjustada, aportesMensuales);
      mostrarResultados(esc.nombre + " (tasa mensual aprox. " + (tasaAjustada*100).toFixed(2) + "%)", resultado);
    }
  } else {
    // simular solo con la tasa ingresada
    let resultado = procesarSimulacion(tasaMensual, aportesMensuales);
    mostrarResultados("Personalizado", resultado);
  }

  console.log("Simulación finalizada para:", nombreUsuario);
}

// Ejecutar automáticamente al cargar el script
// También puedes invocar ejecutarSimulador() desde la consola
console.log("Cargado script.js — para iniciar el simulador ejecuta ejecutarSimulador() o recarga la página.");
ejecutarSimulador();




































