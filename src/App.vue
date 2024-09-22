<template>
  <n-message-provider>
    <n-layout class="layout">
      <n-layout-header>
        <n-tabs v-model:value="activeTab" type="line" style="width: 1920px; justify-content: flex-start;">
          <n-tab-pane name="upload" tab="上传文件测试" />
          <n-tab-pane name="url" tab="提交Url测试" />
          <n-tab-pane name="diskusage" tab="缓存管理" />
        </n-tabs>
      </n-layout-header>
      <n-layout-content>
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
.layout {
  width: 98vw;
  height: 98vh;
  position: fixed;
  top: 0;
  /* bottom: 10px; */
  left: 0;
  right: 0;
  margin: auto;
  background-color: white;
  border-radius: 0px;
  overflow: hidden;
}
</style>
