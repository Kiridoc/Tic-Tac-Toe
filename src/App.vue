<!--SCRIPT-->
<script setup lang="ts">
  import {computed, ref} from "vue";
  import Casilla from "./components/Casilla.vue"

  enum FIGURA{
    x= '❌',
    o= '🔵'
  }

  const resultado = ref<string | null>(null) //Lo de las llaves es para definir que puede ser string o nulo, es algo de type script

  const turno = ref(FIGURA.x)

  const tablero = ref([
    ['','',''],
    ['','',''],
    ['','','']
  ]);

  const reiniciar = () =>{
    tablero.value = [
    ['','',''],
    ['','',''],
    ['','','']  
    ]
    resultado.value = null
  }

  const verificarVertical = (columna: number, figura:string)=>{
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][columna] !== figura){
        return false
      }
    }
    return true
  }

  const verificarHorizontal = (fila: number, figura:string)=>{
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[fila][i] !== figura){
        return false
      }
    }
    return true
  }

  const verificarDiagonal1 = (figura:string)=>{
    let j = 0
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][j] !== figura){
        return false
      }
      j++
    }
    return true
  }
  const verificarDiagonal2 = (figura:string)=>{
    let j = 2
    for(let i = 0; i < tablero.value.length; i++){
      if(tablero.value[i][j] !== figura){
        return false
      }
      j--
    }
    return true
  }
  const verificarDiagonal = (figura:string)=>{
    if(verificarDiagonal1(figura) || verificarDiagonal2(figura)){
      return true
    }
    else{ return false }
  }

  /*Si encuentro un espacio ocupado, no está vacío*/
  const tableroVacio = computed(()=>{
    for(let fila of tablero.value){
      for(let columna of fila){
        if(columna !== ''){
          return false
        }
      }
    }
    return true
  })

  /*Si encuentro un espacio vacío, no está lleno*/
  const tableroLleno = computed(()=>{
    for(let fila of tablero.value){
      for(let columna of fila){
        if(columna === ''){
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
    if(!tablero.value[fila][columna] && resultado.value == null){
      tablero.value[fila][columna] = turno.value
      playSound(turno.value); //Reproduce el sonido
      turno.value = FIGURA.x === turno.value ? FIGURA.o : FIGURA.x

      //Verificar si ganó
      if(verificarVertical(columna, tablero.value[fila][columna]) ||
      verificarHorizontal(fila, tablero.value[fila][columna]) ||
      verificarDiagonal(tablero.value[fila][columna])
      ){
        resultado.value = `GANÓ ${tablero.value[fila][columna]}`
        let audio = new Audio('/sounds/win.mp3')
        audio.play()
      }
      else if(tableroLleno.value){
        resultado.value = 'EMPATE'
        let audio = new Audio('/sounds/empate.mp3')
        audio.play()
      }
  

      if(tableroLleno.value){
       console.log('TABLERO LLENO')
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
    
    <div class="fila" v-for="(fila,indexF) in tablero">
      <Casilla
       v-for="(columna,indexC) in fila"
       :figura="columna"
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
    background-color: transparent;
    border-color: #52438F;
    margin: 5px;
    color: #fff;
    padding: 10px;
    cursor: pointer;
    transition: transform 0.2s ease;
  }

  .btn-activo{
    background-color: #42B883;
    transform: scale(0.95);
  }
</style>
