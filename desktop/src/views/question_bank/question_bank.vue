<template>
    <div>
        <div>
            <!-- 搜索 -->
            <bk-form form-type="inline">
                <bk-form-item>
                    <bk-input placeholder="输入题目关键字" v-model="formData.name" style="width:250px"></bk-input>
                </bk-form-item>
                <bk-form-item>
                    <bk-button ext-cls="mr5" @click="searchbtn" theme="primary" title="提交">搜索</bk-button>
                </bk-form-item>
            </bk-form>

            <!-- 选项卡 -->
            <bk-tab :active.sync="active" type="unborder-card">

                <!-- 单选题 -->
                <bk-tab-panel v-bind="{ name: 'userlist', label: '单选题' }">
                    <!-- 单选下，题目展示表格 -->
                    <bk-table style="margin-top: 15px;"
                        :data="data"
                        :height="500"
                        :size="size"
                        :pagination="pagination"
                        @row-mouse-enter="handleRowMouseEnter"
                        @row-mouse-leave="handleRowMouseLeave"
                        @page-change="handlePageChange"
                        @page-limit-change="handlePageLimitChange">
                        <bk-table-column type="index" label="序列" width="60"></bk-table-column>
                        <bk-table-column label="题目" prop="title"></bk-table-column>
                        <bk-table-column label="选项A" prop="option_A"></bk-table-column>
                        <bk-table-column label="选项B" prop="option_B"></bk-table-column>
                        <bk-table-column label="选项c" prop="option_C"></bk-table-column>
                        <bk-table-column label="选项D" prop="option_D"></bk-table-column>
                        <bk-table-column label="答案" prop="answer"></bk-table-column>
                        <bk-table-column label="操作" width="80">
                            <template slot-scope="props">
                                <bk-button class="mr10" theme="primary" text @click="remove_choose(props.row)">导入</bk-button>
                            </template>
                        </bk-table-column>
                    </bk-table>
                </bk-tab-panel>

                <!-- 填空题 -->
                <bk-tab-panel v-bind="{ name: 'accounts', label: '填空题' }">
                    <!-- 填空下，题目展示表格 -->
                    <bk-table style="margin-top: 15px;"
                        :data="data"
                        :height="500"
                        :size="size"
                        :pagination="pagination"
                        @row-mouse-enter="handleRowMouseEnter"
                        @row-mouse-leave="handleRowMouseLeave"
                        @page-change="handlePageChange"
                        @page-limit-change="handlePageLimitChange">
                        <bk-table-column type="index" label="序列" width="80"></bk-table-column>
                        <bk-table-column label="题目" prop="title"></bk-table-column>
                        <bk-table-column label="答案" prop="answer"></bk-table-column>
                        <bk-table-column label="操作" width="80">
                            <template slot-scope="props">
                                <bk-button class="mr10" theme="primary" text @click="remove_fill(props.row)">导入</bk-button>
                            </template>
                        </bk-table-column>
                    </bk-table>
                </bk-tab-panel>

            </bk-tab>
        </div>
    </div>
</template>

<script>
    import { bkTable, bkTableColumn, bkButton, bkForm, bkFormItem, bkInput } from 'bk-magic-vue'

    export default {
        components: {
            bkTable,
            bkTableColumn,
            bkButton,
            bkForm,
            bkFormItem,
            bkInput
        },
        data () {
            return {
                // 表格尺寸
                size: 'medium',
                // 表格滑块
                active: 'mission',
                // 表格数据------？
                data: [
                    {
                        title: '',
                        option_A: '',
                        option_B: '',
                        option_C: '',
                        option_D: '',
                        answer: '',
                        children: []
                    }
                ],
                // 搜索框东西
                formData: {
                    name: ''
                    // date: ''
                },
                // 试写一下试写一下,填空题的存储数据
                question_fill: {
                    title_fill: 'n ',
                    answer: 'cevvsc'
                }
            }
        },
        created: function () {
            // `this` 指向 vm 实例----分页
            this.pagination.current = 1
            this.pagination.count = 0
            this.pagination.limit = 10
            this.searchbtn()
        },
        methods: {
            // 导入
            remove_choose (row) {
                console.log(row)
                this.$router.push({
                    name: 'set_question',
                    query: { param: row, type: 'MULTIPLE' } })
            },
            searchbtn () {
                // const { current, limit } = this.pagination
                const { name } = this.formData
                this.$http.get(`/api/question_search?title=${name}`).then((res) => {
                    console.log(res.data)
                    this.data = res.data.results
                    this.pagination.count = res.data.count
                }, (err) => {
                    console.log(err)
                })
            },
            handlePageLimitChange () {
                console.log('handlePageLimitChange', arguments)
            },
            handlePageChange (page) {
                this.pagination.current = page
                this.searchbtn()
            }
            // 填空题赋值函数，试写一下试写一下。接口还没给，虚拟的数据哈哈哈。接口应该暂时还写不了，突然发现没有接口不好动工
            // get_question_fill (fill) {
            //     this.title = fill.title_JK
            //     this.answer = fill.answer_JK
            // }
        }
        
    }
</script>

<style>
    .dot-menu {
        display: inline-block;
        vertical-align: middle;
    }

    .tippy-tooltip.dot-menu-theme {
        padding: 0;
    }
    .dot-menu-trigger {
        display: block;
        width: 30px;
        height: 30px;
        line-height: 30px;
        border-radius: 50%;
        text-align: center;
        font-size: 0;
        cursor: pointer;
    }
    .dot-menu-trigger:hover {
        color: #3A84FF;
        background-color: #DCDEE5;
    }
    .dot-menu-trigger:before {
        content: "";
        display: inline-block;
        width: 3px;
        height: 3px;
        border-radius: 50%;
        background-color: currentColor;
        box-shadow: 0 -4px 0 currentColor, 0 4px 0 currentColor;
    }
    .dot-menu-list {
        margin: 0;
        padding: 5px 0;
        min-width: 50px;
        list-style: none;
    }
    .dot-menu-list .dot-menu-item {
        padding: 0 10px;
        font-size: 12px;
        line-height: 26px;
        cursor: pointer;
        /* &:hover {
            background-color: #eaf3ff;
            color: #3a84ff;
        } */
    }
        .input-demo {
        width: 500px;
    }
</style>
