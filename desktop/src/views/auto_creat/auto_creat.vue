<template>
    <bk-tab :active.sync="active" type="unborder-card">
        <!-- 文本生成 -->
        <bk-tab-panel v-bind="{ name: 'userlist', label: '文本生成' }">
            <!-- 文本序列输入 -->
            <bk-form form-type="inline">
                <bk-form-item>
                    <bk-input placeholder="输入关键字，如：二叉树" v-model="formData.name" style="width:250px"></bk-input>
                </bk-form-item>
                <bk-form-item>
                    <bk-button ext-cls="mr5" @click="searchbtn" theme="primary" title="提交">生成</bk-button>
                </bk-form-item>
            </bk-form>
            <!-- 题目显示表格 -->
            <bk-table style="margin-top: 15px;"
                :data="data"
                :size="size">
                <bk-table-column type="index" label="序列" width="60"></bk-table-column>
                <bk-table-column label="问题" prop="question"></bk-table-column>
                <bk-table-column label="答案" prop="answer"></bk-table-column>
                <bk-table-column label="操作" width="150">
                    <template slot-scope="props">
                        <bk-button class="mr10" theme="primary" text @click="exampleSetting5.primary.visible = true">修改问题</bk-button>
                        <!-- 弹窗 -->
                        <bk-dialog v-model="exampleSetting5.primary.visible"
                            theme="primary"
                            :mask-close="false"
                            title="修改">
                            请选择要修改的部分
                            <div>
                                <bk-button @click="exampleSetting6.custom.visible = true" class="mr10">
                                    修改题目
                                </bk-button>
                                <bk-button @click="exampleSetting6_answer.custom.visible = true" class="mr10">
                                    修改答案
                                </bk-button>
                            </div>
                            
                            <!-- 下面少了一句:header-position="exampleSetting6.custom.headerPosition"，也许是整体的弹窗样式设计过的原因 -->
                            <bk-dialog v-model="exampleSetting6.custom.visible"
                                :header-position="exampleSetting6.custom.headerPosition"
                                :ok-text="exampleSetting6.custom.okText"
                                :cancel-text="exampleSetting6.custom.cancelText"
                                title="描述">
                                修改题目的弹窗
                            </bk-dialog>
                            <bk-dialog v-model="exampleSetting6.custom.visible"
                                :header-position="exampleSetting6.custom.headerPosition"
                                :ok-text="exampleSetting6.custom.okText"
                                :cancel-text="exampleSetting6.custom.cancelText"
                                title="描述">
                                修改答案的弹窗
                            </bk-dialog>
                        </bk-dialog>
                        <bk-button class="mr10" theme="primary" text @click="save(props.row)">存入</bk-button>
                    </template>
                </bk-table-column>
            </bk-table>
        </bk-tab-panel>
        
        <!-- 图像生成 -->
        <bk-tab-panel v-bind="{ name: 'accounts', label: '图像生成' }">
            <!-- 图像序列输入 -->
            <bk-form form-type="inline">
                <bk-form-item>
                    <bk-input placeholder="输入举例(英文逗号间隔)：a,8,b,2" v-model="formData.name" style="width:250px"></bk-input>
                </bk-form-item>
                <bk-form-item>
                    <bk-button ext-cls="mr5" @click="searchBC" theme="primary" title="提交">生成并保存</bk-button>
                </bk-form-item>
            </bk-form>
            <br>
            <!-- 显示图片 v-if="image != null"-->
            <br>
            <bk-zoom-image v-if="image != null" :src="image" class="zoom-image"></bk-zoom-image>
            <!-- <img src="a.png" class="" width="100%"> -->
            <!-- <br>
            <bk-button ext-cls="mr5" @click="searchbtn" theme="primary" title="提交">保存题目</bk-button> -->
        </bk-tab-panel>
    </bk-tab>
</template>
<script>
    import { bkTab, bkTabPanel, bkInput, bkTable, bkZoomImage, bkTableColumn, bkButton, bkDialog } from 'bk-magic-vue'

    export default {
        components: {
            bkTab,
            bkTabPanel,
            bkInput,
            bkZoomImage,
            bkTable,
            bkTableColumn,
            bkButton,
            bkDialog
            // bkSideslider
        },
        data () {
            return {
                size: 'middle',
                active: 'mission',
                image: null,
                // value: '此处显示生成后的题目，并提供修改',
                // question: '可在此处修改',
                // 搜索框东西
                formData: {
                    name: ''
                    // date: ''
                },
                // 分页
                // pagination: {
                //     current: 1,
                //     count: 500,
                //     limit: 20
                // },
                data: [
                    {
                        question: '我是一个问题',
                        answer: '那我就是一个答案',
                        children: []
                    }
                ],
                // 弹窗
                exampleSetting5: {
                    primary: {
                        visible: false,
                        headerPosition: 'left'
                    }
                },
                exampleSetting6: {
                    custom: {
                        visible: false,
                        headerPosition: 'left',
                        okText: '修改',
                        cancelText: '取消',
                        sliderVisible: false
                    }
                },
                exampleSetting_answer: {
                    custom: {
                        visible: false,
                        headerPosition: 'left',
                        okText: '修改',
                        cancelText: '取消',
                        sliderVisible: false
                    }
                }
            }
        },
        methods: {
            handlePageLimitChange () {
                console.log('handlePageLimitChange', arguments)
            },
            handlePageChange (page) {
                this.pagination.current = page
            },
            searchBC () {
                // const { current, limit } = this.pagination
                const { name } = this.formData
                this.$http.get(`/api/huffmantrees_show?title=${name}`).then((res) => {
                    console.log(res.data)
                    this.image = res.data.results.img
                    // this.pagination.count = res.data.count
                }, (err) => {
                    console.log(err)
                })
            },
            searchbtn () {
                const { name } = this.formData
                this.$http.post(`/api/question_search`, { title: name }).then((res) => {
                    console.log(res.data)
                    this.data = res.data.results
                    this.pagination.count = res.data.count
                }, (err) => {
                    console.log(err)
                })
            }

        }
    }
</script>

<style lang="postcss">
    .input-demo {
        width: 500px;
    }

        .zoom-image {
        width: 100%;
        height: 300px;
    }
     .dot-menu {
        display: inline-block;
        vertical-align: middle;
    }

    .tippy-tooltip.dot-menu-theme {
        padding: 0;
    }

    /* .dot-menu-trigger:hover {
        color: #3A84FF;
        background-color: #DCDEE5;
    } */
    .dot-menu-trigger:before {
        content: "";
        display: inline-block;
        width: 3px;
        height: 3px;
        border-radius: 50%;
        background-color: currentColor;
        box-shadow: 0 -4px 0 currentColor, 0 4px 0 currentColor;
    }
</style>
