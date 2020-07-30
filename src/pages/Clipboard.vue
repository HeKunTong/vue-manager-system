<template>
    <div class="container">
        <el-input v-model="inputData" placeholder="Please input" style="width:400px; max-width:100%;" />
        <el-button type="primary" class="ml10" icon="el-icon-document" @click="handleCopy(inputData,$event)">
            copy
        </el-button>
    </div>
</template>

<script>
    import clip from '@/utils/clipboard' // use clipboard directly

    export default {
        data() {
            return {
                inputData: 'http://www.baidu.com'
            };
        },
        methods: {
            handleCopy(text, event) {
                clip(text, event)
            }
        }
    }
</script>

<style scoped>
    .ml10 {
        margin-left: 10px;
    }
</style>

