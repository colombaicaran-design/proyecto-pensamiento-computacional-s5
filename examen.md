
# TOCOMPLE SIMULATOR
**Simulador chileno de gestión y armado de completos interactivo en 2D.**

* **Enlace al proyecto:** [https://editor.p5js.org/colombaicaran/sketches/tk7a7RpBh](https://editor.p5js.org/colombaicaran/sketches/tk7a7RpBh)

---

## Referencia de origen / bibliografía
* **Inspiración mecánica:** *Papa's Pizzeria* (y la saga de juegos de cocina *Papa's*), desarrollados por Flipline Studios (Matt Neff y Tony Solary, 2006).
* **Inspiración temática:** La cultura popular chilena, las picadas de completos y el humor criollo en torno al "italiano" perfecto.

---

<img width="286" height="176" alt="examen" src="https://github.com/user-attachments/assets/228cff2b-7ffc-4696-b50b-fd40fe962b2b" />

---

## 👥 Integrantes
* **Colomba Icaran** - [colombaicaran-design](https://github.com/colombaicaran-design)
* **Iñaki Aristia** - [inakiariztia](https://github.com/inakiariztia) 

---

## 📖 Relato inicial
El negocio de los completos es rudo, pero es el sueño de todo chileno emprendedor. Eres el dueño de un carrito de completos "Tocomple" y tu misión es simple pero estresante: atender a la variada fauna urbana que transita por el centro. Desde el **Estudiante** hambriento que busca bajón, pasando por el **"Pelao"** exigente que paga bien pero no perdona errores, hasta la **Abuelita Exigente** que vigila cada paso de la preparación. 

Para consolidar tu pyme y no quedar "pato" (en la quiebra), tendrás que dominar el arte de la precisión al lanzar la vienesa, el tomate y la palta sin que el completo se te desarme en el mesón, para luego coronarlo con un zigzag perfecto de tu salsa dispensadora favorita. ¿Lograrás juntar las 10 lucas de la meta o terminarás vendiendo mermas vencidas?

---

## Storyboard
A continuación se presenta el flujo de la interacción y la experiencia del usuario a través de las distintas pantallas de la cocina:

<img width="720" height="960" alt="holiiiii" src="https://github.com/user-attachments/assets/5d080b69-ad37-4210-b731-1637e719c44c" />

---

## ⚙️ Estados del Sistema (Máquina de Estados)
El simulador cuenta con un sistema de **9 estados y sub-estados lógicos** determinados por la variable global `escena` y las banderas de control internas. Esto estructura el flujo estricto de la interacción paso a paso.

### Condición de Salida General:
El juego entra en un bucle continuo de atención al cliente hasta que el jugador avanza en la pantalla de resultados (`escena === 4`). En ese momento, se evalúa si el dinero acumulado llegó a la meta estipulada (`dineroAcumulado >= metaDinero`) o cayó a números negativos, gatillando el estado final de quiebra o victoria empresarial (`escena = 5`).

---

### Estado 1: Menú Principal (Inicio)
* **Comportamiento:** Pantalla de bienvenida que inicializa el juego. El sistema ejecuta la animación automática de un completo rebotando en las esquinas de la pantalla a modo de salvapantallas.
* **Condición de salida:** Hacer clic en el botón con el texto "¡ABRIR LOCAL Y ATENDER!".
* **Código clave:**
```js
function dibujarEscena0() {
  // Animación del completo rebotando
  x += velX; y += velY;
  if (x + anchoComp/2 > width || x - anchoComp/2 < 0) velX *= -1;
  if (y + altoComp/2 > height - 150 || y - altoComp/2 < height * 0.3) velY *= -1;
  
  dibujarCompleto(x, y, estadoSimulador);
  // Botón de salida detectado en mousePressed
}
 Estado 2: Recepción del Cliente y Pedido
Comportamiento: Se gatilla la función aleatoria para seleccionar el tipo de cliente (Estudiante, Abuelita, Pelao) y su salsa solicitada. El flujo se detiene para permitir la lectura del pedido.

Condición de salida: Hacer clic en el botón "¡IR A LA COCINA!".

Código clave:

JavaScript
function seleccionarNuevoCliente() {
  tipoCliente = int(random(0, 3)); 
  let salsas = ["Mayo", "Ketchup", "Mostaza"];
  pedidoSalsa = random(salsas);
  // Define nombres de clientes: Estudiante, Abuelita Exigente, o pelao
}
Estado 3: Cocina - Armado Activo (Precisión)
Comportamiento: El jugador debe añadir la vienesa, el tomate y la palta de forma correlativa. Una barra oscila horizontalmente en el medidor de precisión.

Condición de salida: Completar los 3 ingredientes con éxito (pasa a Estado 5) o fallar el tiro en la barra (pasa a Estado 4).

Código clave:

JavaScript
if (escena === 2 && ingredienteActual <= 3 && key === ' ') {
  if (barraPrecision < limiteMin || barraPrecision > limiteMax) {
    completoCaido = true; // El completo se cae e interrumpe el armado
  } else {
    ingredienteActual++; // Avanza al siguiente ingrediente (Vienesa -> Tomate -> Palta)
    barraPrecision = 0;  
  }
}
 Estado 4: Cocina - Penalización por Completo Caído
Comportamiento: Bloqueo de la estación de armado. Informa al jugador la pérdida del material base y congela los marcadores.

Condición de salida: Presionar la BARRA ESPACIO para botar la merma, cobrando una multa automática de $500 y reiniciando el flujo al Estado 2.

Código clave:

JavaScript
if (escena === 2 && completoCaido && key === ' ') { 
  dineroAcumulado -= 500; 
  if (dineroAcumulado < 0) escena = 5; 
  else resetearMesaParaSiguienteCliente(); 
}
Estado 5: Cocina - Dispensador de Salsas
Comportamiento: Habilita el dibujo libre continuo en la lona interactiva. El jugador usa M, K, o A para cambiar de color y el arrastre del mouse genera los vectores de salsa.

Condición de salida: Hacer clic en "¡SÍRVELO AL CLIENTE!" o agotarse el tiempo del temporizador de cocina.

Código clave:

JavaScript
if (mouseIsPressed && dist(mouseX, mouseY, width/2, height * 0.52) < 55) {
  puntosSalsaX.push(mouseX); 
  puntosSalsaY.push(mouseY);
  nombresSalsaDibujada.push(tipoSalsaActual); 
}
 Estado 6: Transición Matemática de Auditoría
Comportamiento: Cálculo analítico en backend (no gráfico). Revisa la correspondencia entre los vectores creados en el lienzo de dibujo contra las exigencias iniciales guardadas del cliente.

Condición de salida: Clasificación automática de la calidad del producto y redirección inmediata al Estado 7.

JavaScript
let ratioSalsaCorrecta = nombresSalsaDibujada.length > 0 ? aciertosSalsa / nombresSalsaDibujada.length : 0;
if (ratioSalsaCorrecta < 0.6) {
  puntajeFinal = 20; // Penalización severa por salsa incorrecta
}
 Estado 7: Pantalla de Calificación y Resultados
Comportamiento: Despliegue de los diálogos de retroalimentación criollos del cliente, impresión del puntaje, adjudicación del dinero base ($2000) más las propinas variables calculadas.

Condición de salida: Hacer clic en el botón inferior "Siguiente Cliente ➡️".

JavaScript
// Renderizado de strings de texto dinámicos según el cálculo previo
text("CALIFICACIÓN: " + puntajeFinal + " PTS", width/2, height * 0.57);
text("PROPINA: $" + propinaFinal, width/2, height * 0.61);
Estado 8: Tienda de Mejoras (Upgrades de Local)
Comportamiento: Interfaz económica interactiva embebida. Permite al usuario mutar variables de control físicas globales del entorno (tamaño del área verde de la barra o segundos de margen en el reloj) gastando su capital acumulado.

Condición de salida: Pasiva por interacción con los botones o definitiva al presionar "Siguiente Cliente" para pasar al ciclo de juego regular.

JavaScript
if (!tieneMesonAcero && dineroAcumulado >= 1500) { 
  dineroAcumulado -= 1500; 
  tieneMesonAcero = true; // Altera el tamaño de la zona verde en el Estado 3
}
 Estado 9: Fin de Partida (Game Over / Victoria Corporativa)
Comportamiento: Evalúa los extremos del negocio. Si el dinero es igual o mayor a $10,000 imprime la consolidación de la empresa; si está en bancarrota muestra la pantalla de quiebra.

Condición de salida: Hacer clic en "VOLVER A EMPEZAR", limpiando todos los arreglos, flags de mejoras y capital para volver a la escena = 0.

Código clave:

JavaScript
if (dineroAcumulado >= metaDinero) {
  fill(46, 204, 113); text("¡EMPRESA CONSOLIDADA!", width/2, height * 0.49);
} else {
  fill(231, 76, 60); text("¡QUEDASTE PATO!", width/2, height * 0.49);
}
💻 Muestra de Control General de la Máquina de Estados
El motor gráfico regula y segrega estas lógicas mediante el iterador cíclico estructural de la función draw() en p5.js:

JavaScript
function draw() {
  background(255); 

  if (escena === 0) dibujarEscena0();                          // Estado 1
  else if (escena === 1) dibujarEscena1();                     // Estado 2
  else if (escena === 2) { manejarTimer(); dibujarEscena2(); } // Controla Estados 3 y 4
  else if (escena === 3) { manejarTimer(); dibujarEscena3(); } // Estado 5
  else if (escena === 4) dibujarEscena4();                     // Controla Estados 7 y 8
  else if (escena === 5) dibujarEscenaGameOver();              // Estado 9

  if (escena > 0 && escena < 5) {
    dibujarInterfazEconomica();
  }
}
