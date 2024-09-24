<template>
    <n-space vertical size="large" style="justify-content: flex-start; padding: 5px; margin-top: 5px">
        <n-input v-model:value="debUrl" placeholder="输入URL" style="width: 100%" />
        <n-input v-model:value="id" placeholder="输入ID" style="width: 100%" />
        <n-button @click="submitTest" type="primary" ghost
            style="margin-bottom: 10px; margin-top: 10px;">提交测试</n-button>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NButton, NSpace, useMessage, NDataTable } from 'naive-ui';

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
    setup(props, { emit }) {
        const debUrl = ref('');
        const id = ref('');
        const message = useMessage();
        const submitTest = async () => {
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
                message.error('添加任务失败');
                return;
            } else {
                message.success('添加任务成功');
            }
            props.refreshTasks();
            emit('close'); // 发射关闭事件
        };

        return { debUrl, id, submitTest };
    }
});
</script>
