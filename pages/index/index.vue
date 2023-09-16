<template>
	<view class="Content">
		
		<view class="ScrollNav">
			<!-- 导航栏模块 -->
			<scroll-view scroll-x class="NavScoll" >
				<view class="NavItem " :class="isactive==item.id? 'active':''" v-for="item in Navdata" :key="item.id" :data-index="item.id"  @click="choiceitem(item.id)">
					{{item.classname}}
					
				</view>

			</scroll-view>
		</view>
		
		<!-- 新闻内容 -->
		<view class="NewsContent" >
			<NewsList @click.native="gotodetail(item.id,item.classid)"  class="NewItem"  v-for="item in NewsItem " :key="item.id" :data-NewsId="item.id"
			:item="item"
			></NewsList>
		</view>
		
		<!-- 当获取的栏目没有新闻内容时显示一个空图片  ，获取不到数据或没有数据，数组长度就是0=false，取反就是true>-->
		<view class="nodata"  v-if="!NewsItem.length">
			<image src="../../static/images/nodata.png" mode="aspectFill"></image>
		</view>
		
		<!-- 当没有数据时显示 根据请求的状态来调整提示，如果整个栏目都没有数据->0=false，那么就没有显示的必要了， -->
		<view class="loding"  v-if="NewsItem.length" >
			<view class="" v-if="loding==1">
				正在加载中
			</view>
			
			<view class="" v-else-if="loding==2">
				没有更多了.....
			</view>
			
		</view>
		
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 当前选中的导航项
				isactive:50,
				// 新闻导航栏数据
				Navdata:[],
				// 新闻目列表
				NewsItem:[],
				// 默认选择为第一页
				currentpage:1,
				loding:0  //0默认  1加载中  2没有更多了
				
			}
		},
		onLoad() {
			this.getNavData(),
			this.getNewsList()

		},
		
		onReachBottom:function() {
			console.log('到底部了');
			
			// 如果请求已经没有数据了那就没有必要翻页了	,使用this.loding==2是因为在请求时没数据就会赋值为2了，只需要判断是不是2，是就直接返回。
			if(this.loding==2){			
				return
			}
			// 每触底一次就是请求新的一页所以++
			this.currentpage++
			
			// 在发起请求前就显示记载中
			this.loding=1
			
			// 再发起一次请求,发起的请求需要根据栏目来请求，而当前点击的栏目就存储在isactive，刚好两用
			this.getNewsList(this.isactive)
			
			
	
		}
		,
		methods: {
			// 事件的目的是将当前选中的项存储到本地，然后在css中判断，如何当前选中项和index一致就显示样式。
			choiceitem(id){
					this.isactive=id
					console.log('获取到的id');
					console.log(id);
				
				// 下面这两步是页面初始化
				
					//获取栏目新闻前需要把页数的变量给重置，否之会直接请求到变量的页数。
					this.currentpage=1
					// 获取新闻前需要把变量内的原有的新闻项给清除，否则又会拼接到项目中
					this.NewsItem=[]
					
					// 将加载提示 修改为默认状态即不显示（使用v-if判断实现）
					this.loding=0
					
					// 根据点击的ID获取对应栏目的新闻
					this.getNewsList(id)
				
			},
			
			// 跳转到新闻详情页
			gotodetail(id,cid){
				uni.navigateTo({
					url:`/pages/detail/detail?id=${id}&cid=${cid}`
				})
			},
			
			// 获取导航栏数据
			getNavData(){
				uni.request({
					url:"https://www.fastmock.site/mock/b2460efce2bf87504f84149a003dde96/api/api/newlist",
					success: (res) => {
						this.Navdata=res.data
						// 
						console.log('获取到的导航栏数据');
						console.log(this.Navdata);
					}
				})
			},
			
			//获取新闻项
			getNewsList(id){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,
						page:this.currentpage
						
					},
					success: (res) => {					
						//请求没有数据返回时显示无更多
						if(!res.data.length){
							this.loding=2
						}else{
							// 将请求回来的内容做拼接，否则会覆盖掉之前的内容
							this.NewsItem=[...this.NewsItem,...res.data]						
							// 请求回数据后就隐藏提示内容
							this.loding=0
						}
						console.log('获取到的新闻项数据');
						console.log(this.NewsItem);
		
					}
				})
			}
			

		}
	}
</script>

<style scoped="scss">
	.NavScoll{
		height: 100rpx;
		background-color:#f7f8fa;
		white-space: nowrap;
		position: fixed;
		/* 这是获取最顶层的窗口对象。基于定成对象来定位 */
		--top: var(--window-top);
		left: 0%;
		/* 默认是在内容后面，所以要提高层级 */
		z-index: 10;
	}
	/deep/ ::-webkit-scrollbar {
		width: 4px !important;
		height: 1px !important;
		overflow: auto !important;
		background: transparent !important;
		-webkit-appearance: auto !important;
		display: block;
	}
	
	.NavItem{
		font-size: 40rpx;
		display: inline-block;
		line-height: 100rpx;
		padding: 0 30rpx ;
	}
	
	/* 被选中时的样式 */
	.active{
		color: #31c27c;
		
	}
	
	
	/* 新闻列表样式 */
	.NewsContent{
		padding: 30rpx;
		padding-top: 96rpx;
	}
	.NewItem{
		border-bottom: 1rpx soild blacl;
		padding: 15rpx 0;
	}
	
	.nodata{
		display: flex;
		justify-content: center;
		align-items: center;
	}
	
	.loding{
		color: #A9A9A9;
		font-size: 12rpx;
		text-align: center;
	}
	
</style>
