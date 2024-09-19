<template>
    <n-space vertical size="large">
        <n-input v-model:value="debUrl" placeholder="输入URL" />
        <n-input v-model:value="id" placeholder="输入ID" />
        <n-button @click="submitTest">提交测试</n-button>
        <div v-if="loading">
            <n-spin>正在检测...</n-spin>
        </div>
        <div v-if="response">
            <n-tag :type="score >= 70 ? 'success' : 'error'">测试分数: {{ score }}</n-tag>
            <n-tag :type="score >= 70 ? 'success' : 'error'">检测结果: {{ info }}</n-tag>
            <n-tag>Id: {{ responseId }}</n-tag>
        </div>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NButton, NSpace, NTag, NSpin } from 'naive-ui';

export default defineComponent({
    components: {
        NInput,
        NButton,
        NSpace,
        NTag,
        NSpin
    },
    setup() {
        const debUrl = ref('');
        const id = ref('');
        const response = ref('');
        const score = ref(0);
        const info = ref('');
        const responseId = ref('');
        const loading = ref(false);

        const submitTest = async () => {
            // 清空上一次的结果并显示加载状态
            response.value = '';
            loading.value = true;

            // 创建FormData对象
            const formData = new FormData();
            formData.append('debUrl', debUrl.value);
            formData.append('id', id.value);

            // 发送multipart/form-data请求
            const res = await fetch('http://127.0.0.1:8080/appTestByUrl', {
                method: 'POST',
                body: formData // 不需要设置Content-Type，浏览器会自动处理
            });
            const result = await res.json();
            score.value = result.score;
            info.value = result.info;
            responseId.value = result.id;
            response.value = JSON.stringify(result);
            loading.value = false; // 请求完成后隐藏加载状态
        };
        return { debUrl, id, response, score, info, responseId, loading, submitTest };
    }
});
</script>
