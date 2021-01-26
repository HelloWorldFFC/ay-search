<template>
	<view class="serach">
		<view class="content" :style="{'border-radius':radius+'px'}">

			<view class="content-box" >
				<text class="iconfont icon-sousuo1"></text>

				<input  :placeholder="placeholder" @input="inputChange" confirm-type="search" class="input"
				 :focus="isFocus" v-model="inputVal" @focus="focus" @blur="blur" />

				<text v-if="isDelShow" class="iconfont icon-guanbi" @click.stop="clear"></text>
			</view>

			<view v-if="button === 'inside'" class="serachBtn" :style="style_serachBtn" @click="searchCancal">
				{{cancalName}}
			</view>
		</view>
		<view v-if="button === 'outside'" class="button active"   @click="searchCancal">
			<view class="button-item">{{cancalName}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			//定义默认搜索关键词(水印文字)
			placeholder: {
				value: String,
				default: '请输入搜索内容'
			},
			value: {
				type: String,
				default: false
			},
			button: {
				value: String,
				default: 'outside'
			},
			show: {
				value: Boolean,
				default: true
			},
			radius: {
				value: String,
				default: 60
			},
			themeColor: {
				type: String,
				default: '#33CCCC',
			},
			themeColor_dan: {
				type: String,
				default: '#AFEEEE',
			},
		},
		computed: {
			
			style_serachBtn(){
				let that = this ;
				var themeColor_dan = that.themeColor_dan;
				var themeColor = that.themeColor;
				var style = '' ;
				//background: linear-gradient(to right, #ff9801, #ff570a);
				style += `background: linear-gradient(to right, ${themeColor_dan}, ${themeColor});`;
				
				return style ;
			},
		},
		data() {
			return {
				active: false,
				inputVal: '',
				cancalName: '取消',
				searchName: '取消',
				isDelShow: false,
				isFocus: true
			};
		},
		methods: {
			//触发组件confirm事件
			triggerConfirm() {
				this.$emit('confirm', false);
			},
			//触发组件input事件
			inputChange(event) {
				var keyword = event.detail.value;
				this.$emit('input', keyword);
				if (this.inputVal) {
					this.isDelShow = true;
				}
			},
			focus() {
				this.active = true;
				//增加获取焦点判断
				if (this.inputVal) {
					this.isDelShow = true;
				}
			},
			blur() {
				console.log('blur');
				this.isFocus = false;
				if (!this.inputVal) {
					this.active = false;
				}
			},
			clear() {
				//收起键盘
				uni.hideKeyboard();
				this.isFocus = false;
				this.inputVal = '';
				this.active = false;
				//清空内容时候触发组件input
				this.$emit('input', '');
				//this.$emit('search', '');//清空内容时候不进行搜索

			},
			getFocus() {
				if (!this.isFocus) {
					this.isFocus = true;
				}

			},
			search() {

				this.$emit('search', null);
			},
			searchCancal() {

				uni.hideKeyboard();
				this.isFocus = false;
				this.active = false;

				this.$emit('searchCancal', false);
			},
		},
		watch: {
			inputVal(newVal) {
				if (!newVal) {
					this.isDelShow = false;
				}
			},
			//双向绑定
			value(val) {
				this.inputVal = val;
			},
		}
	};
</script>

<style lang="scss">
	.icon-guanbi{
		padding-right: 20upx;
	}
	.icon-sousuo1{
		padding: 0 15upx 0 20upx;
		color: grey;
	}
	
	.serach {
		display: flex;
		width: 100%;
		//border-bottom: 1px #f5f5f5 solid; //去掉边框
		box-sizing: border-box;
		font-size: $uni-font-size-base;

		.content {
			display: flex;
			align-items: center;
			width: 100%;
			height: 60upx;
			//border: 1px #ccc solid; // 去掉边框
			background: #fff;
			overflow: hidden;
			transition: all 0.2s linear;
			border-radius: 30px;

			.content-box {
				width: 100%;
				display: flex;
				align-items: center;

				
				.input {
					// padding-left: 20upx;
					width: 100%;
					max-width: 100%;
					line-height: 60upx;
					height: 60upx;
					transition: all 0.2s linear;

					&.center {
						width: 200upx;
					}

					&.sub {
						// position: absolute;
						width: auto;
						color: grey;
					}
				}
			}

			.serachBtn {
				height: 100%;
				flex-shrink: 0;
				padding: 0 30upx;
				//按钮背景色
				//background: linear-gradient(to right, #ff9801, #ff570a);
				//background: $uni-color-success;
				line-height: 60upx;
				color: #fff;
				//border-left: 1px #ccc solid; //去掉边框
				transition: all 0.3s;
			}
		}

		.button {
			display: flex;
			align-items: center;
			justify-content: center;
			position: relative;
			flex-shrink: 0;
			width: 0;
			transition: all 0.2s linear;
			white-space: nowrap;
			overflow: hidden;

			&.active {
				padding-left: 15upx;
				width: 100upx;
			}
		}
	}

	
</style>

<!-- 参考：https://ext.dcloud.net.cn/plugin?id=91 -->