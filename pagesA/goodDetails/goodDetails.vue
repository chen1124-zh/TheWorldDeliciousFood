<template>
	<view>
		<view
			:style="{'width':'100%','height':navigation.height+'px','paddingTop': navigation.top + 'px','paddingBottom':'10rpx','position': 'fixed','top':'0','background': 'rgba(0,0,0,.3)','z-index':'101'}">
			<view :style="{'height':navigation.height+'px','width':navigation.left+'px'}" class="topNavigation">
				<view class="search"
					style="height: 100%;display: flex;align-items: center;justify-content: space-between;padding: 0 20rpx;">
					<view class="" @click="fang"
						style="width: 50rpx;height: 50rpx;border-radius: 50%;background: rgba(0,0,0,.5);display: flex;justify-content: center;align-items: center;">
						<uni-icons type="arrowleft" color="#fff"></uni-icons>
					</view>
					<view class="">
						<uni-icons type="star" size="28" class="#000"></uni-icons>

					</view>
				</view>
			</view>
		</view>

		<view class="">
			<view class="" style="height: 380rpx;overflow: hidden;">
				<image :src="store.storeIntroductImg" mode="" style="width: 100%;"></image>
			</view>
		</view>


		<view class="box">
			<view class="shop_information">
				<view class="shop_img">
					<image :src="store.storeLogo" mode="" style="width: 100%;height: 100%;"></image>
				</view>

				<view class="shop_name">
					{{store.storeName}}
				</view>
			</view>

			<view class="evaluate">
				<view class="">
					评价4.8
				</view>

				<view class="">
					月售{{store.addOrderNum||0}}
				</view>

				<view class="">
					配送约{{store.deliveryTime}}分钟
				</view>
				<view class="">
					￥{{store.startingPrice}}/人
				</view>
			</view>

			<view class="businessTime">
				营业时间：{{store.BusinessTimes}}
			</view>

			<view class="relation">
				<view class="address" @click="locations()">
					{{store.area}}{{store.address}}>
				</view>
				<view class="contact" @click.stop="gitPhone">
					<view class="">
						<uni-icons type="phone-filled" color="#289EFF" size="30"></uni-icons>
					</view>
					<view class="" style="color: #289EFF;font-size: 28rpx;">
						联系商家
					</view>

				</view>
			</view>
			<view class="" style="display: flex;">
				<view class="optimization" v-for="(item,index) in store.servuceConfigurationList" :key="index">
					{{item}}
				</view>
			</view>

			<view class="good_taste_label_box">
				<view style="flex: 1;overflow-x: auto;display: flex;margin-right: 10rpx;">
					<view class="good_item_taste_label_box" v-for="(food,i) in store.foodSortList" :key='i'>
						{{food}}
					</view>
				</view>

				<view class="good_reserve">
					支持预订
				</view>

			</view>


			<view class="option">
				<view class="" style="display: flex;">
					<view :class="branch==1?'branch_select':''" @click="branch = 1" style="margin: 20rpx;">
						点餐
					</view>
					<view :class="branch==2?'branch_select':''" @click="branch = 2" style="margin: 20rpx;">
						评价
					</view>
					<view :class="branch==3?'branch_select':''" @click="branch = 3" style="margin: 20rpx;">
						商家
					</view>
				</view>


				<view @click="weeks"
					style="margin: 20rpx;color: #0293FF;font-size: 28rpx;display: flex;align-items: center;">
					<image src="../../static/menu.png" mode="widthFix" style="width: 32rpx;margin-right: 10rpx;">
					</image> 一周菜谱
				</view>
			</view>

			<view class="time" v-if="branch == 1">
				<view class="times">
					<picker mode="selector" :range="cs" range-key="name" @change="cschange">
						<view>{{cs[csIndex].name}}
							<uni-icons type="arrowdown" style="margin-left: 15rpx;"></uni-icons>
						</view>
					</picker>
				</view>
				<view class="week">

					<view class="day" :class="week==index?'weekSelect':''" v-for="(item,index) in weekList" :key='index'
						@click="week=index;">
						<view class="">
							{{item.month}}月{{item.date}}号
						</view>
						<view class="">
							周{{weekStr[item.day]}}
						</view>
					</view>

				</view>
			</view>


			<view class="good_box" style="margin-bottom: 100rpx;" v-if="branch == 1">
				<view class="good_left">

					<view class="good_title_select">
						<view :class="index == selectClassifi?'selectMenu':''" style="position: relative;"
							v-for="(item,index) in classifiList" :key="index" @click="classifiSelect(index)">
							<text>{{item.menuName}}</text>
							<view class="cNum"
								v-if="item.num">
								{{item.num}}
							</view>
						</view>
						
						<view class="cImg" v-if="week!=0">
							<image src="../../static/b.png" mode="heightFix"></image>
						</view>
					</view>
				</view>
				<view class="good_right">
					<view class="good_title">
						{{classifiList[selectClassifi].menuName}}
					</view>
					<view class="good_item_box">
						<view class="good_item" v-for="(item,index) in goodList" :key='index'>
							<view class="good_img">
								<image :src="item.img" mode="" style="width: 100%;height: 100%;"></image>
							</view>
							<view class="good_content" style="flex: 1;padding-right: 20rpx;">
								<view class="good_name">
									{{item.productName}}
								</view>
								<view class="good_describe">
									{{item.describe||'暂无介绍'}}
								</view>
								<view class="operation">
									<view class="price_discount">
										<view class="discount" v-if="false">
											9.8
										</view>
										<view class="price">
											<text style="font-size: 34rpx;">￥</text>
											<text
												style="font-size: 36rpx;">{{item.specifications[0].content[0].price}}</text>
											<text
												style="color: #ccc;text-decoration:line-through;margin-left: 10rpx;">￥{{item.linedPrice}}</text>
										</view>
									</view>

									<view class="" v-if="item.num==0">
										<view class="gauge" @click="gauge(index)" v-if="item.s.length > 1">
											选规格
										</view>
										<view class="an" v-else @click="addShe(index)"
											style="background: #007AFF;color: #fff;">
											+
										</view>
									</view>
									<view class="stepper" v-else>
										<view class="an" @click="reduce(index)">
											-
										</view>
										<view class="" style="margin: 0 20rpx;">
											{{item.num}}
										</view>
										<view class="an" @click="add(index)" style="background: #007AFF;color: #fff;">
											+
										</view>
									</view>

								</view>

							</view>
						</view>
					</view>
				</view>
			</view>

			<view class="business_box" v-if="branch == 3">
				<view class="business_describe">
					本店诚信经营，每一份都是现做
				</view>

				<view class="describe_img_box">
					<view class="describe_img" v-for="(item,index) in store.storeIntroductImgList" :key="index">
						<image :src="item" mode="" style="width: 100%;height: 100%;"></image>
					</view>
				</view>

				<view class="about" @click="food">
					食品安全档案
				</view>
				<view class="about">
					配送服务：商家配送
				</view>
				<view class="about">
					配送时间：{{store.BusinessTimes}}
				</view>
				<view class="about" @click="branchStore">
					共{{shopDataList.length}}家分店
				</view>

				<view class="" v-if="false">
					举报商家
				</view>
			</view>

		</view>


		<view class="Submit">
			<view style="display: flex;align-items: center;flex: 1;padding: 0 20rpx;">
				<view style="margin-right: 10rpx;">
					<image src="../../static/bag.png" mode="widthFix" style="width: 100rpx;"></image>
				</view>
				<view class="">
					<view class="" style="color: red;font-size: 32rpx;">
						<text>￥</text> {{allJia||0}}
					</view>
					<view class="" style="color: #898888;font-size: 24rpx;">
						已优惠￥0
					</view>
				</view>
			</view>
			<view class="op" @click="opa()">
				{{week!=0?"发起拼团":"提交订单"}} 
			</view>
		</view>


		<view class="Merit" v-if="g == true || y == true">
			<view class="Specifications" v-if="g==true">
				<view class="Specifications_box">
					<view class="food_name">
						菜品名称
					</view>


					<view style="margin: 20rpx 0;">
						<view v-for="(item,index) in gaugeData.c" :key='index'>
							<view class="Stitle">
								{{item.title}}
							</view>
							<view :class="items.select == true?	'gaugeSelectItem':'gaugeItem'"
								v-for="(items,indexs) in item.content" :key='indexs' @click="Xgauge(index,indexs)">
								{{items.name}}
							</view>
							<!--  -->

						</view>
					</view>

					<view class="" style="display: flex;justify-content: space-between;">
						<view class="">
							总价<text style="color: red;font-size: 48rpx;">￥</text> <text
								style="color: red;font-size: 50rpx;">{{jia}}</text>
						</view>
						<view class="" @click="addCat"
							style="padding: 10rpx 20rpx;background: #21A0FF;color: #fff;border-radius: 10rpx;">
							加入购物车
						</view>
					</view>
				</view>
				<view class="x" @click="x">
					x
				</view>
			</view>

			<view class="Aweek" v-if="y == true">
				<view class="" style="display: flex;justify-content: space-between;">
					<view class="">
						午餐
					</view>
					<view class="">
						一周
					</view>
					<view class="">
						关闭
					</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	import uniIcons from "@/components/uni-icons/uni-icons.vue"
	import Api from '@/common/http.js'
	export default {
		data() {
			return {
				cs: [{
						name: '早茶',
						value: 'zc'
					},
					{
						name: '早餐',
						value: 'zcn'
					},
					{
						name: '午餐',
						value: 'wc'
					},
					{
						name: '下午茶',
						value: 'xwc'
					},
					{
						name: '晚餐',
						value: 'wcn'
					},
					{
						name: '夜宵',
						value: 'yx'
					}
				],
				csIndex: 0,
				navigation: '',
				storeId: '',
				store: '',
				classifiList: [],
				selectClassifi: 0,
				branch: 1,
				week: 0,
				goodList: [],
				g: false,
				y: false,
				jia: 0,
				tempJia: 0,
				gaugeData: {},
				cat: [],
				user: '',
				allJia: 0,
				weekList: [],
				weekStr: {
					0: '日',
					1: '一',
					2: '二',
					3: '三',
					4: '四',
					5: '五',
					6: '六'
				},
				shopDataList:[]
			}
		},
		components: {
			uniIcons
		},
		onLoad(op) {
			this.storeId = op.id
			this.getStroeData()
			this.user = uni.getStorageSync('user')

			var date = new Date()
			var maxMonth = this.getDayNumByYearMonth(date.getYear(), date.getMonth())
			var currentMonth = date.getMonth()
			var currentDate = date.getDate()
			var currentDay = date.getDay()

			var tempweekList = []
			for (var i = 0; i < 7; i++) {
				tempweekList.push({
					month: currentMonth + 1,
					date: currentDate++,
					day: currentDay++,
				})

				if (currentDate > maxMonth) {
					currentMonth += 1
					currentDate = 1
				}

				if (currentDay == 7) {
					currentDay = 0
				}

			}

			this.weekList = tempweekList



		},
		created() {
			this.navigation = this.$store.getters.getNavigation
		},
		methods: {
			gitPhone() {
				uni.showActionSheet({
					itemList: ['1899999990'],
					itemColor: '#1890FF',
					success: function(res) {


						uni.makePhoneCall({
							phoneNumber: '1899999990' //仅为示例
						});

						console.log(res);
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			},
			locations() {
				var latitude = Number(this.store.latitude)
				var longitude = Number(this.store.longitude)
				uni.getLocation({
					success(res) {
						uni.openLocation({
							latitude: latitude,
							longitude: longitude,
							scale: 15,
						})
					}
				})

			},
			
			getshopList(){
				var than = this
				var data = {
					userId:this.store.userId,
				} 
				Api.getStoreList(data).then(res => {
					console.log('shopList',res)
					than.shopDataList = res.data
				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
			/**
			 * 判断某年是否闰年
			 */
			isRuinian(year) {
				if ((year % 4 == 0 && year % 100 != 0) || (year % 400 == 0)) {
					return 29;
				}
				return 28;
			},
			/**
			 * 根据年和月获取该月有几天
			 */
			getDayNumByYearMonth(year, month) {
				switch (month) {
					case 1:
					case 3:
					case 5:
					case 7:
					case 8:
					case 10:
					case 12:
						return 31;
						break;
					case 4:
					case 6:
					case 9:
					case 11:
						return 30;
						break;
					case 2:
						return this.isRuinian(year);
				}
			},

			classifiSelect(index) {
				this.selectClassifi = index
				// this.classifiList
				this.getGoodData()
			},
			opa() {
				uni.navigateTo({
					url: '../timely/timely?sid=' + this.storeId +'&type='+this.week
				})
			},
			reduce(index) {
				this.goodList[index].num--
				this.$forceUpdate()
				this.allJia = 0



				this.cat.shoppingCarts.map((item) => {

					if (this.goodList[index].id == item.productId) {
						item.num--
						var data = item

						Api.updateShoppingCar(data).then(res => {
							// this.classifiList[this.selectClassifi].num --
							// this.$forceUpdate()

						}).catch(err => {
							uni.showToast({
								title: err.msg,
								icon: 'none'
							})
						});

					}
					this.allJia += item.total * item.num
				})

				// if (this.goodList[index].num == 0) {

				// 	var tmepArr = this.cat.shoppingCarts.filter((item) => {
				// 		if (this.goodList[index].id == item.productId) {
				// 			var data = {
				// 				id: item.id
				// 			}
				// 			Api.deleteShoppingCar(data).then(res => {
				// 				this.classifiList[this.selectClassifi].num--
				// 				this.$forceUpdate()
				// 			}).catch(err => {
				// 				uni.showToast({
				// 					title: err.msg,
				// 					icon: 'none'
				// 				})
				// 			});
				// 		} else {
				// 			return item
				// 		}
				// 	})
				// 	this.cat.shoppingCarts = tmepArr
				// 	console.log(tmepArr)
				// }


			},
			add(index) {
				this.goodList[index].num++
				this.$forceUpdate()
				this.allJia = 0
				this.cat.shoppingCarts.map((item) => {
					if (this.goodList[index].id == item.productId) {
						item.num++
						var data = item

						Api.updateShoppingCar(data).then(res => {
							// this.classifiList[this.selectClassifi].num --
							// this.$forceUpdate()

						}).catch(err => {
							uni.showToast({
								title: err.msg,
								icon: 'none'
							})
						});

					}
					this.allJia += item.total * item.num

				})
			},
			getShoppingCartList() {
				var data = {
					userId: this.user.id,
					storeId: this.storeId
				}
				this.allJia = 0
				Api.getShoppingCart(data).then(res => {

					this.cat = res.data.data[0]
					if (this.cat != undefined) {
						this.cat.shoppingCarts.map((item, index) => {


							this.classifiList.map((items, indexs) => {
								if (items.id == item.meunId) {
									items.num++
								}
							})
							this.allJia += item.total * item.num
						})
					}


					this.getGoodData()
					// console.log('this.classifiList', this.classifiList)
				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
			food() {
				uni.navigateTo({
					url: '../security/security'
				})
			},
			fang() {
				uni.navigateBack({
					delta: -1
				})
			},
			cschange(e) {
				this.csIndex = e.detail.value;
				this.getGoodData()
			},
			Xgauge(index, indexs) {
				this.jia = this.tempJia
				this.gaugeData.c[index].content.map((item, i) => {

					if (indexs == i) {
						item.select = true
					} else {
						item.select = false
					}

				})

				this.gaugeData.c.map((item) => {
					item.content.map((items) => {
						if (items.select == true) {
							this.jia += parseFloat(items.price)
						}
					})


				})



			},
			addCat() {


				// private String userId;     //用户id
				// private String storeId;    //商店id
				// private Integer num;       //数量
				// private BigDecimal price;  //价格
				// private BigDecimal total; //总价
				// private String urlImages;   //图片
				// private String productName;  //商品名称
				// 		   private  String spec;   //规格
				// private String remark;       //备注
				var tempGauge = []


				var urlImages = ''
				var productName = ''
				var price = ''

				for (var i = 0; i < this.gaugeData.c.length; i++) {

					for (var j = 0; j < this.gaugeData.c[i].content.length; j++) {

						if (this.gaugeData.c[i].content[j].select == true) {

							tempGauge.push(this.gaugeData.c[i].content[j])
						}
					}

				}

				for (var i = 0; i < this.goodList.length; i++) {
					if (this.gaugeData.i == this.goodList[i].id) {
						price = this.goodList[i].linedPrice;
						urlImages = this.goodList[i].productImg;
						productName = this.goodList[i].productName
					}
				}

				var data = {
					userId: this.user.id,
					storeId: this.storeId,
					num: 1,
					price: price,
					urlImages: urlImages || '',
					productName: productName,
					total: this.jia,
					spec: JSON.stringify(tempGauge),
					productId: this.gaugeData.i,
					meunId: this.classifiList[this.selectClassifi].id,
					status: 0,
					favourable: '',
					remark: '',
					discounted: 0
				}
				Api.addShoppingCart(data).then(res => {
					this.classifiList[this.selectClassifi].num++
					this.goodList.map((item, index) => {
						if (item.id == this.gaugeData.i) {
							item.num++
						}

					})

					console.log('res.data.data', res.data.data)

					if (this.cat == undefined) {
						this.cat = {
							shoppingCarts: [],
							storeId: this.store.id,
							storeName: this.store.storeName,
							userId: this.user.id,
						}
					}
					this.cat.shoppingCarts.push(res.data.data)
					// this.allJia = 0
					console.log(this.cat)
					this.cat.shoppingCarts.map((item, index) => {
						this.allJia += item.total
					})
					this.g = false

				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});



			},
			addShe(index) {
				var tempGauge = []


				var urlImages = ''
				var productName = ''
				var price = ''
				for (var i = 0; i < this.goodList[index].specifications.length; i++) {

					for (var j = 0; j < this.goodList[index].specifications[i].content.length; j++) {
						this.goodList[index].specifications[i].content[j].select = true
						tempGauge.push(this.goodList[index].specifications[i].content[j])

						this.jia += Number(this.goodList[index].specifications[i].content[j].price)
					}

				}

				price = this.goodList[index].linedPrice;
				urlImages = this.goodList[index].productImg;
				productName = this.goodList[index].productName


				var data = {
					userId: this.user.id,
					storeId: this.storeId,
					num: 1,
					price: price,
					urlImages: urlImages || '',
					productName: productName,
					total: this.jia,
					spec: JSON.stringify(tempGauge),
					productId: this.goodList[index].id,
					meunId: this.classifiList[this.selectClassifi].id,
					status: 0,
					favourable: '',
					remark: '',
					discounted: 0
				}
				Api.addShoppingCart(data).then(res => {
					this.classifiList[this.selectClassifi].num++
					// 	this.goodList.map((item, index) => {
					// 		if (item.id == this.gaugeData.i) {
					// 			item.num++
					// 		}

					// 	})



					if (this.cat == undefined) {
						this.cat = {
							shoppingCarts: [],
							storeId: this.store.id,
							storeName: this.store.storeName,
							userId: this.user.id,
						}
					}

					this.goodList[index].num++
					this.cat.shoppingCarts.push(res.data.data)
					this.allJia = 0
					this.cat.shoppingCarts.map((item, index) => {
						this.allJia += item.total
					})
					// this.g = false

				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
			getStroeData() {

				Api.getStoreList({
					id: this.storeId
				}).then(res => {
					// console.log('res',res);
					this.store = res.data[0]
					
					
					
					
					this.getshopList()
					this.store.BusinessTimes = ''
					var tempBusinessTime = JSON.parse(this.store.businessTime)
					if (tempBusinessTime.standard == true) {
						this.store.BusinessTimes = tempBusinessTime.bz.stime + '-' + tempBusinessTime.bz.etime
					} else {

						tempBusinessTime.timeList.map((item) => {
							this.store.BusinessTimes += item.sTime + '-' + item.dTime + ","
						})
					}

					this.store.BusinessTimes = this.store.BusinessTimes.substring(0, this.store.BusinessTimes
						.length - 1)

					// this.storeList.map((item)=>{
					this.store.storeIntroductImgList = this.store.storeIntroductImg.split(",")
					// console.log('this.store',JSON.parse(this.store.servuceConfiguration) )

					this.store.foodSortList = []
					var foodLabelObj = JSON.parse(this.store.foodLabel)

					foodLabelObj.system.map((items) => {
						this.store.foodSortList.push(items.name)
					})

					foodLabelObj.custom.map((items) => {
						this.store.foodSortList.push(items)
					})


					this.store.servuceConfigurationList = []
					var servuceConfigurationListObj = JSON.parse(this.store.servuceConfiguration)

					servuceConfigurationListObj.system.map((items) => {
						this.store.servuceConfigurationList.push(items.name)
					})

					servuceConfigurationListObj.custom.map((items) => {
						this.store.servuceConfigurationList.push(items)
					})



				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});

				var data = {
					storeId: this.storeId
				}
				Api.getMenuTypeList(data).then(res => {
					this.classifiList = res.data.data

					this.classifiList.map((item, index) => {
						item.num = 0
					})
					this.getShoppingCartList()

					
				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
			gauge(index) {
				this.g = true;
				if (this.goodList[index].specification == '') {

				} else {
					this.tempJia = this.goodList[index].productPrice
					this.jia = this.goodList[index].productPrice
					this.gaugeData.i = this.goodList[index].id
					this.gaugeData.c = JSON.parse(this.goodList[index].specification)
				}

			},
			x() {
				this.g = false
			},
			branchStore(){
				uni.navigateTo({
					url:'../BranchStore/BranchStore?id='+this.store.userId
				})
			},
			weeks() {
				uni.navigateTo({
					url:"../menu"
				})
			},
			getGoodData() {
				var data = {
					storeId: this.storeId,
					menuType: this.classifiList[this.selectClassifi].id,
					week1: this.week == 1 ? 1 : '',
					week2: this.week == 2 ? 1 : '',
					week3: this.week == 3 ? 1 : '',
					week4: this.week == 4 ? 1 : '',
					week5: this.week == 5 ? 1 : '',
					week6: this.week == 6 ? 1 : '',
					week7: this.week == 0 ? 1 : '',
					zc: this.cs[this.csIndex].value == 'zc' ? 1 : '',
					zcn: this.cs[this.csIndex].value == 'zcn' ? 1 : '',
					wc: this.cs[this.csIndex].value == 'wc' ? 1 : '',
					xwc: this.cs[this.csIndex].value == 'xwc' ? 1 : '',
					wcn: this.cs[this.csIndex].value == 'wcn' ? 1 : '',
					xy: this.cs[this.csIndex].value == 'xy' ? 1 : '',
				}


				Api.getProductList(data).then(res => {
					this.goodList = res.data.data
					this.goodList.map((item) => {

						var tempSpecification = JSON.parse(item.specification)
						item.s = []
						tempSpecification.map(items => {
							items.content.map((itemsx) => {
								item.s.push(itemsx)
							})
						})


						item.specifications = JSON.parse(item.specification)

						item.num = 0
						if (item.productImg != undefined) {
							item.img = item.productImg.split(',')[0]
						}
					})


					if (this.cat != undefined) {
						this.cat.shoppingCarts.map((item, index) => {
							this.goodList.map((items, indexs) => {
								if (item.productId == items.id) {
									items.num = item.num
								}
							})

						})
					}

				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
	.shop_information {
		position: relative;
		padding: 20rpx;
	}

	.shop_img {
		position: absolute;
		bottom: 20rpx;
		right: 20rpx;
		width: 130rpx;
		height: 130rpx;
		background: #007AFF;
		overflow: hidden;
	}

	.shop_name {
		font-size: 40rpx;
		font-weight: bold;
	}

	.evaluate {
		display: flex;
		padding: 0 20rpx;
	}

	.evaluate view {
		color: #636363;
		margin-right: 20rpx;
	}

	.box {
		background: #fff;
		border-radius: 30rpx 30rpx 0 0;
		position: relative;
		top: -100rpx;
	}

	.businessTime {
		padding: 0 20rpx;
		font-size: 26rpx;
		background: #EEF7FF;
		padding: 5rpx 40rpx;
		color: #59A0DA;
		border-radius: 20rpx;
		display: inline-block;
		margin: 20rpx 10rpx;
	}

	.relation {
		padding: 0 20rpx;
		display: flex;
		justify-content: space-between;
	}

	.optimization {
		padding: 0 20rpx;
		font-size: 28rpx;
		color: #636363;
	}

	.lableBox {
		display: flex;
		padding: 0 20rpx;
	}

	.option {
		display: flex;
		padding: 0 20rpx;
		justify-content: space-between;
		align-items: center;
	}


	.good_box {
		display: flex;
	}

	.time {
		display: flex;
		margin: 10rpx 20rpx;
		/* padding: 10rpx 20rpx; */
		/* background: #999; */
	}

	.good_title_select {
		
		width: 200rpx;
		background: #F8F8F8;
		height: 100vh;
		view {
			width: auto;
			padding: 0 6rpx;
			text {
				display: block;
				text-align: center;
				padding: 10rpx 0;
				margin: 10rpx 0;
				font-size: 28rpx;
			}
		}
		.selectMenu {
			background: #fff;
			text {
				display: block;
				text-align: center;
				padding: 10rpx 0;
				margin: 10rpx 0;
				border-left: 4rpx solid #007AFF;
				font-size: 28rpx;
			}
		}
		
		.cImg{
			display: flex;
			justify-content: center;
			text-align: center;
			margin: 20rpx;
			height: 500rpx;
			border-radius: 40rpx;
			overflow: hidden;
			image{
				height: 100%;
				text-align: center;
			}
		}
		
	}


	.good_right {
		margin-left: 30rpx;
		flex: 1;
	}

	.good_title {
		margin: 20rpx 0;
		font-size: 30rpx;
	}


	.good_item {
		margin: 20rpx 0;
		display: flex;
		/* align-items: center; */
	}

	.good_img {
		width: 200rpx;
		height: 200rpx;
		background: #000000;
		margin-right: 10rpx;
		overflow: hidden;
	}

	.good_name {
		font-size: 30rpx;
		font-weight: 800;
	}

	.good_describe {
		font-size: 28rpx;
		color: #D4C3CE;
	}

	.operation {
		display: flex;
		align-items: flex-end;
		justify-content: space-between;
	}

	.Submit {
		width: 100%;
		height: 110rpx;
		position: fixed;
		justify-content: space-between;
		align-items: center;
		bottom: 0;
		display: flex;
		background: #fff;
	}

	.Merit {
		position: fixed;
		top: 0;
		z-index: 101;
		height: 100%;
		width: 100%;
		background: rgba(0, 0, 0, 0.3);
		display: flex;
		align-items: center;
		justify-content: center;
		/* display: none;	 */
	}

	.Specifications {
		width: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.Specifications_box {
		width: 80%;
		background: #fff;
		border-radius: 20rpx;
		padding: 20rpx;
	}

	.food_name {
		font-size: 40rpx;
		color: #999;
		font-weight: bold;
	}


	.Aweek {
		position: absolute;
		bottom: 0;
		background: #fff;
		width: 90%;
		height: 70%;
		padding: 5%;
		border-radius: 30rpx 30rpx 0 0;
	}


	.business_box {
		padding: 20rpx;
	}

	.business_describe {
		font-size: 24rpx;
		color: #ccc;
		margin: 20rpx 0;
	}

	.describe_img_box {
		display: flex;
	}

	.describe_img {
		width: 220rpx;
		height: 220rpx;
		background: #007AFF;
		margin-right: 10rpx;
	}

	.about {
		padding: 30rpx 0;
		border-bottom: 1rpx solid #ccc;
		font-size: 36rpx;
	}

	.branch_select {
		padding-bottom: 10rpx;
		color: #DB7878;
		font-weight: bold;
		border-bottom: 6rpx solid #DB7878;
	}

	.times {
		background: #F0F0F0;
		padding: 20rpx;
		margin-right: 20rpx;
		border-radius: 10rpx;
	}

	.week {
		overflow-x: auto;
		display: flex;
		/* flex-wrap: wrap; */
		flex: 1;
	}

	.week .day {
		padding: 10rpx 40rpx;
		text-align: center;
		border-radius: 15rpx 15rpx 0 15rpx;
		box-shadow: 0rpx 0rpx 5rpx #ccc;
		white-space: nowrap;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.weekSelect {
		color: #fff;
		background: #028EFF;
	}


	.op {
		padding: 0 50rpx;
		background: #289EFF;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #fff;
		font-weight: bold;
		/* line-height: 100%; */
	}

	.gaugeItem {
		padding: 10rpx;
		border-radius: 10rpx;
		border: 1rpx solid #999;
		color: #999;
		text-align: center;
		display: inline-block;
		margin-right: 20rpx;
	}

	.gaugeSelectItem {
		padding: 10rpx;
		border-radius: 10rpx;
		border: 1rpx solid #007AFF;
		color: #007AFF;
		text-align: center;
		display: inline-block;
		margin-right: 20rpx;
	}

	.x {
		margin-top: 20rpx;
		font-size: 34rpx;
		width: 50rpx;
		height: 50rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 50%;
		border: 4rpx solid #000;
	}

	.price {
		color: red;
	}

	.stepper {
		display: flex;
		align-items: center;
	}

	.an {
		width: 30rpx;
		height: 30rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		/* padding: 10rpx; */
		border-radius: 50%;
		border: 4rpx solid #007AFF;
		color: #007AFF;
	}

	.address {
		color: #636363;
		font-size: 28rpx;
	}

	.contact {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.good_taste_label_box {
		display: flex;
		font-size: 24rpx;
		color: #999;
		margin: 10rpx;
	}

	.good_item_taste_label_box {
		color: #EEACAA;
		border: 1rpx solid #FCFCFC;
		margin-right: 16rpx;
		padding: 2rpx 20rpx;
		border-radius: 1rpx;
		white-space: nowrap;
	}

	.good_reserve {
		padding: 5rpx 10rpx;
		border: 1rpx solid #999;
	}

	.Stitle {
		margin: 10rpx 0;
	}


	.gauge {
		background: #1897FF;
		font-size: 26rpx;
		padding: 10rpx;
		border-radius: 10rpx;
		color: #fff;
	}
	
	.cNum{
		position: absolute;
		right: 0;
		top: 0;
		background: red;
		color: #fff;
		border-radius: 5rpx;
		font-size: 24rpx;
	}
</style>
