<template>
    <div
        class="option-default relative p-2 py-1 font-bold flex items-center gap-5 rounded-md shadow mt-4 bg-green-600 text-white cursor-pointer">
        <!-- bonus -->
        <div class="absolute bg-blue-500 text-white right-0 top-0 p-2 rounded rotate-45">
            <p class="font-bold -rotate-45 text-sm">+10</p>
        </div>
        <!-- option id -->
        <div class="bg-gray-600 text-white p-3 px-4 rounded">{{ idx }}</div>
        <!-- option text -->
        <div class="font-semibold">{{ choice }}</div>
    </div>
</template>

<script setup>
defineProps({
    idx: Number,
    choice: String,
});
</script>