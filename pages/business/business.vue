<template>
	<view>
		<view :style="{'height':navigation.height+'px','paddingTop': navigation.top + 'px','paddingBottom':'10rpx'}" >
			<view :style="{'height':navigation.height+'px','width':navigation.left+'px'}" class="topNavigation">
				<view class="search"  @click="Tsearch">
					<view class="search_box">
						<view class=".search_icon">
							<image src="../../static/search_icon.png" mode="" style="width: 100%;height: 100%;"></image>
						</view>
						<view class="">
							水煮牛肉
						</view>
					</view>
				</view>
			</view>
		</view>
		
		
		<view class="screen_box">
			<view class="screen_left">
				
				<view class="select_item">
					<!-- <picker mode="selector" :range="sorte" range-key="name" @change="sortchange">
						
					</picker> -->
					<view style="font-weight: bold;font-size: 30rpx;color: #007AFF;" @click="xian = !xian">{{sorte[storeIndex].name}}
						<uni-icons type="arrowdown" color='#007AFF' ></uni-icons>
					</view>
					
				</view>
				<view class="select_item">
					距离
				</view>
				<view class="select_item">
					销量
				</view>
			</view>
			<view class="screen"  @click="sai =! sai">
				筛选 <image src="../../static/screen.png" mode="" style="width: 28rpx;height: 28rpx;"></image>
			</view>
			
			<view class="drop_down_box" v-if="xian">
				<view class="choice" v-for="(item,index) in sorte" :key='index' @click="storeIndex = index,xian = false">
					<view :class="index == storeIndex?'choiceselect':''"> 
						{{item.name}}
					</view>
					<view class="" v-if="index == storeIndex">
						<uni-icons type="checkmarkempty" size="20" color='#007AFF'></uni-icons>
					</view>
				</view>
			</view>
			
			<view class="drop_down_box temp_deop" v-if="sai">
				<view class="title">
					商家服务
				</view>
				<view class="tc_box">
					<view class="tc_item">
						<view class="tc_img">
							<image src="../../static/camera.png" mode="widthFix"></image>
						</view>
						<view class="tc_str">
							商家配送
						</view>
					</view>
					<view class="tc_item">
						<view class="tc_img">
							<image src="../../static/store.png" mode="widthFix"></image>
						</view>
						<view class="tc_str">
							支持配送
						</view>
					</view>
					<view class="tc_item">
						<view class="tc_img">
							<image src="../../static/home.png" mode="widthFix"></image>
						</view>
						<view class="tc_str">
							到店自取
						</view>
					</view>
					<view class="tc_item">
						<view class="tc_img">
							<image src="../../static/Be.png" mode="widthFix"></image>
						</view>
						<view class="tc_str">
							贵宾服务
						</view>	
					</view>
				</view>
			
				<view class="title">
					人均价格
				</view>
				
				<view class="slide">
					
					
					<u-slider v-model="value">
						<!-- 这里外面需要多一层view，否则".badge-button"样式可能不生效 -->
						<view class="">
							<view class="badge-button">
								{{value}}
							</view>
						</view>
					</u-slider>
					
					
					
				</view>
				
				<view class="button">
					<view class="close" @click="saiClick(1)">
						清空
					</view>
					<view class="ss" @click="ssClick(1)">
						搜索
					</view>
				</view>
			</view>
		</view>
		<view class="good_all_box">
			<view class="good_item_box" v-for="(item,index) in storeList" :key='index' @click="rGoog(index)">
				<view class="googImgBox">
					<image :src="item.storeLogo" mode="" style="width: 100%;height: 100%;"></image>
				</view>
				<view class="good_item_describe">
					<view class="good_name_mueu">
						<view class="good_name">
							{{item.storeName}}
						</view>
						<view class="good_mueu">
							...
						</view>
					</view>
					
					<view class="good_describe">
						<view class="good_score">
							<uni-icons type="star-filled" color='#F8621E' size='20'></uni-icons>4.8
						</view>
						<view class="good_sale">
							月售{{item.saleNum||0}}
						</view>
					</view>
							
					<view class="good_performance" v-if="item.startingPrice">
						<view class="good_start_price">
							起送{{item.startingPrice}}
						</view>
						<view class="good_distance_time" v-if="item.deliveryTime && item.deliveryRange">
							{{item.deliveryTime}}分钟 {{item.deliveryRange}}km
						</view>
					</view>
					
					<view class="good_describe_label_box">
						
						<view class="good_item_describe_label" v-for="(foods,i) in item.appraiseManagerList" :key='i'>
							{{foods}}
						</view>
					</view>
					<view class="good_taste_label_box">
						<view style="flex: 1;overflow-x: auto;display: flex;margin-right: 10rpx;">
							<view class="good_item_taste_label_box" v-for="(food,i) in item.foodSortList" :key='i'>
								{{food}}
							</view>
						</view>
						<view class="good_reserve">
							支持预订
						</view>
							
					</view>
					
					
					
				</view>
			</view>
				
			
			
			
			
		</view>
			
	</view>
</template>

<script>
	import Api from '@/common/http.js'
	export default {
		data() {
			return {
				xian:false,
				navigation:null,
				storeList:[],
				sorte: [
					{
						name: '综合排序',
					},
					{
						name:'销量最高',
					},
					{
						name:'距离最近'
					},
					{
						name:'好评优先'
					},
					{
						name:'起送价最低'
					},
					{
						name:'配送最快'
					},
					{
						name:'人均从低到高'
					},
					{
						name:'人均从高到低'
					}
				],
				
				storeIndex: 0,
				sai:false,
				value: 30,
			}
		},
		onLoad() {
			this.getStore()
		},
		components: {
		},
		created() {
			this.navigation = this.$store.getters.getNavigation
		},
		methods: {
			ssClick(type){
				this.sai = false
			},
			saiClick(type){
				this.sai = false
			},
			Tsearch(){
				uni.navigateTo({
					url:"../../pagesA/search/search"
				})
			},
			rGoog(index){
				uni.navigateTo({
					url:'../../pagesA/goodDetails/goodDetails?id='+this.storeList[index].id
				})
			},
			getStore(){
				Api.getStoreList({}).then(res => {
					// console.log('res',res);
					this.storeList = res.data
					this.storeList.map((item) => {
						item.foodSortList = []
						var foodLabelObj = JSON.parse(item.foodLabel)
						
						foodLabelObj.system.map((items)=>{
							item.foodSortList.push(items.name)
						})
						
						foodLabelObj.custom.map((items)=>{
							item.foodSortList.push(items)
						})
						
						item.appraiseManagerList = []
						var appraiseManagerObj = JSON.parse(item.appraiseManager)
						appraiseManagerObj.system.map((items)=>{
							item.appraiseManagerList.push(items.name)
						})
						
						appraiseManagerObj.custom.map((items)=>{
							item.appraiseManagerList.push(items)
						})
						
						
					})
					// this.storeList.foodSortList = this.storeList
					// console.log('this.storeList.foodSortList',this.storeList)
				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
		}
	}
</script>

<style lang="scss" scoped>
	
	image{
		width: 100%;
	}
	
	.search{
		height: 100%;
		// background-color: #0D92FF;
		padding: 0rpx 25rpx;
	}
	
	.search_box{
		height: 100%;
		display: flex;
		align-items: center;
		width: 100%;
		color: #ccc;
		background-color: #f0f0f0;
		border-radius: 38rpx;
	}
	
	.search_icon{
		width: 40rpx;
		height: 40rpx;
		padding: 20rpx;
		// background: #ccc;
	}
	
	
	.screen_box{
		position: relative;
		margin: 0 10rpx;
		display: flex;
		justify-content: space-between;
	}
	
	.screen_left{
		display: flex;
		align-items: center;
	}
	
	.select_item{
		margin: 0 10rpx;
		font-size: 24rpx;
		color: #999;
	}
	
	
	.good_item_box{
		display: flex;
		margin: 20rpx;
	}
	
	.googImgBox{
		width: 170rpx;
		height: 170rpx;
		border-radius: 10rpx;
		background: #007AFF;
		margin-right: 20rpx;
		overflow: hidden;
	}
	.good_item_describe{
		flex: 1;
		overflow: hidden;
	}
	
	.good_name_mueu{
		display: flex;
		justify-content: space-between;
	}
	
	.good_name{
		font-size: 30rpx;
		color: #000;
	}
	.good_describe{
		display: flex;
		margin: 5rpx 0;
		align-items: center;
	}
	
	.good_score{
		margin-right: 20rpx;
		font-size: 28rpx;
		color: #F8621E;
		display: flex;
		align-items: center;
	}
	
	.good_sale{
		font-size: 28rpx;
		color: #999;
	}
	
	.good_start_price,.good_distance_time,.good_describe_label_box,.good_taste_label_box{
		font-size: 24rpx;
		color: #999;
		margin-top: 10rpx;
	}
	
	.good_performance{
		width: 100%;
		display: flex;
		justify-content: space-between;
	}
	
	.good_describe_label_box{
		display: flex;
		overflow-x: auto;
	}
	
	.good_item_describe_label{
		color: #E19D58;
		background: #FEF4E9;
		margin-right: 16rpx;
		padding: 2rpx 20rpx;
		border-radius: 1rpx;
		white-space: nowrap;
	}
	
	.good_item_taste_label_box{
		color: #EEACAA;
		border: 1rpx solid #FCFCFC;
		margin-right: 16rpx;
		padding: 2rpx 20rpx;
		border-radius: 1rpx;
		white-space: nowrap;
	}
	
	.good_taste_label_box{
		display: flex;
	}
	
	.good_reserve{
		padding: 10rpx;
		border: 1rpx solid #999;
	}
	
	
	.drop_down_box{
		position: absolute;
		z-index: 101;
		top: 100%;
		/* left: -130rpx; */
		background: #fff;
		width: 100vw;
	}
	
	.choice{
		display: flex;
		justify-content: space-between;
		font-size: 30rpx;
		padding: 30rpx;
		color: #999;
		/* border-bottom: 1rpx solid #f0f0f0; */
	}
	
	.choiceselect{
		color: #007AFF;
		font-weight: bold;
	}
	
	
	.temp_deop{
		.title{
			margin: 20rpx;
			font-size: 24rpx;
			color: #999;
		}
		
		.tc_box{
			margin: 20rpx;
			display: flex;
			flex-wrap: wrap;
			.tc_item{
				margin:0 20rpx 20rpx 0;
				display: flex;
				padding: 20rpx;
				background: #F8F8F8;
				
				.tc_img{
					width: 40rpx;
				}
				
				.tc_str{
					font-size: 30rpx;
				}
			}
		}
	
		.slide{
			width: 90%;
			margin: 50rpx 20rpx;
		}
		
		.button{
			display: flex;
			view{
				flex: 1;
				text-align: center;
				padding: 20rpx;
			}
			
			.close{
				background: #fff;
				color: #333;
			}
			
			.ss{
				background: #2697F4;
				color: #fff;
			}
		}
	}
	
	.wrap {
		padding: 30rpx;
	}
	
	.badge-button {
		padding: 4rpx 6rpx;
		background-color: #007AFF;
		color: #fff;
		border-radius: 10rpx;
		font-size: 22rpx;
		line-height: 1;
	}
	
</style>
