<template>
    <div class="w-full bg-white h-2 rounded-full my-5 mb-16 sm:mb-5">
        <div class="bg-gray-500 rounded-3xl h-2" :style="`width:${timer}%`"></div>
    </div>
</template>

<script setup>
defineProps({
    timer: {
        type: Number,
        required: true,
    }
})</script>