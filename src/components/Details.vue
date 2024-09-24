<template>
    <n-data-table :columns="columns" :data="tableData" />
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';
import { NDataTable } from 'naive-ui';

export default defineComponent({
    props: {
        refreshTasks: {
            type: Function,
            required: true
        },
        taskId: {
            type: String,
            required: true
        }
    },
    components: {
        NDataTable,
    },
    setup(props) {
        const columns = [
            { title: '检测项', key: 'name', width: 180 },
            { title: '问题类型', key: 'type' },
            { title: '扣除分数', key: 'score', width: 90 }
        ];
        const tableData = ref<{ name: string; type: string; score: string; }[]>([]);

        const getDetail = async (id: string) => {
            if (!id) return;
            const res = await fetch('http://127.0.0.1:12345/appTestResult?id=' + id);
            const csvText = await res.text();
            const resultData = parseCSVToArray(csvText).slice(1, -1);
            tableData.value = resultData.map(item => ({
                name: item[0],
                type: item[1],
                score: item[2]
            }));
        };

        watch(() => props.taskId, (newId) => {
            if (newId) {
                getDetail(newId);
            }
        }, { immediate: true });

        function parseCSVToArray(csvText: string) {
            const result = [];
            let insideQuote = false;
            let currentRow = [];
            let currentCell = '';
            for (let i = 0; i < csvText.length; i++) {
                const char = csvText[i];
                if (char === '"') {
                    insideQuote = !insideQuote;
                } else if (char === ',' && !insideQuote) {
                    currentRow.push(currentCell);
                    currentCell = '';
                } else if (char === '\n' && !insideQuote) {
                    currentRow.push(currentCell);
                    result.push(currentRow);
                    currentRow = [];
                    currentCell = '';
                } else {
                    currentCell += char;
                }
            }
            if (currentCell || currentRow.length > 0) {
                currentRow.push(currentCell);
                result.push(currentRow);
            }
            return result;
        }

        return {
            columns,
            tableData
        };
    }
});
</script>