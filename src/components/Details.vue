<template>
    <n-modal v-model:show="showDetail" class="custom-card" preset="card" :style="bodyStyle" title="检测结果详情" size="small"
        :bordered="false" :segmented="segmented">
        <n-layout-content>
            <n-data-table :columns="columns" :data="tableData" />
        </n-layout-content>
    </n-modal>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NButton, NSpace, NDataTable } from 'naive-ui';

function createColumns() {
    return [
        {
            title: '检测项',
            key: 'name',
            width: 180,
        },
        {
            title: '问题类型',
            key: 'type'
        },
        {
            title: '扣除分数',
            key: 'score',
            width: 90
        },
    ]
}

interface TableDataItem {
    name: string;
    type: string;
    score: string;
}

export default defineComponent({
    props: {
        refreshTasks: {
            type: Function,
            required: true
        },
        show: {
            type: Boolean,
            required: true
        }
    },
    emits: ['update:show'],
    components: {
        NInput,
        NButton,
        NSpace,
        NDataTable,
    },
    setup(props, { emit }) {
        const debUrl = ref('');
        const id = ref('');
        const columns = createColumns();
        const tableData = ref<TableDataItem[]>([])
        let resultData = ref<string[][]>([]);

        function parseCSVToArray(csvText: string | any[]) {
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

        const resultJSON = () => {
            return resultData.value.map((item) => {
                return {
                    name: item[0],
                    type: item[1],
                    score: item[2]
                };
            });
        };

        const getDetail = async (taskId: string) => {
            id.value = taskId;
            const res = await fetch('http://127.0.0.1:12345/appTestResult?id=' + id.value);
            const csvText = await res.text();
            resultData.value = parseCSVToArray(csvText);
            resultData.value = resultData.value.slice(1, -1);
            tableData.value = resultJSON();
        };

        return {
            bodyStyle: {
                width: '600px'
            },
            segmented: {
                content: 'soft',
                footer: 'soft'
            } as const,
            getDetail,
            debUrl,
            id,
            resultData,
            columns,
            tableData,
            showDetail: {
                get: () => props.show,
                set: (value: boolean) => emit('update:show', value)
            }
        };
    }
});
</script>