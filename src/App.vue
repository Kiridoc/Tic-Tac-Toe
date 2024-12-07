<!--SCRIPT-->
<script setup lang="ts">
  import {computed, ref} from "vue";
  import Casilla from "./components/Casilla.vue"

  enum FIGURA{
    x= '❌',
    o= '🔵'
  }

  type CasillaTipo = {
    figura: string,
    esGanadora: boolean,
    oculta: boolean
    mostrar: boolean
  }

  const resultado = ref<string | null>(null) //Lo de las llaves es para definir que puede ser string o nulo, es algo de type script

  const turno = ref(FIGURA.x)

  const tablero = ref([
    [ {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} ],
    [ {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} ],
    [ {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} , {figura: '', esGanadora: false ,oculta: false, mostrar: false} ]
  ]);

  const reiniciar = () =>{
    for (let fila of tablero.value){
      for (let casilla of fila) {
        casilla.oculta = true;
      }
    }
    setTimeout(() =>{
      tablero.value = [
        [ {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} ],
        [ {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} ],
        [ {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} , {figura: '', esGanadora: false ,oculta: false, mostrar: true} ]
      ]
    }, 500)    
    for (let fila of tablero.value){
      for (let casilla of fila) {
        casilla.mostrar = false;
      }
    }  

    resultado.value = null;

    
  }

  const marcarCasillasGanadoras = (posiciones: number[][]) => {
    for( let [fila, columna] of posiciones) {
      tablero.value[fila][columna].esGanadora = true;
    }
  }


  const verificarVertical = (columna: number, figura:string)=>{
    let esGanador = true
    const posiciones: number[][] = []
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][columna].figura !== figura){
        esGanador = false
        break
      }else{
        posiciones.push([i, columna])
      }
    }
    if (esGanador) {
      marcarCasillasGanadoras(posiciones);
    }
    return esGanador
  }

  const verificarHorizontal = (fila: number, figura:string)=>{
    let esGanador = true
    const posiciones: number[][] = []
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[fila][i].figura !== figura){
        esGanador = false
        break
      }else{
        posiciones.push([fila, i])
      }
    }
    if (esGanador){
      marcarCasillasGanadoras(posiciones)
    }
    return esGanador
  }

  const verificarDiagonal1 = (figura:string)=>{
    let esGanador = true
    const posiciones: number[][] = []
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][i].figura !== figura){
        esGanador = false
        break
      }else{
        posiciones.push([i, i])
      }
    }
    if (esGanador){
      marcarCasillasGanadoras(posiciones)
    }
    return esGanador
  }
  const verificarDiagonal2 = (figura:string)=>{
    let esGanador = true
    const posiciones: number[][] = []
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][tablero.value.length - i - 1].figura !== figura){
        esGanador = false
        break
      }else{
        posiciones.push([i, tablero.value.length - i - 1])
      }
    }
    if (esGanador){
      marcarCasillasGanadoras(posiciones)
    }
    return esGanador
  }
  const verificarDiagonal = (figura:string)=>{
    return verificarDiagonal1(figura) || verificarDiagonal2(figura)
  }

  /*Si encuentro un espacio ocupado, no está vacío*/
  const tableroVacio = computed(()=>{
    for(let fila of tablero.value){
      for(let casilla of fila){
        if(casilla.figura !== ''){
          return false
        }
      }
    }
    return true
  })

  /*Si encuentro un espacio vacío, no está lleno*/
  const tableroLleno = computed(()=>{
    for(let fila of tablero.value){
      for(let casilla of fila){
        if(casilla.figura === ''){
          return false
        }
      }
    }
    return true
  })

  /*Para el sonido*/
  const playSound = (figura: string) => {
    let audio;
    if(figura === FIGURA.o){
      audio = new Audio('/sounds/circulo.mp3')
    }else if(figura === FIGURA.x){
      audio = new Audio('/sounds/cruz.mp3');
    }
    if(audio){
      audio.play()
    }
  }

  const cambiarEstadoCasilla = (fila:number, columna:number)=>{ 
    if(!tablero.value[fila][columna].figura && resultado.value == null){
      tablero.value[fila][columna].figura = turno.value
      playSound(turno.value); //Reproduce el sonido
      turno.value = FIGURA.x === turno.value ? FIGURA.o : FIGURA.x

      //Verificar si ganó
      const figuraActual = tablero.value[fila][columna].figura;
      if(verificarVertical(columna, figuraActual) ||
      verificarHorizontal(fila, figuraActual) ||
      verificarDiagonal(figuraActual)
      ){
        resultado.value = `GANÓ ${figuraActual}`
        let audio = new Audio('/sounds/win.mp3')
        audio.play()
      }
      else if(tableroLleno.value){
        resultado.value = 'EMPATE'
        let audio = new Audio('/sounds/empate.mp3')
        audio.play()
      }
    }
  }

  const cambiarTurno = (figura:FIGURA)=>{
    if(tableroVacio.value){
      turno.value = figura;
    }
  }

  const activarBoton = (figura:string)=>{
    return figura === turno.value ? 'btn-activo':'';
  }

</script>

<!--TEMPLATE-->
<template>

  <div class="contenedor">
    <h1>Tic Tac Toe</h1>

    <h3
      v-show="resultado"
      style="color: #fff;">Resultado: {{ resultado }}</h3>
    
    <div class="fila" v-for="(fila,indexF) in tablero" :key="indexF">
      <Casilla
       v-for="(casilla,indexC) in fila"
       :key="indexC"
       :figura="casilla.figura"
       :esGanadora="casilla.esGanadora"
       :oculta="casilla.oculta"
       :mostrar="casilla.mostrar"
       @click="cambiarEstadoCasilla(indexF,indexC)"
       />

    </div>

    <div class="fila" style="margin-top: 20px;">
      <button class="custom-button"
       @click="reiniciar">Reiniciar
      </button>

      <button class="custom-button"
        :class="activarBoton(FIGURA.x)"
       @click="cambiarTurno(FIGURA.x)">{{ FIGURA.x }}
      </button>

      <button class="custom-button"
        :class="activarBoton(FIGURA.o)"
       @click="cambiarTurno(FIGURA.o)">{{ FIGURA.o }}
      </button>
    </div>
  </div>

</template>

<!--STYLES-->
<style scoped>
  *{
    font-family: 'Press Start 2P', sans-serif;
  }

  h1{
    color: #fff;
  }

  .fila{
    width: 350px;
    display: flex;
    justify-content: center;
  }

  .contenedor{
    display: flex;
    width: 100%;
    height: 90vh;
    flex-direction: column;
    justify-content: center; /*centrar vertical*/
    align-items: center; /*centrar horizontal*/
    animation: fadeIn 1s ease;
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to{
      opacity: 1;
    }
  }

  .custom-button{
    width: auto;
    font-size: 25px;
    background-color: #7a00ff;
    border: none;
    border-radius: 10px;
    margin: 5px;
    color: #fff;
    padding: 10px 20px;
    cursor: pointer;
    transition: transform 0.2s ease, background-color 0.3s ease, box-shadow 0.3s ease;
    box-shadow: 0 4px 8px rgba(95, 158, 160, 0.2);
  }

  .custom-button:hover{
    background-color: #48D1CC;
    box-shadow: 0 4px 16px rgba(95, 158, 160, 0.4);
  }

  .custom-button:active{
    transform: scale(0.95);
  }

  .btn-activo{
    background-color: #48D1CC;
    box-shadow: 0 4px 16px rgba(95, 158, 160, 0.4);
    border: 2px solid #FFD700;
    color: #FFD700;
  }

</style>
