<template>
    <div class="maps-container">
        <div class="main" ref="chartDom2"></div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as echarts from "echarts";
import mapList from "../config/mapList3.json";

const chartDom2 = ref("");
let myChart;

let option = {
    legend: {
        itemWidth: 25,
        itemHeight: 25,
        type: 'plain',
    },
    
    aria: {
        enabled: true,
    },
    tooltip: {
        formatter: function (info) {
            return [info.name].join("");
        },
    },
    series: [
        {
            type: "treemap",
            levels: [
                {
                    itemStyle: {
                        borderWidth: 3,
                        // borderColor: "#333",
                        gapWidth: 3,
                    },
                },
                {
                    color: ["#942e38", "#aaa", "#269f3c"],
                    colorMappingBy: "value",
                    itemStyle: {
                        gapWidth: 1,
                    },
                },
            ],
            label: {
                show: true,
                formatter: "{b}",
                ellipsis: true,
            },

            data: mapList,
        },
    ],
};

const loopArray = () => {
        let arr = [];
        for (let i = -36; i <= 36; i++) {
                for (let j = -36; j <= 36; j++) {
                        arr.push([i,j,56])
                }
        }
        return arr;
}

console.dir(loopArray());

onMounted(() => {
    myChart = echarts.init(chartDom2.value);
    //     myChart.showLoading();

    option && myChart.setOption(option);
});
</script>
<style>
.main{
    width: 800px;
    height: 800px;
    border: 1px solid #333;
    margin: 0 auto;
}
</style>