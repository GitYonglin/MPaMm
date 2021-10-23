<script>
  import { onMount } from "svelte";
  const echarts = require("echarts");
  let size = 360;
  $: radius = (size - 60) / 2;
  function gauge(e) {
    const myChart = echarts.init(e, null, { renderer: "svg" });
    var { width, height } = getComputedStyle(e);
    // 计算出中心点位置
    var x = parseInt(width.slice(0, -2), 0) / 2;
    var y = parseInt(height.slice(0, -2), 0) / 2 + radius / 2 - 10;
    var minAngle = 0;
    var maxAngle = 180;
    var maxValue = 100;
    var dataRatio = maxValue / maxAngle;
    var option = {
      series: [
        {
          animation: false, // 关掉动画, 否则拖动会有延迟
          name: "业务指标",
          type: "gauge",
          center: ["50%", radius],
          radius: radius,
          startAngle: maxAngle,
          endAngle: minAngle,
          pointer: {
            width: 8,
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

          data: [{ value: 20, name: "完成率" }]
        }
      ]
    };

    myChart.setOption(option);

    function changeValue(event) {
      var x2 = event.offsetX;
      var y2 = event.offsetY;
      console.log(`x=${x},y=${y}`);
      console.log(`x2=${x2},y2=${y2}`);
      // 当前点击位置的角度.
      var currentAngle = (Math.atan2(y - y2, x - x2) * 180) / Math.PI;
      // 边界处理
      if (currentAngle < minAngle || currentAngle > maxAngle) {
        let _angle = Math.abs(currentAngle);
        if (_angle > 90) {
          currentAngle = maxAngle;
        } else {
          currentAngle = minAngle;
        }
      }
      // 转换回数据值, 这里就是实际的值, 默认保留2位小数.
      let value = (currentAngle * dataRatio).toFixed(2);
      option.series[0].data[0].value = value;
      myChart.setOption(option);
    }
    // 这里使用 zrender 的事件监听可以监听到画布的所有鼠标事件.
    myChart._zr.on("mousedown", function(event) {
      changeValue(event);
      myChart._zr.on("mousemove", changeValue);
    });
    myChart._zr.on("mouseup", function(event) {
      myChart._zr.off("mousemove", changeValue);
    });
  }
</script>
<h1>上线横向排列2</h1>
<div style="display: flex;
flex-direction: column;
    width: {size}px;
    justify-content: space-between;
    ">
  <div id="gauge" style="width: 100%; height: {radius+10}px;" use:gauge></div>

</div>