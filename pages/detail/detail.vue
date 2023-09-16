<template >
	<view class="container">
		
		<view class="Tittle">
			{{contentdel.title}}
		</view>
		
			<view class="info">
				<view class="author">
					作者：{{contentdel.author}}
				</view>
				<view class="time">
					时间：{{contentdel.posttime}}
				</view>
			
			</view>
		
		<view class="Content">
			
			<!-- 通过富文本来渲染HTML结构  并且通过CSS来控制里面的图片大小-->
			<rich-text :nodes="contentdel.content"></rich-text>
			
			
		</view>
		
		
		 
		<view class="statement">
			声明：本站的内容均采集与腾讯新闻，如果侵权请联系管理(126272713@qq.com)进行整改刚除，本站进行了内容采集不代表本站及作者观点，若有
			侵犯请及时联系管理员，谢谢您的支持。
		</view>
		
		
	</view>
</template>

<script>
	// 引入工具类格式化时间
	import {parseTime} from "@/util/tools.js"
	export default {
		data() {
			return {
				option:null,
				contentdel:{}
			};
		},
		// 获取传递过来的参数
		onLoad(option) {
			console.log(option);
			this.option=option
			
			this.getNewsDetail()
		},
		methods:{
		// 获取新闻详情页内容
			getNewsDetail(){
				uni.request({
					url:'https://ku.qingnian8.com/dataApi/news/detail.php',
					data:this.option  ,//直接传递参数对象
					success:(res)=> {
						// 格式化完数据后再进行赋值
						res.data.posttime=parseTime(res.data.posttime)
						this.contentdel=res.data
						console.log(res);
						console.log(this.contentdel);
						
						// 存储到历史记录中,再detail里写是因为已经点进来了，所以肯定是看过了...
						this.saveHistory()
						
						
						// 获取完信息后设置标题,使用请求回来的数据
						uni.setNavigationBarTitle({
							title:this.contentdel.title
						})
					}
					
				})
			},
			
			// 存储历史记录  使用本地存储
			saveHistory(){
				let timestamp = Date.now(); // 获取当前时间戳
				
				// 将部分数据结构存储到本地存储里再历史记录页面调用
				let item = {
				  id: this.contentdel.id,
				  classid: this.contentdel.classid,
				  picurl: this.contentdel.picurl,
				  // 时间要设置当前查看的时间。不要求去重
				  looktime: parseTime(timestamp),  
				  title: this.contentdel.title
				};
				
				// 需要拼接已有的历史记录,要不然每次存储都会覆盖,没有记录就复制空数组
				let hisArr=uni.getStorageSync("hisArr") || []
				
				// 追加当前的记录对象
				hisArr.unshift(item)
				
				uni.setStorageSync("hisArr",hisArr)
				
	
			}
			
		}
	}
</script>

<style lang="less">
	
	.container{
		padding: 30rpx;
	}
	
	.Tittle{
		font-size: 46rpx;
		color:#333
	}
	.info{
		display: flex;
		justify-content:space-between;
		padding: 10rpx;
		font-size: 22rpx;	
		color:#666;
		background:#F6F6F6  ;
		margin: 40rpx 0;
	}
	
	.Content{
		padding: 20rpx;
	}
	
	.Content img{
		// 只用写最大宽度即可。
		max-width: 100%;

	}

	.statement{
		background: #FEF0F0;
		font-size: 26rpx;
		padding:20rpx;
		color:#F89898;
		line-height: 1.8em;
	}

</style>
