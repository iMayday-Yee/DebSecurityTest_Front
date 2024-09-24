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
            //设置列宽
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
        }
    },
    components: {
        NInput,
        NButton,
        NSpace,
        NDataTable,
    },
    setup() {
        const debUrl = ref('');
        const id = ref('');
        const columns = createColumns();
        const tableData = ref<TableDataItem[]>([])
        const showDetail = ref(false);
        let resultData = ref<string[][]>([]);

        // 解析CSV文本为二维数组
        function parseCSVToArray(csvText: string | any[]) {
            const result = [];
            let insideQuote = false;
            let currentRow = [];
            let currentCell = '';
            for (let i = 0; i < csvText.length; i++) {
                const char = csvText[i];
                if (char === '"') {
                    // 遇到引号时，切换 insideQuote 状态
                    insideQuote = !insideQuote;
                } else if (char === ',' && !insideQuote) {
                    // 在引号外遇到逗号，结束当前单元格并推入当前行
                    currentRow.push(currentCell);
                    currentCell = '';
                } else if (char === '\n' && !insideQuote) {
                    // 在引号外遇到换行符，结束当前行并推入结果数组
                    currentRow.push(currentCell);
                    result.push(currentRow);
                    currentRow = [];
                    currentCell = '';
                } else {
                    // 其他字符正常加入当前单元格
                    currentCell += char;
                }
            }
            // 如果最后一行没有以换行符结束，手动结束最后一行
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

        const getDetail = async () => {
            const res = await fetch('http://127.0.0.1:12345/appTestResult?id=' + id.value);
            const csvText = await res.text();
            resultData.value = parseCSVToArray(csvText);
            resultData.value = resultData.value.slice(1, -1);
            tableData.value = resultJSON();  // 更新 ref 的值
        };

        return {
            bodyStyle: {
                width: '600px'
            },
            segmented: {
                content: 'soft',
                footer: 'soft'
            } as const,
            showDetail, getDetail, debUrl, id, resultData, columns, tableData
        };
    }
});
</script>
