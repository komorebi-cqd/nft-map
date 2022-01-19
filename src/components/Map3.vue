<template>
    <div class="maps-container">
        <div class="main" ref="chartDom"></div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as echarts from "echarts";
import map3 from "../config/map3.json";
// import e from 'echarts/extension/bmap/bmap';
// require('echarts/extension/bmap/bmap'); 
// import {svg1} from "../config";


const chartDom = ref("");
let myChart;
var option;

var COLORS = ['#070093', '#1c3fbf', '#1482e5', '#70b4eb', '#b4e0f3', '#ffffff'];
var lngExtent = [39.5, 40.6];
var latExtent = [115.9, 116.8];
var cellCount = [50, 50];
var cellSizeCoord = [
  (lngExtent[1] - lngExtent[0]) / cellCount[0],
  (latExtent[1] - latExtent[0]) / cellCount[1]
];
var gapSize = 0;
// prettier-ignore
function renderItem(params, api) {
  console.log(params,'params', api,'api');
  var context = params.context;
  var lngIndex = api.value(0);
  var latIndex = api.value(1);
  var coordLeftTop = [
    +(latExtent[0] + lngIndex * cellSizeCoord[0]).toFixed(6),
    +(lngExtent[0] + latIndex * cellSizeCoord[1]).toFixed(6)
  ];
  var pointLeftTop = getCoord(params, api, lngIndex, latIndex);
  var pointRightBottom = getCoord(params, api, lngIndex + 1, latIndex + 1);
  return {
    type: 'rect',
    shape: {
      x: pointLeftTop[0],
      y: pointLeftTop[1],
      width: pointRightBottom[0] - pointLeftTop[0],
      height: pointRightBottom[1] - pointLeftTop[1]
    },
    style: api.style({
      stroke: 'rgba(0,0,0,0.1)'
    }),
    styleEmphasis: api.styleEmphasis()
  };
}
function getCoord(params, api, lngIndex, latIndex) {
  var coords = params.context.coords || (params.context.coords = []);
  var key = lngIndex + '-' + latIndex;
  // bmap returns point in integer, which makes cell width unstable.
  // So we have to use right bottom point.
  return (
    coords[key] ||
    (coords[key] = api.coord([
      +(latExtent[0] + lngIndex * cellSizeCoord[0]).toFixed(6),
      +(lngExtent[0] + latIndex * cellSizeCoord[1]).toFixed(6)
    ]))
  );
}
option = {
  tooltip: {},
  visualMap: {
    type: 'piecewise',
    inverse: true,
    top: 10,
    left: 10,
    pieces: [
      {
        value: 0,
        color: COLORS[0]
      },
      {
        value: 1,
        color: COLORS[1]
      },
      {
        value: 2,
        color: COLORS[2]
      },
      {
        value: 3,
        color: COLORS[3]
      },
      {
        value: 4,
        color: COLORS[4]
      },
      {
        value: 5,
        color: COLORS[5]
      }
    ],
    borderColor: '#ccc',
    borderWidth: 2,
    backgroundColor: '#eee',
    dimension: 2,
    inRange: {
      color: COLORS,
      opacity: 0.7
    }
  },
  series: [
    {
      type: 'custom',
      coordinateSystem: 'bmap',
      renderItem: renderItem,
      animation: false,
      emphasis: {
        itemStyle: {
          color: 'yellow'
        }
      },
      encode: {
        tooltip: 2
      },
      data: map3
    }
  ],
  bmap: {
    center: [116.46, 39.92],
    zoom: 11.8,
    roam: true,
    mapStyle: {
      styleJson: [
        {
          featureType: 'water',
          elementType: 'all',
          stylers: {
            color: '#d1d1d1'
          }
        },
        {
          featureType: 'land',
          elementType: 'all',
          stylers: {
            color: '#f3f3f3'
          }
        },
        {
          featureType: 'railway',
          elementType: 'all',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'highway',
          elementType: 'all',
          stylers: {
            color: '#999999'
          }
        },
        {
          featureType: 'highway',
          elementType: 'labels',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'arterial',
          elementType: 'geometry',
          stylers: {
            color: '#fefefe'
          }
        },
        {
          featureType: 'arterial',
          elementType: 'geometry.fill',
          stylers: {
            color: '#fefefe'
          }
        },
        {
          featureType: 'poi',
          elementType: 'all',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'green',
          elementType: 'all',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'subway',
          elementType: 'all',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'manmade',
          elementType: 'all',
          stylers: {
            color: '#d1d1d1'
          }
        },
        {
          featureType: 'local',
          elementType: 'all',
          stylers: {
            color: '#d1d1d1'
          }
        },
        {
          featureType: 'arterial',
          elementType: 'labels',
          stylers: {
            visibility: 'off'
          }
        },
        {
          featureType: 'boundary',
          elementType: 'all',
          stylers: {
            color: '#fefefe'
          }
        },
        {
          featureType: 'building',
          elementType: 'all',
          stylers: {
            color: '#d1d1d1'
          }
        },
        {
          featureType: 'label',
          elementType: 'labels.text.fill',
          stylers: {
            color: 'rgba(0,0,0,0)'
          }
        }
      ]
    }
  }
};



onMounted(() => {
    myChart = echarts.init(chartDom.value);
    //     myChart.showLoading();

    option && myChart.setOption(option);
});
</script>
<style>
.main {
    width: 1400px;
    height: 1400px;
}
</style>