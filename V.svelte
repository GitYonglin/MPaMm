<script>
  import { onMount } from "svelte";
  const echarts = require("echarts");

  // option
  function bar(e) {
    let option = {
      backgroundColor: "#0f375f00",
      grid: {
        top: 10,
        bottom: 10,
        left: 0,
        right: 30
      },
      xAxis: {
        show: false,
        data: [1],
        axisLine: {
          lineStyle: {
            color: "#ccc"
          }
        }
      },
      yAxis: {
        splitLine: { show: false },
        position: "right",
        axisLine: {
          lineStyle: {
            color: "#ccc"
          }
        },
        axisLabel: {
          margin: -5
        }
      },
      series: [
        {
          name: "bar",
          type: "bar",
          barWidth: 10,
          itemStyle: {
            // borderRadius: 5,
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 0, color: "#14c8d4" },
              { offset: 1, color: "#43eec6" }
            ])
          },
          data: [100]
        },
        {
          name: "dotted",
          type: "pictorialBar",
          symbol: "rect",
          itemStyle: {
            color: "#0f375f"
          },
          symbolRepeat: true,
          symbolSize: [10, 5],
          // symbolMargin: 1,
          z: -10,
          data: [300]
        }
      ]
    };
    var myChart = echarts.init(e);
    myChart.setOption(option);
  }
  function gauge(e) {
    const gaugeData = [
      {
        value: 20,
        name: "Good",
        title: {
          offsetCenter: ["-40%", "80%"]
        },
        detail: {
          offsetCenter: [-75, "0%"]
        }
      }
    ];
    let option = {
      series: [
        {
          type: "gauge",
          center: [150, "50%"],
          radius: 150,
          startAngle: 270,
          endAngle: 90,
          anchor: {
            show: true,
            showAbove: true,
            size: 18,
            itemStyle: {
              color: "#FAC858"
            }
          },
          pointer: {
            icon:
              "path://M2.9,0.7L2.9,0.7c1.4,0,2.6,1.2,2.6,2.6v115c0,1.4-1.2,2.6-2.6,2.6l0,0c-1.4,0-2.6-1.2-2.6-2.6V3.3C0.3,1.9,1.4,0.7,2.9,0.7z",
            width: 8,
            length: "80%",
            offsetCenter: [0, "8%"]
          },
          progress: {
            show: true,
            overlap: true
            // roundCap: true
          },
          axisLine: {
            // roundCap: true
          },
          splitLine: {
            distance: 0
          },
          axisTick: {
            distance: 0
          },
          data: gaugeData,
          title: {
            show: false
          },
          detail: {
            width: 30,
            height: 30,
            fontSize: 14,
            color: "#fff",
            backgroundColor: "auto",
            // borderRadius: 3,
            formatter: v => {
              return v.toFixed(1);
            }
          }
        }
      ]
    };
    var myChart = echarts.init(e);
    myChart.setOption(option);
  }
</script>
<h1>左右竖向排列</h1>
<div style="display: flex;
    height: 300px;
    width: 210px;
    justify-content: space-between;
    ">
  <div id="gauge" style="height:320px; width: 160px;" use:gauge></div>
  <div id="bar" style="height:320px; width: 50px;" use:bar></div>
</div>