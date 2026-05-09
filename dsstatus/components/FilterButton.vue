<script setup>
defineProps({
  label: String
})
</script>
<template>
    <button class="sm:p-0 sm:text-md md:text-xl rounded-lg shadow-md shadow-gray-500 backdrop-blur-sm font-medium inset-shadow-sm inset-shadow-olive-500 uppercase lg:p-2 bg-taupe-100 text-taupe-900 flex-auto hover:bg-mist-800 hover:text-mauve-100 hover:inset-shadow-none" >{{ label }}</button>
</template>