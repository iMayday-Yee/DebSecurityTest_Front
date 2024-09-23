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
        <n-data-table :columns="columns" :data="tasks" />
        <n-button @click="getTasks">刷新任务列表</n-button>
      </n-layout-content>
    </n-layout>
  </n-message-provider>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import { NLayout, NLayoutHeader, NLayoutContent, NTabs, NTabPane, NMessageProvider, NDataTable, NButton } from 'naive-ui';
import UploadFileTest from './components/UploadFileTest.vue';
import SubmitUrlTest from './components/SubmitUrlTest.vue';
import DiskUsage from './components/DiskUsage.vue';

function createColumns() {
  return [
    {
      title: 'ID',
      key: 'ID',
      width: 200
    },
    {
      title: '状态',
      key: 'Status',
      width: 250
    },
    {
      title: '分数',
      key: 'Score',
      width: 180
    },
    {
      title: '是否通过',
      key: 'Info',
      width: 300
    },
    {
      title: '结果详情',
      key: 'ResultFile',
      width: 400
    },
    {
      title: '错误',
      key: 'Error',
    },
  ]
}

interface Task {
  ID: string;
  Status: string;
  Score: number;
  Info: string;
  ResultFile: string;
  Error: string;
}

const tasks = ref<Task[]>()


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
    DiskUsage,
    NDataTable,
    NButton
  },
  setup() {
    const activeTab = ref('upload');
    const columns = createColumns();
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
    const getTasks = async () => {
      const res = await fetch('http://127.0.0.1:12345/showTasks');
      const tasksJson = await res.json();
      tasks.value = tasksJson;  // 更新 ref 的值
    };
    onMounted(() => {
      getTasks();
    });
    return { activeTab, activeTabComponent, columns, getTasks, tasks };
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
