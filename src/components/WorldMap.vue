<template>
    <div class="world-map-container">
        <div class="main" ref="world"></div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as echarts from "echarts";
import worldMapData from "../config/worldMapData.json";
import worldMapSVG from "../config/worldMapSVG";
let myChart;
let option;

var lngExtent = [2077, 4264];
var latExtent = [2020, 4207];
var cellCount = [39, -39];
var cellSizeCoord = [
    (lngExtent[1] - lngExtent[0]) / cellCount[0],
    (latExtent[1] - latExtent[0]) / cellCount[1],
];
var gapSize = 0;
// prettier-ignore
var COLORS = ['#b4e0f3', '#1c3fbf', '#1482e5', '#70b4eb', '#b4e0f3', '#ffffff'];
function renderItem(params, api) {
    var context = params.context;
    var lngIndex = api.value(0);
    var latIndex = api.value(1);
    var coordLeftTop = [
        +(latExtent[0] + lngIndex * cellSizeCoord[0]).toFixed(6),
        +(lngExtent[0] + latIndex * cellSizeCoord[1]).toFixed(6),
    ];
    var pointLeftTop = getCoord(params, api, lngIndex, latIndex);
    var pointRightBottom = getCoord(params, api, lngIndex + 1, latIndex + 1);
    return {
        type: "rect",
        shape: {
            x: pointLeftTop[0],
            y: pointLeftTop[1],
            width: pointRightBottom[0] - pointLeftTop[0],
            height: pointRightBottom[1] - pointLeftTop[1],
        },
        style: api.style({
            stroke: "rgba(0,0,0,.1)",
        }),
        styleEmphasis: api.styleEmphasis(),
    };
}
function getCoord(params, api, lngIndex, latIndex) {
    var coords = params.context.coords || (params.context.coords = []);
    var key = lngIndex + "-" + latIndex;
    return (
        coords[key] ||
        (coords[key] = api.coord([
            +(latExtent[0] + lngIndex * cellSizeCoord[0]).toFixed(6),
            +(lngExtent[0] + latIndex * cellSizeCoord[1]).toFixed(6),
        ]))
    );
}

let world = ref("");

function s(svg) {
    echarts.registerMap("sicily", { svg: svg });
    option = {
        visualMap: {
            type: "piecewise",
            inverse: true,
            top: 10,
            left: 10,
            show: false,
            pieces: [
                {
                    value: 56,
                    color: COLORS[0],
                    // colorAlpha: 0.1,
                },
                {
                    value: 1,
                    color: COLORS[1],
                },
                {
                    value: 2,
                    color: COLORS[2],
                },
                {
                    value: 3,
                    color: COLORS[3],
                },
                {
                    value: 4,
                    color: COLORS[4],
                },
                {
                    value: 5,
                    color: COLORS[5],
                },
            ],
            borderColor: "#ccc",
            borderWidth: 1,
            backgroundColor: "#f40",
            dimension: 2,
            inRange: {
                color: COLORS,
                opacity: 0.01,
            },
            calculable: true,
        },
        title: {
            text: "世界地图",
            left: "center",
            // textStyle: {
            //     color: "red",
            // },
        },
        tooltip: {
            trigger: "item",
            formatter: function (params) {
                return `${params.value[0]},${params.value[1]}`;
            },
            enterable: true,
        },
        geo: [
            {
                map: "sicily",
                roam: true,
                layoutCenter: ["50%", "50%"],
                layoutSize: "80%",
                selectedMode: "single",
                scaleLimit: {
                    max: 3,
                    min: 0.5,
                },
                tooltip: {
                    trigger: "item",
                    enterable: true,
                    formatter: function (params) {
                        return `${params.value[0]},${params.value[1]}`;
                    },
                },
                emphasis: {
                    label: {
                        color: "blue",
                        fontWeight: "bold",
                        fontSize: 30,
                    },
                },
                select: {
                    itemStyle: {
                        color: "red",
                    },
                    label: {
                        show: false,
                    },
                },
            },
        ],
        series: [
            {
                type: "custom",
                coordinateSystem: "geo",
                renderItem: renderItem,
                animation: false,
                emphasis: {
                    //选中颜色
                    itemStyle: {
                        color: "yellow",
                        opacity: 1,
                    },
                },
                encode: {
                    tooltip: 2,
                },
                data: worldMapData,
                labelLayout: {
                    rotate: 90,
                },
                // itemStyle: {
                //     borderWidth: 5,
                //     borderColor: "#000",
                // },
            },
        ],
    };
    option && myChart.setOption(option);
}

onMounted(() => {
    myChart = echarts.init(world.value);
    myChart.on("click", { seriesIndex: 0 }, function (event) {
        console.log(event);
    });
    s(worldMapSVG);
});
</script>
<style>
.main {
    width: 1200px;
    height: 800px;
    border: 1px solid #333;
    margin: 0 auto;
}
</style>