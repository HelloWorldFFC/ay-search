
## 前言
简介：
1.搜索框，可自定义提示文字，可点更多
2.关键字调地图api得到搜索列表，取消按钮可自由变色
3.历史搜索、热门搜索

## 有疑问
微信搜索“慢慢向好”小程序，找客服反馈，相应问题。
				  

## 素材
[图片资源](https://pixabay.com)
 
## 开始使用

## 使用搜索框的展示

下载源码解压，复制`/components` 下的组件至项目根目录的 `/components` 文件夹下


`index.vue`的`script`加入如下部分：
```
import search from '@/components/ay-search/search.vue';
	export default {
		components: {
			search,

		},
		data() {
			return {
				themeColor:'#33CCCC',
				isToAll : true ,
			}
		},
		onLoad() {
			let that = this;

		},

		methods: {
			toSearchFun(e) {
				
			},
			toAllFun(){
				
			},
		}
	}
```


`index.vue`的`template`加入如下部分：
```
<view >
		<search preholder="搜索附近位置" @toSearch="toSearchFun"></search>
		<search preholder="搜索附近位置2" :isToAll="isToAll" :themeColor="themeColor" @toSearch="toSearchFun" @toAll="toAllFun"></search>
	</view>

```

引入阿里矢量图，复制示例源码`/style` 下的`/iconfont.css`至项目根目录的 `/style` 文件夹下

页面`App.vue` 引入css
```
<style>
	/*每个页面公共css */
	@import './style/iconfont.css';
</style>
```

## 使用关键字得到搜索列表的展示，包括历史搜索和热门搜索
## 说明项目下载，填入自己的高德key，微信小程序即可运行，其余也是配置好按理也行
## 前期准备
[高德地图申请key](https://lbs.amap.com/api/wx/guide/create-project/config-project)
[百度地图申请H5的ak](http://lbsyun.baidu.com/apiconsole/key)

H5引用百度地图搜关键字需执行命令 

```
npm install vue-baidu-map --save

```

下载js：[高德地图微信小程序js下载地址](https://lbs.amap.com/api/wx/gettingstarted)

微信小程序后台，需设置服务器域名
```
https://restapi.amap.com

```
新建`search.vue`的`script`加入如下部分：
```
import aSearchList from '@/components/ay-search/ay-search-list.vue';
	
	//#ifdef MP-WEIXIN
	var amapFile = require('../../js/gaodemap/amap.js');
	// key 是在高德地图开发者平台申请的小程序密钥 详见 https://lbs.amap.com/api/wx/guide/create-project/config-project */
	let amapPlugin = new amapFile.AMapWX({
		key: '*****',
	})
	// #endif

	//#ifdef APP-PLUS
	let mapSearch = weex.requireModule('mapSearch')
	// #endif

	// 百度地图
	//#ifdef H5
	//H5引用百度地图需执行命令 npm install vue-baidu-map --save
	import Vue from 'vue'
	import BaiduMap from 'vue-baidu-map'
	Vue.use(BaiduMap, {
		// ak 是在百度地图开发者平台申请的密钥 详见 http://lbsyun.baidu.com/apiconsole/key */
		ak: '*****'
	})
	// #endif

	
	export default {
		data() {
			return {
				latlgitude: {
					latitude: 22.5427624046,
					longitude: 114.0579724734,
				},
				map_search_radius: 40000, //单位米
				BMap: null,
				map: null,
				defaultKw: '搜索关键字',
				searchKw: '',
				kw: "",
				kwList: [],
				hotKwList: [
					{
						kw: '海岸城购物中心',
						lng: 113.935631, //经度
						lat: 22.516988, //纬度
						adrs: '文心五路33号'
					},
					{
						kw: '海上世界',
						lng: 113.917051, //经度
						lat: 22.482804, //纬度
						adrs: '望海路1128号'
					},
					{
						kw: '花园城',
						lng: 113.923004, //经度
						lat: 22.502386, //纬度
						adrs: '南海大道1145号'
					},
				],
				
			}
		},
		onLoad(options) {
			let that = this;

			//console.log(options.searchType)
			//let data = options.data ? JSON.parse(decodeURIComponent(options.data)) : false;
			let data = options.searchType ? options.searchType : 1;
			if (data) {
				that.searchType = parseInt(data);
				//console.log(that.searchType)
			}
			this.init();
		},
		onReady: function() {

		},
		components: {
			//引用组件
			aSearchList
		},
		mounted() {

		},
		methods: {
			
			//加载热门搜索
			loadHotKw() {
				//定义热门搜索关键字
				
			},

			setListByMap_wx(kw) {
				let that = this;

				let latlg = that.latlgitude;
				let lat_local = latlg.latitude;
				let lng_local = latlg.longitude;

				let keyw = kw.trim();
				if (keyw.length > 0) {
					//keyw += that.searchKeyword ;
				} else {
					that.kwList = [];
					return;
				}
				let kwList = [];
				//console.log('**************');
				let city = '';
				let lonlat = {
					latitude: lat_local,
					longitude: lng_local,
				};
				//console.log(lonlat)
				amapPlugin.getInputtips({
					keywords: keyw,
					city: city,
					location: lonlat,
					success: (ret) => {
						console.log(JSON.stringify(ret));
						kwList = that.getListBymap_wx(ret);
						that.kwList = kwList;
					}
				})

				
			},

			setListByMap_app(kw) {
				let that = this;

				let latlg = getApp().globalData.latlgitude;
				let lat_local = latlg.latitude;
				let lng_local = latlg.longitude;

				let keyw = kw.trim();
				if (keyw.length > 0) {
					//keyw += that.searchKeyword ;
				} else {
					that.kwList = [];
					return;
				}
				let kwList = [];

				mapSearch.poiSearchNearBy({
					point: {
						latitude: lat_local,
						longitude: lng_local
					},
					key: keyw,
					radius: that.map_search_radius,
					index: 1,
				}, ret => {
					// console.log('--------');
					// console.log(JSON.stringify(ret));
					// console.log(JSON.stringify(ret.poiList));
					// console.log(JSON.stringify(ret.poiList.length));
					// console.log(JSON.stringify(ret.poiList[0]));
					// console.log(JSON.stringify(ret.currentNumber));
					// console.log(JSON.stringify(ret.pageIndex));
					// console.log(JSON.stringify(ret.pageNumber));


					// uni.showModal({
					//     content: JSON.stringify(ret)
					// })
					kwList = that.getListBymap(ret);
					that.kwList = kwList;
					//var poi = searchResult.getPoi(0);

				})

			},
			clearkwList(){
				this.kwList = [];
			},
			setListByMap(kw) {
				//#ifdef H5
				this.setListByMap_H5(kw);
				// #endif

				//#ifdef APP-PLUS
				this.setListByMap_app(kw);
				// #endif

				//#ifdef MP-WEIXIN
				this.setListByMap_wx(kw);
				// #endif
			},
			getListBymap_wx(ret) {
				let that = this;
				let sList = [];
				var sHr = ret.tips;
				for (let i = 0; i < sHr.length; i++) {
					let item = sHr[i];
					let location = item.location ;//"location":"120.303578,31.486577" 
					if(location.length>0){
						let latlngArr = location.split(',')
						let obj = {
							kw: item.name,
							lat: latlngArr[1]||'',
							lng: latlngArr[0],
							address: item.address,
						}
						//看是否有相同项，有相同项则不保存
						let oldHave = false;
						sList.forEach(item => {
							if (item.kw == obj.kw) {
								oldHave = true;
							}
						})
						if (!oldHave) {
							sList.push(obj);
						}
					}
					

				}
				//console.log(sList)
				return sList;
			},

			getListBymap(ret) {
				let that = this;
				let sList = [];
				var sHr = ret.poiList;
				for (let i = 0; i < sHr.length; i++) {
					let item = sHr[i];
					let obj = {
						kw: item.name,
						lat: item.location.latitude,
						lng: item.location.longitude,
					}
					//看是否有相同项，有相同项则不保存
					let oldHave = false;
					sList.forEach(item => {
						if (item.kw == obj.kw) {
							oldHave = true;
						}
					})
					if (!oldHave) {
						sList.push(obj);
					}

				}
				//console.log(sList)
				return sList;
			},

			mapReady({
				BMap,
				map
			}) {
				this.BMap = BMap;
				this.map = map;

			},

			async setListByMap_H5(kw) {
				console.log('searchResult')
				let that = this;

				let keyw = kw.trim();
				if (keyw.length > 0) {
					//keyw += that.searchKw ;
				} else {
					that.kwList = [];
					return;
				}

				var localSearch = new this.BMap.LocalSearch(this.map);
				let sList = [];
				localSearch.setSearchCompleteCallback(function(searchResult) {
					console.log('searchResult')
					console.log(searchResult)
					console.log(searchResult.Hr.length)
					var sHr = searchResult.Hr;
					for (let i = 0; i < sHr.length; i++) {
						let item = sHr[i];
						let obj = {
							kw: item.title,
							lat: item.point.lat,
							lng: item.point.lng,
						}
						//看是否有相同项，有相同项则不保存
						let oldHave = false;
						sList.forEach(item => {
							if (item.kw == obj.kw) {
								oldHave = true;
							}
						})
						if (!oldHave) {
							sList.push(obj);
						}


					}
					//var poi = searchResult.getPoi(0);
					console.log(sList)
					that.kwList = sList;
				});

				localSearch.search(keyw);

			},

			init() {
				this.loadHotKw();
			},
			
			//执行搜索
			doSearch(item) {
				let kw = item.kw;
				this.kw = item.kw;
				
				//高德地图：gcj02坐标，也称为火星坐标
				//console.log(item)
				uni.openLocation({
					latitude: parseFloat(item.lat), //要去的纬度-地址       
					longitude: parseFloat(item.lng), //要去的经度-地址
					name: item.kw, //地址名称
					address: item.address || '', //详细地址名称
					scale: 10,
					success: function() {
						console.log('导航成功');
					},
					fail: function(error) {
						console.log(error)
					}
				});

			},
		}
	}


```

`search.vue`的`template`加入如下部分：
 ```
 <view >
 	
 	<aSearchList 
 	
 	:placeholder="defaultKw"
 	:kwList="kwList" 
 	:hotKwList="hotKwList" 
 	@clearkwList="clearkwList"
 	@setListByKw="setListByMap"
 	@doSearch="doSearch"></aSearchList>
 	<!--  #ifdef H5  -->
 	<view class="map">
 		<baidu-map class="map-contain" @ready="mapReady">
 		
 		</baidu-map>
 	</view>
 	<!-- #endif -->
 </view>
 ```
 引入阿里矢量图，复制示例源码`/style` 下的`/iconfont.css`至项目根目录的 `/style` 文件夹下
 
 页面`App.vue` 引入css
 ```
 <style>
 	/*每个页面公共css */
 	@import './style/iconfont.css';
 </style>
 ```
 
 ## 参考插件
[图片资源](https://ext.dcloud.net.cn/plugin?id=91)