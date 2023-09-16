<template>
	<view>
		
		<!-- 用户历史 -->
		<view class="HistoryIcon" v-if="histArr.length">
			<image src="../../static/images/history.png" mode="aspectFill"></image>
		</view>
		
		<view class="HistoryItem" v-for="item in histArr">
			<NewsList :item="item" @click.native="goToNews(item)"></NewsList>
		</view>
		
		<!-- 如果没有浏览历史就显示一个图片 -->
		<view class="" v-if="!histArr.length" style=" text-align: center;">
			<image src="../../static/images/nohis.png" mode="aspectFill" style="width: 100%; "></image>
			没有浏览记录哦
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				histArr:[]
				
			};
		},
		methods:{
			getHistoryStor(){
				// 如果没有历史就会赋值为空数组
				let histArr= uni.getStorageSync("hisArr") || []
				this.histArr=histArr
				// 从本地存储中获取到的浏览历史
				console.log(this.histArr);
				this.onPullDownRefresh
			},
			// 跳转到详情页
			goToNews(item){
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
				
			},
			
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title:'浏览历史'
			})
			this.getHistoryStor()
		},
		
			
		onShow(){
			// 进入页面应该就主动刷新，防止页面数据没有渲染上来
			uni.startPullDownRefresh()


		},
		onPullDownRefresh(){
			this.getHistoryStor()
			setTimeout(()=>{
						uni.stopPullDownRefresh()
						console.log('重新获取数据完成');
						console.log(this.histArr);
			},500)
	
		}
		

		
	}
</script>

<style lang="scss">
	
	.HistoryIcon{
		display: flex;
		margin: 20rpx auto;
		width: 200rpx;
		height: 200rpx;
			
	image{
		width: 100%;
		height: 100%;
		}
	}

</style>
