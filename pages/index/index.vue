<template>
<view class="mainPage">
	<view class="imgBox" @tap="upLoadImg()">
		<view class="shu">
		</view>
		<view class="heng">
			
		</view>
	</view>
	<view class="foot-Img" @tap="">
		<img src="/static/footer.png" alt="">
	</view>
</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				imgSrc:'',
			}
		},
		onLoad() {

		},
		methods: {
			edit(){
				uni.navigateTo({
					url:"edit?imgSrc="+imgSrc,
				})
			},
			upLoadImg(){
				var _this=this;
				uni.chooseImage({
					count:1,
					sizeType:['original'],
					sourceType:['album'],
					success:function(res){
						_this.imgSrc= res.tempFilePaths;
						uni.navigateTo({
							url: 'zhufu?imgSrc='+_this.imgSrc
						});
					}
				})
			}
		}
	}
</script>

<style>
	.shu{
		margin: 150px auto;
		width: 0px;
		height: 70px;
		border: 2px solid #ff8826;
	}
	.heng{
		margin: -187px auto;
		width: 70px;
		height: 0px;
		border: 2px solid #ff8826;
	}
	page{
		height: 100%;
	}
	.mainPage{
		height: 100%;
		background-color: #feebed;
	}
	.imgBox{
		position: relative;
		top: 40px;
		background-color: #fff;
		margin: 0 auto;
		border-radius: 6px;
		width: 300px;
		height: 430px;
		border: 5px solid #ff8826;
	}
image{
	width: 100%;
}
.foot-Img{
	background-color: #b01f24;
	position: fixed;
	bottom: 0;
	height: 100px;
	width: 100%;
}
	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
