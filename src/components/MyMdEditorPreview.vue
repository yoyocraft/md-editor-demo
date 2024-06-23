<template>
    <MdPreview :editorId="id" :modelValue="text" />
    <MdCatalog :editorId="id" :scrollElement="scrollElement" />
</template>

<script setup>
import { ref } from 'vue';
import { MdPreview, MdCatalog } from 'md-editor-v3';

const id = 'preview-only';
const text = ref('<a href="static/emoji/[doge].webp" target="_blank"><img src="static/emoji/[doge].webp" style="width: 2rem; display: inline-block;" /></a>');
const scrollElement = document.documentElement;
</script>