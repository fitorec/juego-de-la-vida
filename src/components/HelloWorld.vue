<template>
  <div class="hello">
    <h1>Juego de la vida</h1>
    <h3 v-if="step === 0">
      Tablero de
      <input type='number' v-model="cols" min="2" max="50" step="1"> x 
      <input type='number' v-model="rows" min="2" max="50" step="1">
    </h3>
    <button @click="crearTablero" v-if="step === 0">Generar tablero</button>
    <h3 v-if="step > 0">
      Tablero de {{ cols }} x {{ rows }}, población {{ poblacion }}
    </h3>
    <div id="tablero" v-if="step >  0 ">
      <div v-for="fila in parseInt(rows, 10)" :key="fila" class="fila">
        <Celula
          v-for="col in parseInt(cols, 10)" :key="col"
          :viva="celulas[(fila * rows) + col]"
          @click="changeEdo(fila, col)"
        />
      </div>
      <button @click="next()" v-if="step === 1" class="start">Iniciar</button>
      <h3 v-if="step == 2">
        Estamos en la generación {{ generacion }}
      </h3>
      <button @click="next()" v-if="step === 2" class="stop">Detener</button>
    </div>
    <h3>Reglas del juego de la vida:</h3>
    <ol>
      <li>Una celula muerta que tiene 3 celulas vecinas vivas nace.</li>
      <li>Una celula viva con 2 o 3 celulas vecinas vivas segurá viva.</li>
      <li>Cualquier otra cantidad de células vecinas vivas, matará a la célula por sobre población o por soledad.</li>
    </ol>
  </div>
</template>

<script>
import Celula from './celula'
import { ref, reactive } from 'vue' 

export default {
  components: {Celula},
  setup() { // props, context
    const generacion = ref(0);
    const poblacion = ref(0);
    const cols = ref(10);
    const rows = ref(10);
    const iniciado = ref(false);
    const step = ref(0);
    const celulas = reactive({data: []});
    
    const siguienteGeneracion = () => {
      poblacion.value = 0;
      for(let f = 0; f< rows.value; f++) {
        for(let c = 0; c< cols.value; c++) {
          const index = ((f) * cols.value ) + c;
          const edo = celulas[index];
          if (edo) {
            console.log("el elemento tiene el valor true " + index);
          }
          const nvecinos = numvecinos(f, c);
          // Una celula muerta que tiene 3 celulas vecinas vivas nace
          if (!edo && nvecinos === 3) {
            celulas[index] = true;
            poblacion.value ++;
          } else if (edo && (nvecinos === 3 || nvecinos === 2)) {
            // Una celula viva con 2 o 3 celulas vecinas vivas segurá viva.
            celulas[index] = true;
            poblacion.value ++;
          } else {
            celulas[index] = false;
          }
        }
      }
      console.log('siguienteGeneracion');
      if (step.value === 2) {
        generacion.value++;
        setTimeout(function(){ siguienteGeneracion(); }, 1000);
      }
    };
    const crearTablero = () => {
      poblacion.value = 0;
      generacion.value = 0;
      celulas.data.slice(0, celulas.length);
      for(let i = 0; i< rows.value; i++) {
        for(let j = 0; j< cols.value; j++) {
          celulas.data.push(false);
        }
      }
      step.value++;
    }
    const next = () => {
      step.value++;
      if (step.value === 1) {
        return;
      }
      if (step.value === 2) {
        siguienteGeneracion();
        return ;
      }
      step.value = 0;
    }
    const edo = (f, c) => {
      const index = ((f) * cols.value ) + c;
      if (celulas.length < index) {
        celulas.push(false);
        return false;
      }
      return celulas[index];
    };
    const changeEdo = (f, c) => {
      if (step.value !== 1) {
        return ;
      }
      const index = ((f) * cols.value ) + c;
      celulas[index] = !celulas[index];
      if (celulas[index]) {
        poblacion.value++
      } else {
        poblacion.value--;
      }
      console.log("numero de vecinos: " + numvecinos(f, c));
    };
    const numvecinos = (fila, column) => {
      // una celula solo puede tener 4 vecinos
      let num = 0;
      if (fila>0) { // top
        const topIndex = ((fila - 1) * cols.value) + column;
        if (celulas[topIndex]) {
          num++;
        }
        if (column>0) {
          const topLeft = ((fila - 1) * cols.value) + column - 1;
          if (celulas[topLeft]) {
            num++;
          }
        }
        if (column < cols.value) {
          const topRight = ((fila - 1) * cols.value) + column + 1;
          if (celulas[topRight]) {
            num++;
          }
        }
      }
      if (fila < rows.value) { // bottom
        const bIndex = ((fila + 1) * cols.value) + column;
        if (celulas[bIndex]) {
          num++;
        }
        if (column>0) {
          const bLeft = ((fila + 1) * cols.value) + column - 1;
          if (celulas[bLeft]) {
            num++;
          }
        }
        if (column < cols.value) {
          const bRight = ((fila + 1) * cols.value) + column + 1;
          if (celulas[bRight]) {
            num++;
          }
        }
      }
      if (column > 0) { // left
        const lIndex = (fila * cols.value) + column -1;
        if (celulas[lIndex]) {
          num++;
        }
      }
      if (column < cols.value) { // rigth
        const rIndex = (fila * cols.value) + column + 1;
        if (celulas[rIndex]) {
          num++;
        }
      }
      return num;
    }
    return {
      cols, rows, next, iniciado, celulas, edo,
      changeEdo, step, generacion, poblacion, crearTablero
    };
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.fila {
  text-align: center;
  height: 20px;
  background: #777;
  padding: 0;
  margin: 0;
}
button {
  border-radius: 1rem;
  background: #0093FF;
  color: white;
  display: block;
  width: 100%;
  font-size: 2rem;
}
.stop {
  background: #952100;
}
ol {
  list-style: none;
  counter-reset: my-awesome-counter;
}
ol li {
  display: block;
  counter-increment: my-awesome-counter;
}
ol li::before {
  content: counter(my-awesome-counter) ". ";
  color: red;
  font-weight: bold;
}
</style>
