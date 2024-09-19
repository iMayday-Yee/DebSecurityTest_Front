<template>
  <n-layout>
    <n-layout-header>
      <n-tabs v-model:value="activeTab">
        <n-tab-pane name="upload" tab="上传文件测试" />
        <n-tab-pane name="url" tab="提交Url测试" />
      </n-tabs>
    </n-layout-header>
    <n-layout-content>
      <component :is="activeTabComponent" />
    </n-layout-content>
  </n-layout>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import { NLayout, NLayoutHeader, NLayoutContent, NTabs, NTabPane } from 'naive-ui';
import UploadFileTest from './components/UploadFileTest.vue';
import SubmitUrlTest from './components/SubmitUrlTest.vue';

export default defineComponent({
  components: {
    NLayout,
    NLayoutHeader,
    NLayoutContent,
    NTabs,
    NTabPane,
    UploadFileTest,
    SubmitUrlTest
  },
  setup() {
    const activeTab = ref('upload');
    const activeTabComponent = computed(() => {
      return activeTab.value === 'upload' ? UploadFileTest : SubmitUrlTest;
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