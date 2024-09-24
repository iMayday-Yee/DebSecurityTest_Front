<template>
    <n-space vertical size="large" style="justify-content: flex-start; padding: 5px;">
        <n-input v-model:value="debUrl" placeholder="输入URL" style="width: 100%" />
        <n-input v-model:value="id" placeholder="输入ID" style="width: 100%" />
        <n-button @click="submitTest" type="primary" ghost>提交测试</n-button>
        <div>
            <n-data-table v-if="showTable" :columns="columns" :data="tableData" />
        </div>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NButton, NSpace, useMessage, NDataTable } from 'naive-ui';

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
    setup(props) {
        const debUrl = ref('');
        const id = ref('');
        const showTable = ref(false);
        const message = useMessage();
        const columns = createColumns();
        const tableData = ref<TableDataItem[]>([])

        const submitTest = async () => {
            // 清空上一次的结果并显示加载状态
            showTable.value = false;
            // 创建FormData对象
            const formData = new FormData();
            formData.append('debUrl', debUrl.value);
            formData.append('id', id.value);
            // 发送multipart/form-data请求
            const res = await fetch('http://127.0.0.1:12345/appTestByUrl', {
                method: 'POST',
                body: formData // 不需要设置Content-Type，浏览器会自动处理
            });
            const result = await res.json();
            // 检测失败时显示错误提示
            if (result.status === "ERROR") {
                message.error('检测失败');
                return;
            }
            props.refreshTasks();
        };

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

        const getResult = async () => {
            const res = await fetch('http://127.0.0.1:12345/appTestResult?id=' + id.value);
            const csvText = await res.text();
            resultData.value = parseCSVToArray(csvText);
            resultData.value = resultData.value.slice(1, -1);
            tableData.value = resultJSON();  // 更新 ref 的值
            showTable.value = true;
        };

        return { debUrl, id, submitTest, getResult, resultData, showTable, columns, tableData };
    }
});
</script>
