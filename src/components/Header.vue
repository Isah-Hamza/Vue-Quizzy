<template>
    <div class="flex items-center justify-between">
        <!-- title -->
        <p class="text-lg font-bold text-black">QUIZZY</p>
        <!-- score -->
        <div class="text-sm bg-white rounded-md px-5 py-1 w-fit flex items-center gap-2 ml-auto">
            <p>SCORE:</p>
            <span class="text-lg font-bold">{{ score }}</span>
        </div>
    </div>
</template>

<script setup>
defineProps({
    score: Number
});
</script>