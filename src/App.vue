<template>
  <n-message-provider>
    <n-layout class="layout">

      <div class="button-row">
        <div class="button-group-left">
          <n-button @click="showAddTask = true" class="button" type="primary">
            新增任务
          </n-button>
          <n-button @click="showDiskUsageManagement = true" class="button" strong secondary type="primary">
            缓存管理
          </n-button>
        </div>
        <div class="button-group-right">
          <n-button @click="refreshTasks" class="button" type="primary" ghost>刷新任务列表</n-button>
        </div>
      </div>

      <n-modal v-model:show="showAddTask" class="custom-card" preset="card" :style="bodyStyle" title="新增任务" size="small"
        :bordered="false" :segmented="segmented">
        <n-layout-header>
          <n-tabs v-model:value="activeTab" type="line" style="justify-content: flex-start;">
            <n-tab-pane name="upload" tab="上传文件测试" />
            <n-tab-pane name="url" tab="提交Url测试" />
          </n-tabs>
        </n-layout-header>
        <n-layout-content>
          <component :is="activeTabComponent" :refresh-tasks="refreshTasks" />
        </n-layout-content>
      </n-modal>

      <n-modal v-model:show="showDiskUsageManagement" class="custom-card" preset="card" :style="bodyStyle" title="缓存管理"
        size="small" :bordered="false" :segmented="segmented">
        <n-layout-content>
          <DiskUsage :refresh-tasks="refreshTasks" />
        </n-layout-content>
      </n-modal>

      <Tasks ref="getTasksRef" />

    </n-layout>
  </n-message-provider>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import { NLayout, NMessageProvider, NButton, NModal, NTabs, NTabPane } from 'naive-ui';
import UploadFileTest from './components/UploadFileTest.vue';
import SubmitUrlTest from './components/SubmitUrlTest.vue';
import DiskUsage from './components/DiskUsage.vue';
import Tasks from './components/Tasks.vue';

export default defineComponent({
  components: {
    NLayout,
    NMessageProvider,
    NButton,
    NModal,
    NTabs,
    NTabPane,
    UploadFileTest,
    SubmitUrlTest,
    DiskUsage,
    Tasks
  },
  setup() {
    const activeTab = ref('upload');
    const showAddTask = ref(false);
    const showDiskUsageManagement = ref(false);
    const getTasksRef = ref();
    const activeTabComponent = computed(() => {
      return activeTab.value === 'upload' ? UploadFileTest : SubmitUrlTest;
    });
    const refreshTasks = () => {
      if (getTasksRef.value) {
        getTasksRef.value.getTasks(); // 调用子组件的 getTasks 方法
      }
    };
    return {
      bodyStyle: {
        width: '600px'
      },
      segmented: {
        content: 'soft',
        footer: 'soft'
      } as const,
      showAddTask,
      showDiskUsageManagement,
      activeTab,
      activeTabComponent,
      getTasksRef,
      refreshTasks
    };
  }
});
</script>

<style>
.layout {
  width: 98vw;
  height: 98vh;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  margin: auto;
  background-color: white;
  border-radius: 0px;
  overflow: hidden;
}

.button {
  margin-top: 10px;
  margin-bottom: 10px;
}

.button-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.button-group-left {
  display: flex;
  gap: 10px;
}

.button-group-right {
  display: flex;
}
</style>
