<template>
    <p>Count: {{ count }}</p>
    <div v-if="count > 5">Bravo vous avez cliqué plus de 5 fois</div>
    <div v-else>Vous n'avez pas cliqué plus de 5 fois</div>
    <button @click="count++">Increment</button>
    <button @click="count--">Decrement</button>
</template>

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>