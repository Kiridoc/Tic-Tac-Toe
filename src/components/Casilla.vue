<script setup lang="ts">
import {computed} from 'vue';

const props = defineProps({
    figura: String
})

const figuraClass = computed(()=>{
    return props.figura === '🔵' ? 'circulo' : props.figura === '❌' ? 'cruz' : '';
})

</script>


<template>
    <button :class="figuraClass">
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

</style>