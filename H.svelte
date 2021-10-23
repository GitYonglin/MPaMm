<script>
  import { onMount } from "svelte";
  import DragArc from "drag-arc";
  const echarts = require("echarts");
  let size = 300;
  $: radius = (size - 20) / 2;
  let mpaValue = 100;
  let mpaChart;
  let mpaOption;
  let mmValue = 100;
  let mmChart;
  let mmOption;

  // option
  function bar(e) {
    mmOption = {
      backgroundColor: "#0f375f00",
      grid: {
        top: 0,
        bottom: 5,
        left: 10,
        right: 10
      },
      yAxis: {
        show: false,
        data: [1]
      },
      xAxis: {
        // inverse: true, // 坐标方向
        splitLine: { show: false },
        position: "right",
        axisLine: {
          lineStyle: {
            color: "#000"
          }
        },
        axisLabel: {
          margin: -9
        }
      },
      series: [
        {
          type: "bar",
          barWidth: 20,
          itemStyle: {
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 0, color: "#14c8d4" },
              { offset: 1, color: "#43eec6" }
            ])
          },
          data: [mmValue]
        },
        {
          type: "pictorialBar",
          symbol: "rect",
          itemStyle: {
            color: "#0f375f"
          },
          symbolRepeat: true,
          symbolSize: [10, 20],
          // symbolMargin: 1,
          z: -10,
          data: [300]
        }
      ]
    };
    mmChart = echarts.init(e, null, { renderer: "svg" });
    mmChart.setOption(mmOption);
  }
  function gauge(e) {
    const gaugeData = [
      {
        value: 22,
        title: {
          offsetCenter: ["-40%", "80%"]
        },
        detail: {
          offsetCenter: [0, "-50%"]
        }
      }
    ];
    mpaOption = {
      series: [
        {
          type: "gauge",
          center: ["50%", radius],
          radius: radius,
          startAngle: 180,
          endAngle: 0,
          anchor: {
            show: true,
            showAbove: true,
            size: 18,
            itemStyle: {
              color: "#FAC858"
            }
          },
          pointer: {
            width: 4,
            length: "100%"
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
            fontSize: 24,
            color: "#f00",
            formatter: v => {
              return v.toFixed(1);
            }
          }
        }
      ]
    };
    mpaChart = echarts.init(e, null, { renderer: "svg" });
    mpaChart.setOption(mpaOption);
  }
  function mmChange() {
    mmOption.series[0].data = [mmValue];
    mmChart.setOption(mmOption);
  }
  $: {
    if (mpaChart && mpaOption.series[0].data[0]) {
      const x = Math.atan2(50 - 0, 50 - mpaValue);
      const r = (360 * x) / Math.PI - 90;
      const v = r / 1.8;
      console.log(x, "<=>", r, "v=", v);

      console.log(mpaValue);
      mpaOption.series[0].data[0].value = v;
      mpaChart.setOption(mpaOption);
    }
  }
  function dragArc(e) {
    new DragArc({
      el: e,
      value: 10,
      startDeg: 1,
      endDeg: 2,
      counterclockwise: false,
      innerLineWidth: 0,
      outLineWidth: 0,
      change: v => {
        console.log(v);
      }
    });
  }
</script>
<h1>上线横向排列</h1>
<div style="display: flex;
flex-direction: column;
    width: {size}px;
    justify-content: space-between;
    ">
  <div id="gauge" style="position: relative;width: 100%; height: {radius+10}px;" use:gauge>
   <input style="
      position: absolute;
    width: 280px;
    height: 100%;
    top: 8px;
    left: 8px;
    z-index: 100;
    outline: none;
    appearance: none;
    background: #f000;
  " id="slider"  bind:value="{mpaValue}" type="range" min="0" max="100" step="1"  /> 
  </div>
  <div id="bar" style="position: relative;width:100%; height: 45px;" use:bar>
  <input style="
      position: absolute;
    width: 280px;
    height: 20px;
    top: 8px;
    left: 8px;
    z-index: 100;
    outline: none;
    appearance: none;
    background: #f000;
  " id="slider" on:change={mmChange} bind:value="{mmValue}" type="range" min="0" max="300" step="1"  /> 
  </div>
  
</div>

<style>
  input[type="range"]::-webkit-slider-thumb {
    appearance: none; /*//这三个是去掉滑块原有的默认样式，划重点！！*/
    box-shadow: 0 0 2px; /*设置滑块的阴影*/
    width: 5px;
    height: 25px;
    background-image: linear-gradient(to top, #14c8d4 0%, #43eec6 100%);
  }
</style>