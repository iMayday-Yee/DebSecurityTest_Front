<template>
    <n-space vertical size="large" style="justify-content: flex-start; padding: 5px; margin-top: 5px">
        <n-upload ref="upload" multiple directory-dnd :max="1" :on-change="handleChange">
            <n-upload-dragger>
                <n-text style="font-size: 16px">
                    点击或者拖动文件到该区域来上传
                </n-text>
            </n-upload-dragger>
        </n-upload>
        <n-input v-model:value="id" placeholder="输入ID" style="width: 98%;" />
        <n-button @click="submitTest" type="primary" ghost
            style="margin-bottom: 10px; margin-top: 10px;">提交测试</n-button>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { NInput, NUpload, NButton, NSpace, useMessage, NText, NUploadDragger } from 'naive-ui';
import type { UploadFileInfo } from 'naive-ui';
const apiUrl = import.meta.env.VITE_BACKEND_URL;

export default defineComponent({
    props: {
        refreshTasks: {
            type: Function,
            required: true
        }
    },
    components: {
        NInput,
        NUpload,
        NButton,
        NSpace,
        NText,
        NUploadDragger
    },
    setup(props, { emit }) {
        const id = ref('');
        const fileList = ref<UploadFileInfo[]>([]);
        const message = useMessage();
        const upload = ref<typeof NUpload | null>(null);

        const handleChange = (data: { fileList: UploadFileInfo[] }) => {
            fileList.value = data.fileList;
        };

        const submitTest = async () => {
            if (fileList.value.length === 0) {
                message.error('请选择一个文件上传');
                return;
            }

            const formData = new FormData();
            formData.append('id', id.value);
            formData.append('debFile', fileList.value[0].file as File);

            try {
                const res = await fetch(`${apiUrl}/appTestByFile`, {
                    method: 'POST',
                    body: formData
                });
                const result = await res.json();

                if (result.status === "ERROR") {
                    message.error('添加任务失败');
                } else {
                    message.success('添加任务成功');
                    props.refreshTasks();
                    emit('close');
                    // 清空文件列表和ID输入
                    fileList.value = [];
                    id.value = '';
                    if (upload.value) {
                        upload.value.clear();
                    }
                }
            } catch (error) {
                console.error('Error:', error);
                message.error('提交失败，请稍后重试');
            }
        };

        return { id, fileList, submitTest, upload, handleChange };
    }
});
</script>