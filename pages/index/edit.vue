<template>
	<view class="img-content"  :eventPenetrationEnabled="false">
			<image class="moveImg" :src="imgSrc" alt="" @click="">
	</view>
</template>

<script>
	export default {
		onLoad:function(option){
			var _this=this
			this.imgSrc=option.path;
			console.log(this.imgSrc)
		},
		data() {
			return {
				imgWidth:0,
				imgHeight:0,
				imgSrc:'',
			}
		},
		methods: {
			
		}
	}
</script>

<style>
	.imgBox{
		width: 100%;
	}
	.img-content{
		width: 100%;
		height: 100%;
	}
	.fontImg{
		z-index: 0;
		top: 0;
		left: 0;
		position: absolute;
		width: 100%;
		height: 100%;
	}
	
	page{
		width: 100%;
		height: 100%;
	}
.moveAre{
	overflow: hidden;
	width: 100%;
	height: 100%
}
</style>
