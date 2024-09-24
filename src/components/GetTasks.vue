<template>
    <n-layout>
        <n-data-table :columns="columns" :data="tasks" style="margin: 5px;" />
    </n-layout>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, defineExpose } from 'vue';
import { NLayout, NDataTable } from 'naive-ui';

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
    ];
}

interface Task {
    ID: string;
    Status: string;
    Score: number;
    Info: string;
    ResultFile: string;
    Error: string;
}

const tasks = ref<Task[]>([]);

export default defineComponent({
    components: {
        NLayout,
        NDataTable,
    },
    setup() {
        const columns = createColumns();

        const getTasks = async () => {
            const res = await fetch('http://127.0.0.1:12345/showTasks');
            const tasksJson = await res.json();
            tasks.value = tasksJson;  // 更新 ref 的值
        };

        onMounted(() => {
            getTasks();
        });

        // 将 getTasks 暴露给父组件
        defineExpose({ getTasks });

        return {
            columns,
            getTasks,
            tasks
        };
    }
});
</script>

<style>
/* 这里可以添加样式 */
</style>