柱状图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/echarts.min.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
		<!-- 为ECharts准备一个具备大小（宽高）的Dom -->
		<div id="main" style="width: 600px;height:400px;"></div>
		<script type="text/javascript">
			var myChart = echarts.init(document.getElementById('main'));
			var option = {
				tooltip: {},
				legend: {
					data: ['订单数(条)', '订单金额(万元)']
				},
				xAxis: {
					data: [1, 2, 3, 4, 5, 6]
				},
				yAxis: {},
				series: [{
					name: '订单数(条)',
					type: 'bar',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}, {
					name: '订单金额(万元)',
					type: 'bar',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}]
			};
			myChart.setOption(option);
		</script>
	</body>
</html>

```

折线图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/echarts.min.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
		<!-- 为ECharts准备一个具备大小（宽高）的Dom -->
		<div id="main" style="width: 600px;height:400px;"></div>
		<script type="text/javascript">
			var myChart = echarts.init(document.getElementById('main'));
			var option = {
				tooltip: {},
				legend: {
					data: ['订单数(条)', '订单金额(万元)']
				},
				xAxis: {
					data: [1, 2, 3, 4, 5, 6]
				},
				yAxis: {},
				series: [{
					name: '订单数(条)',
					type: 'line',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}, {
					name: '订单金额(万元)',
					type: 'line',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}]
			};
			myChart.setOption(option);
		</script>
	</body>
</html>

```

饼图

```html
    option = {
        title: {
            text: '某站点用户访问来源',
            subtext: '纯属虚构',
            left: 'center'
        },
        tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
            orient: 'vertical',
            left: 'left',
            data: ['直接访问', '邮件营销', '联盟广告', '视频广告', '搜索引擎']
        },
        series: [
            {
                name: '访问来源',
                type: 'pie',
                radius: '55%',
                center: ['50%', '60%'],
                data: [
                    {value: 335, name: '直接访问'},
                    {value: 310, name: '邮件营销'},
                    {value: 234, name: '联盟广告'},
                    {value: 135, name: '视频广告'},
                    {value: 1548, name: '搜索引擎'}
                ],
                emphasis: {
                    itemStyle: {
                        shadowBlur: 10,
                        shadowOffsetX: 0,
                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                    }
                }
            }
        ]
    };

```

散点图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/echarts.min.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
		<!-- 为ECharts准备一个具备大小（宽高）的Dom -->
		<div id="main" style="width: 600px;height:400px;"></div>
		<script type="text/javascript">
			var myChart = echarts.init(document.getElementById('main'));
			var option = {
				tooltip: {},
				legend: {
					data: ['订单数(条)', '订单金额(万元)']
				},
				xAxis: {
					data: [1, 2, 3, 4, 5, 6]
				},
				yAxis: {},
				series: [{
					name: '订单数(条)',
					type: 'scatter',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}, {
					name: '订单金额(万元)',
					type: 'scatter',
					data: [1, 2, 3, 4, 5, 6],
					markPoint: {
						data: [{
							type: 'max'
						}, {
							type: 'min'
						}]
					},
					markLine: {
						data: [{
							type: 'average'
						}]
					}

				}]
			};
			myChart.setOption(option);
		</script>
	</body>
</html>

```

