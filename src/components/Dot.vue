<!-- white dot -->
<script setup lang="ts">
const props = defineProps<{ top: number | string; left: number | string }>()
</script>

<template>
    <div class="absolute bg-white rounded-full translate-x-[-50%] translate-y-[-50%] border-0.5 dot" :style="{
        top: typeof props.top === 'number' ? `${props.top}%` : props.top,
        left: typeof props.left === 'number' ? `${props.left}%` : props.left
    }" />
</template>


<style scoped>
.dot {
    width: 12px;
    height: 12px;
    box-shadow: rgba(0, 0, 0, 0.5) 0px 80px 80px,
        rgba(0, 0, 0, 0.25) 0px -20px 40px,
        rgba(0, 0, 0, 0.25) 0px 10px 20px,
        rgba(0, 0, 0, 0.3) 0px 20px 20px,
        rgba(0, 0, 0, 0.2) 0px -5px 10px,
        rgba(0, 0, 0, 0.15) 0px -2px 5px;
}
</style>