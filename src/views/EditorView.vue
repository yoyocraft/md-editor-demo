<script setup>
import MyMdEditor from '@/components/MyMdEditor.vue'
import { ref } from 'vue';

const mdText = ref("");
</script>

<template>
    <MyMdEditor v-model:mdText="mdText" />
</template>

<style scoped></style>
