<template>
	<view style="margin: 30rpx;">
		<view class="field">
			<view class="">
				收货人：
			</view>
			<view class="inp">
				<input type="text" v-model="nickName" />
			</view>
		</view>
		<view class="field">
			<view class="">
				手机号码：
			</view>
			<view class="inp">
				<input type="number" v-model="mobile" maxlength="11"/>
			</view>
		</view>
		<view class="field">
			<view class="">
				所在区域：
			</view>
			<view class="inp">
				<input type="text" v-model="region" />
			</view>
		</view>
		<view class="field">
			<view class="">
				详细地址：
			</view>
			<view class="inp">
				<textarea v-model="address" style="width: 100%;height: 150rpx;padding: 10rpx;"/>
			</view>
		</view>
		
		<view class="delbox" v-if="type" @click="delress()">
			<view class="del">
				删除
			</view>
			
		</view>
		
		<view class="" style="display: flex;justify-content: space-between;margin: 20rpx 0;" >
			<view class="">
				设为默认地址
			</view>
			<view class="choice" @click="select=!select">
				<view class="select" v-if="select">
					
				</view>
			</view>
		</view>
	
		<view class="" style="background: #FF1F6C; margin-top: 30rpx; border-radius: 30rpx;padding: 10rpx;text-align: center;color: #fff;" @click="addRess">
			保存收货地址
		</view>
			
	</view>
</template>

<script>
	import Api from '@/common/http.js'
	export default {
		data() {
			return {
				mobile:'',         //手机号
				nickName:'',     //昵称
				region:'',       //地区
				address:'',      //地址
				userId:'',       //用户id
				id:'',
				select:false,
				type:0
			}
		},
		onLoad(op) {
			this.type = op.type
			this.id = op.id
			if(this.id != ''){
				this.QRess()
			}
		},
		onShow() {
			var user = uni.getStorageSync('user')
			this.userId = user.id
		},
		methods: {
			QRess(){
				
			},
			delress(){
				Api.deleteAddress({id:id}).then(res => {
					uni.showToast({
						title:'删除成功',
						icon:'none'
					})
					setTimeout(()=>{
						uni.navigateBack({
							delta:-1
						})
					},1000)
					
				}).catch(err => {
					uni.showToast({
						title: err.msg,
						icon: 'none'
					})
				});
			},
			addRess(){
				if(this.mobile == '' || this.nickName == '' || this.region == '' || this.address == ''){
					uni.showToast({
						title:'请不要留空',
						icon:"none"
					})
					return
				}
				var z = /^1[3456789]d{9}$/
				
				if(!(RegExp(/^1[34578]\d{9}$/).test(this.mobile))){
					uni.showToast({
						title:'请输入正确的手机号码',
						icon:"none"
					})
					return
				}
				
				var data = {
					mobile:this.mobile,         //手机号
					nickName:this.nickName,     //昵称
					address:this.region+this.address,      //地址
					userId:this.userId,       //用户id
					status:0,
					defaultAddress:this.select?"1":"0"
				}
				
				Api.addAddress(data).then(res => {
					uni.showToast({
						title:'新增成功',
						icon:'none'
					})
					setTimeout(()=>{
						uni.navigateBack({
							delta:-1
						})
					},1000)
					
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
	
	view{
		font-size: 28rpx;
	}
	
	.field{
		display: flex;
		border-bottom: 1rpx solid #F0F0F0;
		padding: 10rpx;
		/* margin: 20rpx; */
		
	}
	.delbox{
		text-align: right;
		margin: 10rpx 0;
	}
	
	.del{
		display: inline-block;
		padding:10rpx 20rpx;
		background: #A2A2A2;
		color: #fff;
		border-radius: 30rpx;
	}
	
	.inp{
		flex: 1;
	}
	
	.choice{
		width: 30rpx;
		height: 30rpx;
		border-radius: 50%;
		border: 1rpx solid #ccc;
		padding: 5rpx;
	}
	
	.select{
		width: 100%;
		height: 100%;
		border-radius: 50%;
		background: #007AFF;
	}
	
	
	
</style>
