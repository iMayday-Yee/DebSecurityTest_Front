<template>
  <n-message-provider>
    <n-layout>
      <n-layout-header>
        <n-tabs v-model:value="activeTab">
          <n-tab-pane name="upload" tab="上传文件测试" />
          <n-tab-pane name="url" tab="提交Url测试" />
          <n-tab-pane name="diskusage" tab="缓存管理" />
        </n-tabs>
      </n-layout-header>
      <n-layout-content class="content">
        <component :is="activeTabComponent" />
      </n-layout-content>
    </n-layout>
  </n-message-provider>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import { NLayout, NLayoutHeader, NLayoutContent, NTabs, NTabPane, NMessageProvider } from 'naive-ui';
import UploadFileTest from './components/UploadFileTest.vue';
import SubmitUrlTest from './components/SubmitUrlTest.vue';
import DiskUsage from './components/DiskUsage.vue';

export default defineComponent({
  components: {
    NLayout,
    NLayoutHeader,
    NLayoutContent,
    NTabs,
    NTabPane,
    NMessageProvider,
    UploadFileTest,
    SubmitUrlTest,
    DiskUsage
  },
  setup() {
    const activeTab = ref('upload');
    const activeTabComponent = computed(() => {
      switch (activeTab.value) {
        case 'upload':
          return UploadFileTest;
        case 'url':
          return SubmitUrlTest;
        case 'diskusage':
          return DiskUsage;
        default:
          return UploadFileTest;
      }
    });
    return { activeTab, activeTabComponent };
  }
});
</script>

<style>
.content {
  padding: 20px;
  width: 100%;
  box-sizing: border-box;
  /* 确保内边距包含在元素的总宽度和高度内 */
}
</style>