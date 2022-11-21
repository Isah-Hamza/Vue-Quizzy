<template>
    <div class="rounded-lg p-2 mb-10 neumorphic">
        <div class="bg-white p-5 text-center">
            <p class='font-semibold'>{{ currenQuestion.question }}
            </p>
        </div>
    </div>
</template>

<script setup>
defineProps({
    currenQuestion: {
        type: Object,
        required: true,
    }
});
</script>