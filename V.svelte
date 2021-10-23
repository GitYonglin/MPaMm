<script>
  import { onMount } from "svelte";
  import DragArc from "drag-arc";
  const echarts = require("echarts");
  let size = 360;
  $: radius = (size - 20) / 2;
  let mpaValue = 100;
  let mpaChart;
  let mpaOption;
  var endAngle = 90;
  var startAngle = 270;
  var maxValue = 60;
  let mmValue = 100;
  let mmChart;
  let mmOption;

  // option
  function bar(e) {
    mmOption = {
      backgroundColor: "#0f375f00",
      grid: {
        top: 10,
        bottom: 10,
        left: 0,
        right: 20
      },
      xAxis: {
        show: false,
        data: [1]
      },
      yAxis: {
        splitLine: { show: false },
        position: "right",
        axisLine: {
          lineStyle: {
            color: "#000"
          }
        },
        axisLabel: {
          margin: -1
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
          data: [100]
        },
        {
          type: "pictorialBar",
          symbol: "rect",
          itemStyle: {
            color: "#0f375f"
          },
          symbolRepeat: true,
          symbolSize: [19, 10],
          // symbolMargin: 1,
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
          offsetCenter: ["-50%", 0]
        }
      }
    ];
    mpaOption = {
      series: [
        {
          animation: false, // 关掉动画, 否则拖动会有延迟
          type: "gauge",
          center: [radius, "50%"],
          radius: radius,
          startAngle,
          endAngle,
          max: maxValue,
          pointer: {
            width: 5,
            length: 20,
            offsetCenter: [0, -(radius - 20)],
            icon:
              "path://M2.9,0.7L2.9,0.7c1.4,0,2.6,1.2,2.6,2.6v115c0,1.4-1.2,2.6-2.6,2.6l0,0c-1.4,0-2.6-1.2-2.6-2.6V3.3C0.3,1.9,1.4,0.7,2.9,0.7z"
          },
          anchor: { show: false },
          progress: { show: false },
          axisLine: { show: false },
          splitLine: { show: false },
          axisTick: { show: false },
          title: { show: false },
          detail: { show: false },
          axisLabel: { show: false },
          z: 100,
          data: [{ value: 30 }]
        },
        {
          type: "gauge",
          center: [radius, "50%"],
          radius: radius,
          startAngle,
          endAngle,
          max: maxValue,
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

    /** 角度范围 */
    const dataRatio = Math.abs(endAngle - startAngle);
    var { width, height } = getComputedStyle(e);
    const x = parseInt(width.slice(0, -2), 0) - 10;
    const y = parseInt(height.slice(0, -2), 0) / 2;
    function changeValue(event) {
      const x2 = event.offsetX;
      const y2 = event.offsetY;
      console.log(`x=${x},y=${y}`);
      // 当前点击位置的角度.
      let currentAngle = (Math.atan2(y - y2, x - x2) * 180) / Math.PI;
      console.log(`x2=${x2},y2=${y2}`, currentAngle);
      currentAngle += endAngle;
      // 边界处理
      if (currentAngle < 0) {
        currentAngle = 0;
      } else if (currentAngle > dataRatio) {
        currentAngle = Math.abs(endAngle - startAngle);
      }
      // 转换回数据值, 这里就是实际的值, 默认保留2位小数.
      let value = (currentAngle * (maxValue / dataRatio)).toFixed(2);
      mpaOption.series[0].data[0].value = value;
      mpaChart.setOption(mpaOption);
    }
    // 这里使用 zrender 的事件监听可以监听到画布的所有鼠标事件.
    mpaChart._zr.on("mousedown", function(event) {
      changeValue(event);
      mpaChart._zr.on("mousemove", changeValue);
    });
    mpaChart._zr.on("mouseup", function(event) {
      mpaChart._zr.off("mousemove", changeValue);
    });
  }
  function mmChange() {
    mmOption.series[0].data = [mmValue];
    mmChart.setOption(mmOption);
  }
</script>
<h1>左右竖向排列</h1>
<div style="display: flex;
    height: {size}px;
    ">
  <div id="gauge" style="height:100%; width: {radius+10}px;" use:gauge></div>
  <div id="bar" style="position: relative;height:100%; width: 45px;" use:bar>
<input style="
    position: absolute;
    width: {size-20}px;
    height: 20px;
    top: {radius-2}px;
    left: -{radius-10}px;
    z-index: 100;
    outline: none;
    appearance: none;
    background: rgba(255, 0, 0, 0);
    transform: rotate(270deg);
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