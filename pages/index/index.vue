<template>
	<view >
		<search preholder="搜索附近位置" @toSearch="toSearchFun"></search>
		<search preholder="搜索附近位置2" :isToAll="isToAll" :themeColor="themeColor" @toSearch="toSearchFun" @toAll="toAllFun"></search>
	</view>
</template>

<script>
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
</script>

<style lang="scss">
	
</style>
