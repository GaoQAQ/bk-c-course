<template>
    <div>
        <!-- 顶部标识和按钮 -->
        <bk-form form-type="inline">
            <bk-form-item>
                <bk-tag theme="info" type="filled">每节课详情页</bk-tag>
            </bk-form-item>
            <bk-form-item>
                <span></span><span></span>
                <bk-button id="right" :text="true" :theme="'default'" :title="'基础按钮'" @click="jumpBack">返回</bk-button>
            </bk-form-item>
            <br><br>
        </bk-form>
        <!-- 柱状图 -->
        <div>
            <tol :width="width" :height="height" :x-data="xData" :y-data="yData"></tol>
        </div>
        <!-- 课程表格 -->
        <template>
            <bk-table
                :data="data"
                :virtual-render="true"
                height="200px">
                <bk-table-column prop="test_index" label="题目序列" width="200px"></bk-table-column>
                <bk-table-column label="题目难易" prop="course_difficulty"></bk-table-column>
                <bk-table-column label="题目类型" prop="course_types"></bk-table-column>
                <bk-table-column label="正确率" prop="course_correct"></bk-table-column>
                <bk-table-column label="完成度" prop="course_finish"></bk-table-column>
            </bk-table>
        </template>
    </div>
</template>

<script>
    import { bkTable, bkTableColumn, bkButton, bkTag } from 'bk-magic-vue'
    import tol from '../../components/echarts/tol.vue'
    // import echarts from 'echarts'

    export default {
        components: {
            bkTable,
            bkTableColumn,
            bkButton,
            bkTag,
            tol
        },
        data () {
            return {
                size: 'small',
                width: '',
                height: '',
                echart: null,
                xData: ['第1题', '第2题', '第3题', '第4题', '第5题', '第6题'],
                yData: [98, 95, 77, 90, 47, 28],
                myChartStyle: { float: 'left', width: '100%', height: '400px' },
                data: [
                    {
                        test_index: '第1题',
                        course_difficulty: '易',
                        course_types: '单选',
                        course_correct: '90%',
                        course_finish: '98%'
                    },
                    {
                        test_index: '第2题',
                        course_difficulty: '难',
                        course_types: '单选',
                        course_correct: '70%',
                        course_finish: '90%'
                    },
                    {
                        test_index: '第3题',
                        course_difficulty: '易',
                        course_types: '填空',
                        course_correct: '87%',
                        course_finish: '98%'
                    },
                    {
                        test_index: '第4题',
                        course_difficulty: '难',
                        course_types: '多选',
                        course_correct: '70%',
                        course_finish: '99%'
                    },
                    {
                        test_index: '第5题',
                        course_difficulty: '易',
                        course_types: '简答',
                        course_correct: '81%',
                        course_finish: '95%'
                    },
                    {
                        test_index: '第6题',
                        course_difficulty: '难',
                        course_types: '简答',
                        course_correct: '73%',
                        course_finish: '93%'
                    },
                    {
                        test_index: '',
                        course_difficulty: '',
                        course_types: '',
                        course_correct: '',
                        course_finish: ''
                    }
                ]
            }
        },
        methods: {
            // 跳转，返回上一个页面
            jumpBack () {
                this.$router.push({
                    name: 'show_course'
                })
            },
            change (checked, tag) {
                this.$router.push({
                    name: 'student_show'
                })
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
