<template>
    <div class="grid gap-1 justify-center text-center mt-7">
        <p class="text-sm">
            <span class="font-bold">{{ questionCounter }}</span>
            <span class="font-bold">/</span>
            <span class="font-bold">{{ questions.length }}</span>
        </p>
        <div class="w-16 h-1.5 rounded bg-black"></div>
    </div>
</template>

<script setup>
defineProps({
    questionCounter: {
        type: Number,
        required: true
    },
    questions: {
        type: Array,
        required: true
    }
});
</script>