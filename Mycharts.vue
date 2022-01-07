<template>
	<div>
		<x-header :left-options="{showBack: false}">企业门户
		</x-header>

				<selector  :options="EPSList" v-model="epsvalue" @on-change="onChange"></selector>
				<div class="hr"></div>
		<!--echarts-->
		<div :style='Supervise_Scroller'>
			<div class="superviseTable">
				<tab>
					<tab-item class="tab-item-left" selected>在建项目预览</tab-item>
					<!-- 在建项目预览 -->
				</tab>
				<group class='img_list'>
					<div class="file-img-cell">
						<div v-for='img, i in fileimglist'>
							<div class="file-cell-content" v-on:click="ShowImg(img.icountImg, img.isImg)">
								<span class="img-del">  </span>
								<!--上传图片-->
								<div class="img-show">
									<img v-if='img.src && img.isImg' style='width:100%; height:100px; float: left;' class="previewer-demo-img" :src="img.src" width="100">
									<i v-else class="fa fa-file"></i>
								</div>
								<div id="img-title">
									<p class="img-title">{{img.FILE_NAME}}</p>
								</div>
							</div>
						</div>
					</div>
				</group>
			</div>
			<div class="superviseTable">
				<tab>
					<tab-item class="tab-item-left" selected>总控计划达成率（按专业）全公司</tab-item>
				</tab>
				<div class="outer"></div>
			</div>
			<div class="superviseTable">
				<tab>
					<tab-item class="tab-item-left" selected>全国（业态）质量关闭率</tab-item>
				</tab>
				<br />
				<div id="qualitychart" style="width: 100%;height: 300px;"></div>
			</div>
		</div>

		<!-- 导航 -->
		<Home></Home>
		<!-- 图片预览 -->
		<previewer :list="attachListImg" ref="previewer" :options="options"></previewer>
	</div>
</template>

<script>
	import { Group, Previewer, Tab, TabItem, XHeader, Flexbox, FlexboxItem, Divider, Box, Icon, Tabbar, Selector, Popup, TabbarItem } from 'vux'
	import Home from './Home'
	import echarts from 'echarts'
	import $ from 'jquery'

	export default {
		components: {
			Group,
			Previewer,
			Tab,
			TabItem,
			XHeader,
			Selector,
			Popup,
			Home,
			Flexbox,
			FlexboxItem,
			Divider,
			Box,
			Icon,
			Tabbar,
			TabbarItem
		},
		data() {
			return {
				basedata: [{
					type: 0,
					dataarr: [],
					filearr: [{
						FILE_NAME: '图片1',
						src: 'static/images/会议图片1.jpg'
					}, {
						FILE_NAME: '工程验收',
						src: 'static/images/会议图片2.jpg'
					}, {
						FILE_NAME: '图片1',
						src: 'static/images/会议图片3.jpg'
					}]
				}, ],
				Datas: [],
				isAdmin: false,
				epsvalue: '泰康之家',
				EPSList: ['泰康之家', '纪念同泰项目', '杭州竹茶园项目', '罗浮山', '泰康之家本部', '泰康金融大厦', '泰康昌平生命科学园', '泰康人寿大厦'],
				Supervise_Scroller: '',
				fileimglist: [],
				attachListImg: [],
				options: { // 图片预览配置
					getThumbBoundsFn(index) {
						let thumbnail = document.querySelectorAll('.previewer-demo-img')[index]
						let pageYScroll = window.pageYOffset || document.documentElement.scrollTop
						let rect = thumbnail.getBoundingClientRect()
						return { x: rect.left, y: rect.top + pageYScroll, w: rect.width }
					}
				},
			}
		},
		activated() {
			this.initScrollerHeight()
			this.setstuta()
			this.getMeetAttachment()

		},
		mounted() {
			this.$nextTick(function() {
				this.initcharts()
				this.qualitychart('qualitychart')
			})
		},
		methods: {
			initScrollerHeight() {
				var height = document.documentElement.clientHeight - 146
				this.$nextTick(() => {
					this.Supervise_Scroller = 'height:' + height + 'px;overflow-y: auto;overflow-x: hidden;'
				})
			},
			setstuta() {
				var that = this;
				that.fileimglist = that.basedata[0].filearr
			},
			getMeetAttachment() {
				this.attachListImg = []
				var icountImg = 0
				var that = this;
				var arr = that.fileimglist;
				for(var i = 0; i < arr.length; i++) {
					if((arr[i].src).indexOf('.jpg') != -1) {
						arr[i].isImg = 'true'
						arr[i].icountImg = icountImg;
						icountImg++
						var obj = {
							src: arr[i].src,
							isImg: true,
							w: 600,
							h: 600,
							icountImg: icountImg
						}
						this.attachListImg.push(obj)
					}
				}
				var count = this.fileimglist.length
			},
			ShowImg(index, isImg) {
				if(isImg && index || index === 0) {
					this.$refs.previewer.show(index)
				}
			},
			onChange(val) {
				if(val == '泰康之家') {
					this.isAdmin = true
				} else {
					this.isAdmin = false
				}
			},
			creatdiv() {
				var arr = [
					[12, 80],
					[19, 70],
					[20, 50],
					[15, 44],
					[10, 60],
					[15, 44],
					[10, 60]
				]
				var num = arr.length;
				var i = Math.ceil(num / 2)
				for(var a = 0; a < i; a++) {
					var html = '<div id="div' + (1 + a * 2) + '" class="circleH"></div><div id="div' + (2 + a * 2) + '" class="circleH"></div><div style="clear: both;"></div>'
					$(html).appendTo($('.outer'));
				}
			},
			initcharts() {
				this.creatdiv()
				var arr = [
					[12, 80],
					[19, 70],
					[20, 50],
					[15, 44],
					[10, 60],
					[15, 44],
					[10, 60]
				]
				var num = arr.length;
				var divarr = $('.outer').find('[id]')
				for(var i = 0; i < num; i++) {
					var labelTop = {
						normal: {
							color: '#ccc',
							label: {
								show: true,
								position: 'center',
								formatter: '{b}',
								textStyle: {
									color: '#2393FA',
									baseline: 'bottom'
								}
							},
							labelLine: {
								show: false
							}
						}
					};
					var labelFromatter = {
						normal: {
							label: {
								formatter: '{d}%',
								textStyle: {
									color: '#B5B999',
									baseline: 'top'
								}
							}
						},
					}
					var labelBottom = {
						normal: {
							color: '#2393FA',
							label: {
								show: true,
								position: 'center'
							},
							labelLine: {
								show: false
							}
						},
						emphasis: {
							color: 'rgba(0,0,0,0.5)'
						}
					};
					var radius = [32, 40];
					var namearr = ['项目招采', '总控计划', '专项计划', '材料计划', '实施', '总控计划', '专项计划']
					var valarr1 = [12, 19, 20, 15, 10, 30, 25]
					var valarr2 = [80, 70, 50, 44, 60, 40, 66]
					var a = ''
					var b = ''
					var c = ''
					a = namearr[i]
					b = valarr1[i]
					c = valarr2[i]
					this.charts = echarts.init(document.getElementById(divarr[i].getAttribute("id")))
					this.charts.setOption({
						legend: { show: false, },
						series: [{
							type: 'pie',
							center: ['50%', '50%'],
							radius: radius,
							itemStyle: labelFromatter,
							data: [{
								name: 'other',
								value: b,
								itemStyle: labelBottom
							}, {
								name: a,
								value: c,
								itemStyle: labelTop,
							}],
						}]
					})

				}
			},
			qualitychart(id) {
				this.charts = echarts.init(document.getElementById(id))
				this.charts.setOption({
					backgroundColor: 'white',
					tooltip: {
						trigger: 'axis',
						axisPointer: { // 坐标轴指示器，坐标轴触发有效
							type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
						}
					},
					legend: {
						data: ['待解决', '待复核', '已关闭'],
						x: 'center',
					},
					color: ['#2393FA', '#125488', '#9CB6C1'],
					grid: {
						x: 65,
						y: 10,
						x2: 10,
						y2: 10
					},
					xAxis: {
						show: false
					},
					yAxis: {
						data: ['养老', '医疗', '写字楼', '纪念园'],
						splitLine: { show: false },
						axisLine: {
							show: false
						},
						axisTick: {
							show: false
						},
						axisLabel: {
							textStyle: {
								color: 'black'
							}
						}
					},
					series: [{
						name: '待解决',
						type: 'bar',
						stack: '1',
						barWidth: 18,
						label: {
							color: '#000',
							normal: {
								show: true,
								position: 'insideRight',
								textStyle: {
									color: 'white'
								}
							}
						},
						data: [64, 43, 59, 54],
					}, {
						name: '待复核',
						type: 'bar',
						stack: '1',
						barWidth: 18,
						label: {
							normal: {
								show: true,
								position: 'insideRight',
								textStyle: {
									color: 'white'
								}
							}
						},
						data: [14, 13, 19, 18],
					}, {
						name: '已关闭',
						type: 'bar',
						stack: '1',
						barWidth: 18,
						label: {
							normal: {
								show: true,
								position: 'insideRight',
								textStyle: {
									color: 'white'
								}
							}
						},
						data: [14, 13, 19, 18],
					}]
				})
			},
		}
	}
</script>

<style type="text/css">
	.circleH {
		width: 50%;
		height: 100px;
		float: left;
	}
</style>