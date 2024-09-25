<template>
    <n-layout>
        <n-data-table :columns="columns" :data="tasks" style="margin-top: 5px;" />
    </n-layout>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, defineExpose, h } from 'vue';
import { NLayout, NDataTable, NButton, NTag } from 'naive-ui';
import { DataTableColumns } from 'naive-ui'
const apiUrl = import.meta.env.VITE_BACKEND_URL;

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
            width: '12%',
        },
        {
            title: '状态',
            key: 'Status',
            width: '15%',
            render(row) {
                let colorType: 'default' | 'info' | 'error' | 'primary' | 'success' | 'warning' = 'info'; // 默认颜色
                if (row.Status === 'Running') colorType = 'info';
                else if (row.Status === 'Finished') colorType = 'success';
                else if (row.Status === 'Error') colorType = 'error';
                return h(NTag, { bordered: false, type: colorType }, { default: () => row.Status });
            }
        },
        {
            title: '分数',
            key: 'Score',
            width: '10%',
            render(row) {
                if (row.Status !== 'Finished') return null;
                let colorType: 'default' | 'info' | 'error' | 'primary' | 'success' | 'warning' = 'success'; // 默认颜色
                if (row.Score >= 70) colorType = 'success';
                else colorType = 'error';
                return h(NTag, { bordered: false, type: colorType }, { default: () => row.Score });
            }
        },
        {
            title: '是否通过',
            key: 'Info',
            width: '15%',
            render(row) {
                if (row.Status !== 'Finished') return null;
                let colorType: 'default' | 'info' | 'error' | 'primary' | 'success' | 'warning' = 'success'; // 默认颜色
                if (row.Score >= 70) colorType = 'success';
                else colorType = 'error';
                return h(NTag, { bordered: false, type: colorType }, { default: () => row.Info });
            }
        },
        {
            title: '结果详情',
            key: 'ResultFile',
            width: '20%',
            render(row) {
                if (row.Status !== 'Finished') return null;
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
            title: '错误（如果有）',
            key: 'Error',
        },
    ];
}

const tasks = ref<Task[]>([]);

export default defineComponent({
    components: {
        NLayout,
        NDataTable,
        NButton,
        NTag
    },
    setup(_, { emit }) {
        const columns = createColumns({
            show(row: Task) {
                emit('show-details', row.ID);
            }
        });

        const getTasks = async () => {
            const res = await fetch(`${apiUrl}/showTasks`);
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