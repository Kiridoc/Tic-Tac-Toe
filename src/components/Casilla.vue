<script setup lang="ts">
import {computed} from 'vue';

const props = defineProps({
    figura: String,
    esGanadora: Boolean,
    oculta: Boolean,
    mostrar: Boolean
})

const figuraClass = computed(()=>{
    return props.figura === '🔵' ? 'circulo' : props.figura === '❌' ? 'cruz' : '';
})

const ganadoraClass = computed(() =>{
    return props.esGanadora ? 'casilla-ganadora' : '';
})

const ocultaClass = computed(() => {
    return props.oculta ? 'figura-oculta' : '';
})

const mostrarClass = computed(() => {
    return props.mostrar ? 'figura-mostrar' : '';
})

</script>


<template>
    <button :class="[figuraClass, ganadoraClass, ocultaClass, mostrarClass]">
        <span>{{ figura }}</span>
    </button>
</template>

<style scoped>
    button{
        width: 100px;
        height: 100px;
        font-size: 50px;
        background-color: transparent;
        border-color: #52438F;
        margin: 5px;
        cursor: pointer;
        transition: transform 0.2s ease;
        display: flex;
        justify-content: center;
        align-items: center;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        transition: box-shadow 0.3s ease, transform 0.2s ease;
    }

    button:hover{
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.4);
    }

    button:active{
        transform: scale(0.95);
    }
    
    /*Animaciones*/

    @keyframes drawCircle{
        0% {transform: scale(0);}
        100% {transform: scale(1);}
    }

    @keyframes drawCross{
        0% {transform: scale(0) rotate(0deg);}
        100% {transform: scale(1) rotate(90deg);}
    }

    .circulo span{
        animation: drawCircle 0.5s ease-out forwards;
    }

    .cruz span{
        animation: drawCross 0.5s ease-out forwards;
    }

    .casilla-ganadora{
        background-color: aquamarine;
        animation: ganarAnimacion 1s infinite;
        box-shadow: 0 0 20px #00d2ff;
    }

    @keyframes ganarAnimacion{
        0% {
            background-color: #00d2ff;
            box-shadow: 0 0 20px #00d2ff;
        }
        50% {
            background-color: #7a00ff;
            box-shadow: 0 0 20px #00d2ff;
        }
        100% {
            background-color: #00d2ff;
            box-shadow: 0 0 20px #00d2ff;
        }
    }

    .figura-oculta{
        animation: fadeOut 0.5s ease-out forwards;
    }

    .figura-mostrar{
        animation: fadeIn 0.5s ease-in forwards;
    }

    /*Animaciones de salida*/
    @keyframes fadeOut {
        from{
            opacity: 1;
            transform: scale(1);
        }
        to{
            opacity: 0;
            transform: scale(0);
        }
    }
    @keyframes fadeIn {
        from{
            opacity: 0;
            transform: scale(0);
        }
        to{
            opacity: 1;
            transform: scale(1);
        }
    }

    

</style>