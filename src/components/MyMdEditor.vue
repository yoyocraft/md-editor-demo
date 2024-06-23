<template>
  <div>
    <h1 class="header">
      看到小电视了吗？点一下
    </h1>

    <div>
      <MdEditor :toolbars="toolbars" v-model="localMdText">
        <template #defToolbars>
          <ModalToolbar title="BiliBili">
            <template #trigger>
              <div @click="showBiliVideoModal = true">
                <img src="../assets/bilibili.png" alt="bilibili" class="toolbar-icon" />
              </div>
            </template>
          </ModalToolbar>
        </template>
      </MdEditor>

      <!-- 模态框 -->
      <div v-if="showBiliVideoModal" class="modal-overlay">
        <div class="modal-content">
          <h2 class="modal-title">插入Bilibili视频</h2>
          <img src="../assets/video_url_guide.png" alt="guide" class="guide-image" />
          <textarea v-model="videoUrl" placeholder="输入Bilibili视频地址" class="modal-input" />
          <div class="modal-actions">
            <button @click="showBiliVideoModal = false" class="modal-button modal-cancel">取消</button>
            <button @click="insertVideo" class="modal-button modal-insert">插入视频</button>
          </div>
        </div>
        <div class="modal-backdrop" @click="showBiliVideoModal = false"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import { MdEditor, ModalToolbar } from 'md-editor-v3';
import { debounce } from 'lodash';
import 'md-editor-v3/lib/style.css';

const props = defineProps({
  mdText: {
    type: String,
    default: ''
  }
});

const emit = defineEmits(['update:mdText']);

const localMdText = ref(props.mdText);

const debouncedEmit = debounce((newVal) => {
  emit('update:mdText', newVal);
}, 300);

watch(localMdText, (newVal) => {
  debouncedEmit(newVal);
});

const showBiliVideoModal = ref(false);
const videoUrl = ref("");

const toolbars = [
  'bold', 'underline', 'italic', '-', 'title', 'strikeThrough', 'sub', 'sup',
  'quote', 'unorderedList', 'orderedList', 'task', '-', 'codeRow', 'code',
  'link', 'image', 0, 1, 'table', 'mermaid', 'katex', '-', 'revoke', 'next', 'save',
  '=', 'pageFullscreen', 'fullscreen', 'preview', 'previewOnly', 'catalog'
];

const parseVideoUrl = (originUrl) => {
  const parser = new DOMParser();
  const doc = parser.parseFromString(originUrl, 'text/html');
  const iframe = doc.querySelector('iframe');

  if (iframe) {
    iframe.setAttribute('width', '100%');
    iframe.setAttribute('height', '500');
    iframe.setAttribute('sandbox', 'allow-top-navigation allow-same-origin allow-forms allow-scripts');
    iframe.setAttribute('allowfullscreen', 'allowfullscreen');
    iframe.removeAttribute('border');
    iframe.removeAttribute('framespacing');
  }

  return iframe.outerHTML;
};

const insertVideo = () => {
  if (videoUrl.value) {
    const output = parseVideoUrl(videoUrl.value);
    localMdText.value += `\n${output}\n`;
    videoUrl.value = "";
    showBiliVideoModal.value = false;
  }
};
</script>

<style scoped>
.header {
  font-size: 1.875rem;
  font-weight: bold;
  text-decoration: underline;
}

.toolbar-icon {
  width: 24px;
  height: 24px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
}

.modal-content {
  background-color: white;
  padding: 1.5rem;
  border-radius: 6px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
  width: 24rem;
  max-width: 90%;
  z-index: 60;
  box-sizing: border-box;
}

.modal-title {
  font-size: 1.25rem;
  margin-bottom: 1rem;
}

.modal-input {
  border: 1px solid #d1d5db;
  padding: 0.5rem;
  width: 100%;
  margin-bottom: 1rem;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
}

.modal-button {
  padding: 0.5rem 1rem;
  border-radius: 4px;
  margin-left: 0.5rem;
}

.modal-cancel {
  background-color: #e5e7eb;
  color: black;
}

.modal-insert {
  background-color: #3b82f6;
  color: white;
}

.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 55;
}

.guide-image {
  max-width: 100%;
  height: auto;
  margin-bottom: 1rem;
}
</style>
