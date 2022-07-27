<template>
    <div class="wrap" ref="wrap">
        <div id="grid" :style="{ width: width, height: height }"></div>
    </div>
</template>

<script>
    import * as echarts from 'echarts'
    export default {
        props: {
            xData: {
                type: Array,
                data: []
            },
            yData: {
                type: Array,
                data: []
            }
        //     width: {
        //         type: String,
        //         default: '100%'
        //     },
        //     height: {
        //         type: String,
        //         default: '250px'
        //     }
        },
        data () {
            return {
                width: '650px',
                height: '250px',
                chart: null
                // xData: [],
                // yData: []
            }
        },
        created () {
            console.log('初始化')
        },
        mounted () {
            this.width = document.querySelector('.wrap').offsetWidth
            // this.width = document.querySelector('.wrap').offsetWidth
            this.initChart()
        },
        methods: {
            initChart () {
                this.chart = echarts.init(document.getElementById('grid'))
                this.SetOption()
            },
            SetOption () {
                console.log(this.chart)
                const option = {
                    grid: {
                        left: '6%',
                        right: '10%',
                        bottom: '10%',
                        width: '90%',
                        top: '10%'
                    },
                    xAxis: {
                        show: true,
                        type: 'category',
                        boundaruGap: false,
                        data: this.xData
                    },
                    yAxis: {
                        // show: true,
                        // type: 'value',
                        // data: [20, 40, 60, 80, 100],
                        axisLabel: { // y轴设置为%
                            show: true,
                            interval: 'auto',
                            formatter: '{value}%'
                        },
                        max: 100, // 最大值
                        min: 0 // 最小值
                    },
                    series: [{
                        data: this.yData, // y轴
                        type: 'bar'
                    }]
                }
                this.chart.setOption(option)
            }
        }
    }
</script>

<style>
.wrap {
    width: 100%;
    height: 250px;
    /* 250 */
    margin-left: 70px
}

</style>
