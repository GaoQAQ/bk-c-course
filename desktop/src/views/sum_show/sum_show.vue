<template>
    <bk-tab :active.sync="active" type="unborder-card">
        <!-- 总体完成情况 -->
        <bk-tab-panel v-bind="{ name: 'sum_finish', label: '总体完成情况' }" ref="bktab">
            <!-- 柱状图 -->
            <div>
                <tol :width="width" :height="height"
                    :x-data="xData"
                    :y-data="yData"></tol>
            </div>
            <!-- 课程表格 -->
            <!-- 表格 -->
            <template>
                <bk-table
                    :data="tableData"
                    :virtual-render="true"
                    height="200px">
                    <bk-table-column label="课程名" prop="course"></bk-table-column>
                    <bk-table-column label="总课时" prop="course_data"></bk-table-column>
                    <bk-table-column label="学生人数" prop="course_people"></bk-table-column>
                    <bk-table-column label="操作" width="150">
                        <template slot-scope="props">
                            <bk-button class="mr10" theme="primary" text @click="jumpSearch(props.row)">查看详情</bk-button>
                        </template>
                    </bk-table-column>
                </bk-table>
            </template>
        </bk-tab-panel>

        <!-- 学生完成情况 -->
        <!-- <bk-tab-panel v-bind="{ name: 'person_finish', label: '学生完成情况' }">
            搜索框"
            <bk-form form-type="inline">
                <bk-form-item>
                    <bk-input placeholder="输入学生姓名" v-model="formData.name" style="width:250px"></bk-input>
                </bk-form-item>
                <bk-form-item>
                    <bk-button ext-cls="mr5" @click="searchbtn" theme="primary" title="提交">搜索</bk-button>
                </bk-form-item>
                <br><br>
            </bk-form>
            表格
            <template>
                <bk-table
                    :data="data"
                    :size="size"
                    :pagination="pagination"
                    @page-change="handlePageChange">
                    <bk-table-column type="index" label="序列" width="60"></bk-table-column>
                    <bk-table-column label="课程" prop="course_stu"></bk-table-column>
                    <bk-table-column label="总课时" prop="course_time"></bk-table-column>
                    <bk-table-column label="人数" prop="course_sum"></bk-table-column>
                    <bk-table-column label="操作" width="150">
                        <template slot-scope="props">
                            <bk-button class="mr10" theme="primary" text @click="JumpStu(props.row)">查看详情</bk-button>
                        </template>
                    </bk-table-column>
                </bk-table>
            </template>
            <br>
        </bk-tab-panel> -->
    </bk-tab>
</template>
<script>
    import { bkTab, bkTabPanel, bkButton, bkTable, bkTableColumn } from 'bk-magic-vue'
    import tol from '../../components/echarts/tol.vue'
    export default {
        components: {
            bkTab,
            bkTabPanel,
            bkButton,
            bkTable,
            bkTableColumn,
            tol
            // bkProgress
        },
        data () {
            return {
                size: 'small',
                width: '',
                height: '',
                active: 'mission',
                searchValue: '', // 关键字搜索
                echart: null,
                xData: ['大数据', '线性代数', '算法分析', '数据结构', '离散数学', '大学物理'],
                yData: [75, 60, 85, 56, 87, 32],
                myChartStyle: { float: 'left', width: '100%', height: '400px' },
                bottomLoadingOptions: {
                    size: 'small',
                    isLoading: false
                },
                tableData: [
                    {
                        
                        course: '大数据',
                        course_data: 16,
                        course_people: 300,
                        course_stu: '算法分析',
                        course_time: 14,
                        course_sum: 60
                        // children: []
                    },
                    {
                        course: '线性代数',
                        course_data: 12,
                        course_people: 200,
                        course_stu: '数据结构',
                        course_time: 12,
                        course_sum: 60
                        // children: []
                    },
                    {
                        course: '算法分析',
                        course_data: 14,
                        course_people: 60,
                        course_stu: '大数据',
                        course_time: 16,
                        course_sum: 300
                        // children: []
                    },
                    {
                        course: '数据结构',
                        course_data: 12,
                        course_people: 60,
                        course_stu: '线性代数',
                        course_time: 12,
                        course_sum: 200
                        // children: []
                    },
                    {
                        course: '离散数学',
                        course_data: 12,
                        course_people: 60,
                        course_stu: '大学物理',
                        course_time: 12,
                        course_sum: 200
                        // children: []
                    },
                    {
                        course: '大学物理',
                        course_data: 12,
                        course_people: 60,
                        course_stu: '离散数学',
                        course_time: 12,
                        course_sum: 200
                        // children: []
                    },
                    {
                        course: '大学物理',
                        course_data: 12,
                        course_people: 60,
                        course_stu: '离散数学',
                        course_time: 12,
                        course_sum: 200
                        // children: []
                    }
                ],
                // 学生搜索搜索框数据
                formData: {
                    name: ''
                    // date: ''
                }
                // 分页
                // compactPaging: {
                //     current: 1,
                //     limit: 10,
                //     count: 300
                // }
            }
        },
        methods: {
            handlePageChange (page) {
                this.pagination.current = page
            },
            handlePageLimitChange () {
                console.log('handlePageLimitChange', arguments)
            },
            // 老师查询课程详情页面的跳转
            jumpSearch () {
                this.$router.push({
                    name: 'show_course'
                })
            },
            // 学生查询课程详情页面的跳转
            JumpStu () {
                this.$router.push({
                    name: 'show_course'
                })
            },
            handleScrollToBottom () {
                this.bottomLoadingOptions.isLoading = true
                setTimeout(() => {
                    this.tableData = this.tableData.concat(this.tableData)
                    this.bottomLoadingOptions.isLoading = false
                }, 2000)
            }
        }
    }
</script>

<style>
#linechart{
    width: 100%;
    height: 350px;
    /* 350px */
    background-color: rgba(1,1,1, .1);
    z-index: 0;
}
.canvas {
    width: 100%;
}
</style>
