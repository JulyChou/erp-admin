<template>
    <div id="todo">
        <el-card>
            <div class="todo-card-header" slot="header">
                <i class="el-icon-arrow-down allCheckedIcon" :class="{'active': allChecked}" @click="allChecked = !allChecked"></i>
                <el-input class="titleInput" v-model="titileInput" placeholder="Todo List" size="small" :maxlength="20" clearable></el-input>
                <div class="newTag">
                    <el-input class="input-new-tag" v-if="inputVisible" v-model="inputValue" :maxlength="15" ref="saveTagInput" size="small" @keyup.enter.native="addItem" @blur="addItem">
                    </el-input>
                    <i v-else class="el-icon-plus button-new-tag" @click="showInput"></i>
                </div>
            </div>
            <div class="todo-card-body">
                <el-checkbox-group v-model="checked">
                    <transition-group name="el-zoom-in-top" class="list" tag="div">
                        <div class="list-item" v-for="(item,index) in list" :key="index">
                            <el-checkbox class="list-item-checkbox" :label="item" :title="item"></el-checkbox>
                            <i class="el-icon-close" @click.stop="deleteItem(index)"></i>
                        </div>
                    </transition-group>
                </el-checkbox-group>
                <p class="completed" v-if="!(list.length > 0)">没有要做的事情^_^</p>
            </div>
            <div class="todo-card-footer">
                <span :class="{'selected': allChecked}">统计结果</span>
                <span :class="{'selected': !allChecked}">{{list.length - checked.length}}项未完成</span>
            </div>
        </el-card>
    </div>
</template>
<script>
export default {
    name: 'todo',
    data() {
        return {
            list: ['1', '2', '3', '4', '5', '6', '7'],
            checked: ['4', '5', '6'],
            titileInput: '任务组',
            inputVisible: false,
            inputValue: ''
        }
    },
    computed: {
        allChecked: {
            get: function() {
                return this.checked.length === this.list.length
            },
            set: function(v) {
                if (v) {
                    this.checked = this.list.slice()
                } else {
                    this.checked = []
                }
            }
        }
    },
    watch: {
        list() {
            let Arr = []
            this.checked.forEach(check => {
                this.list.forEach(item => {
                   if (check === item) {
                        Arr.push(item)
                    }
                })
            })
            this.checked = Arr
        },
        checked(newVal, oldVal) {
            if (newVal.length - oldVal.length === 1) {
                let lastVal = newVal[newVal.length - 1]
                let isIncludes = this.list.includes(lastVal)
                if (isIncludes) {
                    this.$notify.success('您勾选了' + lastVal)
                }
            } else if (newVal.length - oldVal.length > 1) {
                let count = 0
                newVal.forEach(item => {
                    let isIncludes = this.list.includes(item)
                    if (isIncludes) {
                        this.$notify({
                            type: 'success',
                            message: '您勾选了' + item,
                            offset: 60 * count
                        })
                        count++
                    }
                })
            }
        }
    },
    methods: {
        deleteItem(index) {
            this.list.splice(index, 1)
        },
        addItem() {
            let inputValue = this.inputValue
            if (inputValue) {
                this.list.push(inputValue)
            }
            this.inputVisible = false
            this.inputValue = ''
        },
        showInput() {
            this.inputVisible = true
            this.$nextTick(() => {
                this.$refs.saveTagInput.$refs.input.focus()
            })
        }
    }
}
</script>
<style lang='less'>
#todo {
    color: #4d4d4d;
    font-weight: 300;
    .el-card {
        overflow: inherit;
        .el-card__header {
            padding: 15px 12px;
            box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
            .todo-card-header {
                height: 32px;
                display: flex;
                justify-content: space-between;
                align-items: center;
                .allCheckedIcon{
                    font-size: 20px;
                    color: #d9d9d9;
                    padding: 3px;
                    margin-right: 10px;
                    cursor: pointer;
                    &.active{
                        color: inherit;
                    }
                }
                .titleInput {
                    font-size: 16px;
                    color: inherit;
                    margin-right: 20px;
                    input {
                        border: none;
                        outline: none;
                        padding: 0;
                    }
                }
                .newTag {
                    .button-new-tag {
                        font-size: 20px;
                        color: #42b983;
                        cursor: pointer;
                    }
                }
            }
        }
        .el-card__body {
            padding: 0;
            .todo-card-body {
                position: relative;
                min-height: 225px;
                .completed{
                    position: absolute;
                    width: 100%;
                    top: 50%;
                    transform: translateY(-50%);
                    color: #999;
                    text-align: center;
                }
                .list {
                    .list-item {
                        position: relative;
                        height: 45px;
                        padding: 0 12px;
                        font-size: 24px;
                        border-bottom: 1px solid #ededed;
                        transition: all 0.1s ease-out;
                        display: flex;
                        align-items: center;
                        &:last-child{
                            border-bottom: none;
                        }
                        .list-item-checkbox {
                            width: 100%;
                            padding-right: 40px;
                            display: flex;
                            align-items: center;
                            &.is-checked {
                                .el-checkbox__label {
                                    color: #d9d9d9;
                                    text-decoration: line-through;
                                }
                            }
                            .el-checkbox__inner {
                                width: 26px;
                                height: 26px;
                                border-radius: 100%;
                                &::after {
                                    height: 12px;
                                    left: 11px;
                                    top: 5px;
                                }
                            }
                            .el-checkbox__label {
                                width: 200px;
                                overflow: hidden;
                                text-overflow: ellipsis;
                                white-space: nowrap;
                                line-height: inherit;
                                transition: color 0.4s;
                            }
                        }
                        .el-icon-close {
                            display: none;
                            position: absolute;
                            right: 20px;
                            top: 50%;
                            transform: translateY(-50%);
                            font-size: 20px;
                            color: #cc9a9a;
                            cursor: pointer;
                        }
                        &:hover {
                            .el-icon-close {
                                display: block;
                            }
                        }
                    }
                }
            }
            .todo-card-footer {
                position: relative;
                display: flex;
                justify-content: space-between;
                align-items: center;
                color: #777;
                padding: 10px 18px;
                font-size: 12px;
                border-top: 1px solid #ededed;
                user-select: none;
                &::before {
                    content: '';
                    position: absolute;
                    right: 0;
                    bottom: 0;
                    left: 0;
                    height: 100%;
                    overflow: hidden;
                    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6, 0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6, 0 17px 2px -6px rgba(0, 0, 0, 0.2);
                }
                span{
                    padding: 4px 7px;
                    text-decoration: none;
                    border: 1px solid transparent;
                    border-radius: 3px;
                    letter-spacing: 1px;
                    &.selected{
                        border-color: rgba(175, 47, 47, 0.2);
                    }
                }
            }
        }
    }
}
</style>
