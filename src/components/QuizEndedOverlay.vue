<template>
    <!-- gameOver overlay -->
    <div class="fixed inset-0 z-10 bg-black/50 grid place-content-center">
        <div class="bg-green-400 rounded-md p-10 text-center w-60">
            <p class="text-xl font-semibold text-white">Game Over</p>
            <div class="text-lg flex justify-center items-center gap-2 text-white text-center">
                <p>Your Score is:</p>
                <p>{{ score }}</p>
            </div>
            <div class="flex gap-5 mt-6 justify-center">
                <button @click="restartGame" class="px-10 py-2 bg-blue-500 text-white rounded">Restart</button>
            </div>
        </div>
    </div>
</template>
<script setup>
defineProps({
    score:Number,
    restartGame:Function,
})
</script>