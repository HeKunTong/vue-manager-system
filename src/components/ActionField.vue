<template>
    <div>
        <el-button
            v-for="(button, index) in buttons"
            :key="index"
            type="text"
            :icon="button.icon"
            :class="button.type"
            @click="onButton(button)"
        >
            {{ button.title }}
        </el-button>
    </div>
</template>

<script>
    export default {
        name: 'action-field',
        props: {
            name: String,
            index: [Number, String],
            row: Object,
            buttons: Array,
            func: Function,
        },
        methods: {
            onButton: function(button) {
                if (button.onAction) {
                    button.onAction(this.row, this.index);
                }
            }
        }
    };
</script>

<style scoped>
    .danger {
        color: #ff0000;
    }
</style>