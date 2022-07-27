<template>
    <div>
        <!-- 顶部标识和按钮 -->
        <bk-form form-type="inline">
            <bk-form-item>
                <bk-tag theme="info" type="filled">课程详情页</bk-tag>
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
                <bk-table-column label="课程时间" prop="course_time"></bk-table-column>
                <bk-table-column label="上课地点" prop="course_place"></bk-table-column>
                <bk-table-column label="学生人数" prop="course_people"></bk-table-column>
                <bk-table-column label="操作" width="200">
                    <template slot-scope="props">
                        <bk-button class="mr10" theme="primary" text @click="jumpSearch(props.row)">查看详情</bk-button>
                    </template>
                </bk-table-column>
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
                show: true,
                echart: null,
                xData: ['第6节', '第5节', '第4节', '第3节', '第2节', '第1节'],
                yData: [52, 64, 76, 57, 90, 23, 99],
                myChartStyle: { float: 'left', width: '100%', height: '400px' },
                data: [
                    {
                        course_time: '第6节',
                        course_place: '3-101',
                        course_people: 300
                    },
                    {
                        course_time: '第5节',
                        course_place: '10-601',
                        course_people: 300
                    },
                    {
                        course_time: '第4节',
                        course_place: '3-101',
                        course_people: 300
                    },
                    {
                        course_time: '第3节',
                        course_place: '10-601',
                        course_people: 300
                    },
                    {
                        course_time: '第2节',
                        course_place: '3-101',
                        course_people: 300
                    },
                    {
                        course_time: '第1节',
                        course_place: '3-101',
                        course_people: 300
                    },
                    {
                        course_time: '',
                        course_place: ''
                    }
                ]
            }
        },
        // mounted () {
        //     // this.width = document.querySelector('.bk-tab-panel').offsetWidth
        //     // this.height = document.querySelector('.bk-tab-panel').offsetHeight
        //     this.width = this.$refs.bktab.style.width
        //     console.log('宽是' + this.width)
        //     console.log('高是' + this.height)
        // },
        methods: {
            // 跳转，返回上一个页面
            jumpBack () {
                this.$router.push({
                    name: 'sum_show'
                })
            },
            // 老师查询课程详情页面的跳转
            jumpSearch () {
                this.$router.push({
                    name: 'student_show'
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
/* #back{
    z-index: 999;
    position: relative;
  left: 30px;
  top: 2px;

} */
/* #right{
    display: flex;
  justify-content: flex-end;
} */
</style>
