<template>
    <n-space vertical size="large" style="padding: 5px;">
        <n-flex style="margin-bottom: 10px; margin-top: 5px;">
            当前缓存大小: {{ diskUsage }}
        </n-flex>
        <n-button @click="cleanCache" type="primary" ghost style="margin-bottom: 10px;">清除缓存</n-button>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { NButton, NSpace, NFlex, useMessage } from 'naive-ui';
const apiUrl = import.meta.env.VITE_BACKEND_URL;

export default defineComponent({
    props: {
        refreshTasks: {
            type: Function,
            required: true
        }
    },
    components: {
        NSpace,
        NButton,
        NFlex
    },
    setup(props, { emit }) {
        const diskUsage = ref('');
        const getDiskUsage = async () => {
            const res = await fetch(`${apiUrl}/diskUsage`, {
                method: 'GET',
            });
            const result = await res.json();
            diskUsage.value = result.diskUsage;
        };
        const msg = ref('');
        const message = useMessage();
        const cleanCache = async () => {
            const res = await fetch(`${apiUrl}/cleanCache`, {
                method: 'GET',
            });
            const result = await res.json();
            msg.value = result.status;
            if (msg.value === 'OK') {
                message.success('清除缓存成功');
            } else {
                message.error('清除缓存失败');
            }
            getDiskUsage();
            props.refreshTasks();
            emit('close'); // 发射关闭事件
        };

        // 在组件加载时调用 getDiskUsage 方法
        onMounted(() => {
            getDiskUsage();
        });

        return { diskUsage, getDiskUsage, cleanCache };
    }
});
</script>

<style>
/* 添加一些基本样式 */
h1 {
    font-size: 24px;
    margin-bottom: 20px;
}
</style>
