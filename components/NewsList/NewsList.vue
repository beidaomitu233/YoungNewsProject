<template>
	<view class="NewSlist">
		
		<view class="NewsPictrue">
			<image :src="item.picurl" mode="aspectFill"></image>
			
		</view>
		
		<view class="NewsText">
			<view class="Tittle">
				{{item.title}}
			</view>
			<view class="Describe">
				新闻的描述新闻的描述新闻的描述新闻的描述	
			</view>
			
			<!-- 历史页面的项只显示时间，不显示作者等信息。所以就通过判断
			 父组件是否传这个参数来判断是否是历史页面。
			 -->
			<view class="info" v-if="!item.looktime">
				
					<view class="author">
							作者:{{item.author}}
					</view>
					<view class="hits">
							浏览量：{{item.hits}}
					</view>
			</view>
			<view class="info" v-else>
				浏览时间：{{item.looktime}}
			</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		name:"NewsList",
		props:{
				
			item:{
				type:Object,
				default(){
					return{
						title:'组件默认标题踢踢踢踢',
						author:'xxx',
						hits:998,
						picurl:'/static/images/0.jpg',
						looktime:'2023-9-9'
					}
				}
			}
		},
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="less">
	
.NewSlist{
	display: flex;
		// 照片部分
	.NewsPictrue{
		width: 230rpx;
		height: 160rpx;
		
		image{
			width: 100%;
			height: 100%;
			}
		}
// 新闻文字部分

		.NewsText{
			flex: 1;
			display: flex;
			flex-direction: column;
			padding: 30rpx;
			padding-top: 0rpx;
			padding-left: 20rpx;

			
			.Tittle{
				font-size: 38rpx;
				color: #333;
				overflow: hidden;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-line-clamp: 1;
				-webkit-box-orient: vertical;
				
			}
			.Describe{
				width: 100%;
				margin-top: 15rpx;
				color: #999;
				font-size: 26rpx;
				
				overflow: hidden;
				white-space: normal;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-line-clamp: 2;
				-webkit-box-orient: vertical;
				
				
				
			}
		
		// 基本信息
		.info{
			margin-top: 5rpx;
			display: flex;
			flex-direction: row;
			color:#999;
			font-size: 26rpx;
			view{
				flex:1;
				
				overflow: hidden;
				white-space: normal;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-line-clamp: 1;
				-webkit-box-orient: vertical;
			}
		}
			
	}
	
	}
</style>