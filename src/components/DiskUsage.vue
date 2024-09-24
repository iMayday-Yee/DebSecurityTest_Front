<template>
    <n-space vertical size="large" style="padding: 5px;">
        <n-flex>
            当前缓存大小: {{ diskUsage }}
        </n-flex>
        <n-button @click="cleanCache" type="primary" ghost>清除缓存</n-button>
    </n-space>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { NButton, NSpace, NFlex, useMessage } from 'naive-ui';

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
    setup(props) {
        const diskUsage = ref('');
        const getDiskUsage = async () => {
            const res = await fetch('http://127.0.0.1:12345/diskUsage', {
                method: 'GET',
            });
            const result = await res.json();
            diskUsage.value = result.diskUsage;
        };
        const msg = ref('');
        const message = useMessage();
        const cleanCache = async () => {
            const res = await fetch('http://127.0.0.1:12345/cleanCache', {
                method: 'GET',
            });
            const result = await res.json();
            msg.value = result.status;
            if (msg.value === 'OK') {
                message.success('清除成功');
            } else {
                message.error('清除失败');
            }
            getDiskUsage();
            props.refreshTasks();
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
