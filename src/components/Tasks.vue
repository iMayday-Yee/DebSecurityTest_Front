<template>
    <n-layout>
        <n-data-table :columns="columns" :data="tasks" style="margin-top: 5px;" />
    </n-layout>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, defineExpose, h } from 'vue';
import { NLayout, NDataTable, NButton } from 'naive-ui';
import type { DataTableColumns } from 'naive-ui'

interface Task {
    ID: string;
    Status: string;
    Score: number;
    Info: string;
    ResultFile: string;
    Error: string;
}

function createColumns({
    show
}: {
    show: (row: Task) => void
}): DataTableColumns<Task> {
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
            width: 400,
            render(row) {
                return h(
                    NButton,
                    {
                        strong: true,
                        tertiary: true,
                        size: 'small',
                        onClick: () => show(row)
                    },
                    { default: () => '查看详情' }
                )
            }
        },
        {
            title: '错误',
            key: 'Error',
        },
    ];
}

const tasks = ref<Task[]>([]);

export default defineComponent({
    components: {
        NLayout,
        NDataTable,
        NButton
    },
    setup(_, { emit }) {
        const columns = createColumns({
            show(row: Task) {
                emit('show-details', row.ID);
            }
        });

        const getTasks = async () => {
            const res = await fetch('http://127.0.0.1:12345/showTasks');
            const tasksJson = await res.json();
            tasks.value = tasksJson;
        };

        onMounted(() => {
            getTasks();
        });

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