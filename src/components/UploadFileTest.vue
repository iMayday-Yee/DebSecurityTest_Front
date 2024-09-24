<template>
    <n-space vertical size="large" style="justify-content: flex-start; padding: 5px; margin-top: 5px">
        <n-input v-model:value="id" placeholder="输入ID" style="width: 98%;" />
        <n-upload v-model:file-list="fileList" :action="uploadUrl" />
        <n-button @click="submitTest" type="primary" ghost
            style="margin-bottom: 10px; margin-top: 10px;">提交测试</n-button>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NUpload, NButton, NSpace } from 'naive-ui';

export default defineComponent({
    components: {
        NInput,
        NUpload,
        NButton,
        NSpace
    },
    setup() {
        const id = ref('');
        const fileList = ref([]);
        const response = ref('');
        const uploadUrl = 'your-upload-url'; // 替换为你的上传接口

        const submitTest = async () => {
            // 处理文件上传和ID提交逻辑
            // 假设后端接口为 /api/upload
            const formData = new FormData();
            formData.append('id', id.value);
            if (fileList.value.length > 0) {
                formData.append('file', fileList.value[0].file);
            }

            const res = await fetch('/api/upload', {
                method: 'POST',
                body: formData
            });
            response.value = await res.text();
        };

        return { id, fileList, response, submitTest };
    }
});
</script>