<template>
    <div class="container">
        <el-row :gutter="20">
            <el-col :span="6">
                <div class="example">
                    <button v-on:click="shuffle" class="btn">Shuffle</button>
                    <transition-group name="flip-list">
                        <li class="tag-item" v-for="item in items" v-bind:key="item">
                            {{ item }}
                        </li>
                    </transition-group>
                </div>
            </el-col>
            <el-col :span="12">
                <div class="example">
                    <button @click="add" class="btn mr10">Add</button>
                    <button @click="remove" class="btn">Remove</button>
                    <transition-group name="list" tag="p">
                        <span v-for="item in tags" v-bind:key="item" class="list-item">
                          {{ item }}
                        </span>
                    </transition-group>
                </div>
            </el-col>
            <el-col :span="6">

            </el-col>
        </el-row>
    </div>
</template>

<script>
    import shuffle from 'lodash/shuffle';
    import floor from 'lodash/floor';
    import random from 'lodash/random';

    export default {
        data() {
            return {
                items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
                tags: [1, 2, 3, 4, 5, 6, 7, 8, 9],
                nextNum: 10,
            };
        },
        methods: {
            shuffle: function () {
                this.items = shuffle(this.items);
            },
            randomIndex: function () {
                return floor(random(0, this.tags.length));
            },
            add: function() {
                this.tags.splice(this.randomIndex(), 0, this.nextNum++);
            },
            remove: function() {
                this.tags.splice(this.randomIndex(), 1);
            },
        }
    };
</script>

<style scoped>
    .example {
        margin-bottom: 20px;
    }

    .btn {
        border: none;
        background-color: #409EFF;
        color: #fff;
        padding: 6px 12px;
        margin-bottom: 20px;
        border-radius: 4px;
        outline: none;
    }

    .mr10 {
        margin-right: 10px;
    }

    .flip-list-move {
        transition: transform 1s;
    }

    .tag-item {
       display: list-item;
        line-height: 30px;
    }

    .list-item {
        display: inline-block;
        margin-right: 10px;
    }

    .list-enter-active, .list-leave-active {
        transition: all 1s;
    }

    .list-enter, .list-leave-to {
        opacity: 0;
        transform: translateY(30px);
    }

</style>