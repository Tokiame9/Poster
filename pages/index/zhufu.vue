<template>
	<view class="canvas-content" @longtap="saveImg()" @touchstart='start' @touchmove="move" @touchend="moveEnd">
		<movable-area class="moveAre" scale-area="true" >
			<view class="canvas-border">
				<canvas canvas-id="firstCanvas" @longpress="saveImg()">
					
				</canvas>
			</view>
		
		<!-- <movable-view damping="10000" animation="false"  @change="drawUpdata" class="moveImg max" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" direction="all">
			
		</movable-view> -->
		
		<movable-view animation="false" calss="enlarge" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" scale="true" @scale="scaler">
			
		</movable-view>
		<p style="text-align: center;">长按保存图片</p>
		</movable-area>
		
		<view class="foot-Img" @tap="">
			<view v-for="item in imgBox" class="imgBox">
				<img :src="item.preImg" alt="" @tap="switchImg(item.srcImg)">
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imgBox:[
					{
						preImg:'/static/pre_border/1.png',
						srcImg:'/static/1.png'
					},
					{
						preImg:'/static/pre_border/2.png',
						srcImg:'/static/2.png'
					},
					{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					}
				],
				saveIf:true,//存储触发时避免触发其他按压事件
				posStartX:0,//按下时记录的起始的位置X 下同
				posStartY:0,
				offSetX:0,//记录X坐标与起始位置的差值 下同
				offsetY:0,
				posX:0,//画布中上传图像的位置X 下同
				posY:0,
				startPosX:0,//画布中按压时上传图像的位置
				startPosY:0,
				setData:'',//存储图像信息
				imgSrc1:'',//上传图像路径
				scaleSize:1,//图片放大倍率默认为1
				// picWidth:100,
				// picHeigth:100,
				// filePath:'',
				imgSrc2:'/static/1.png',
			}
		},
		onLoad:function(option){
			const query=uni.createSelectorQuery().in(this);
			query.select('')
			this.imgSrc1=option.imgSrc
			this.drawImg();
		},
		methods: {
			switchImg(src){
				this.saveIf=false;
				this.imgSrc2=src;
				this.drawImg();
				this.saveIf=true;
			},
			//缩放计算倍率
			scaler(e){ 
				let evt=e;
				this.scaleSize=evt.detail['scale']
				this.drawImg();
			},
			//按下时纪录位置
			start(e){
				let touch = e.touches[0]
				this.posStartX=touch["pageX"];
				this.posStartY=touch["pageY"];
				this.startPosX=this.posX;
				this.startPosY=this.posY;
			},
			//移动时记录相对差值
			move(e){
				if(this.saveIf){
				let touch= e.touches[0]
				this.offSetX=(touch["pageX"]-this.posStartX)/1.3;
				this.offSetY=(touch["pageY"]-this.posStartY)/1.3;
				this.posX=this.startPosX+this.offSetX;
				this.posY=this.startPosY+this.offSetY;
				this.drawImg()
				}
			},
			//按下抬起时触发记录位移位置
			moveEnd(e){
				this.posX=this.startPosX+this.offSetX;
				this.posY=this.startPosY+this.offSetY;
			},
			//更新画布
			drawUpdata(){
				this.drawImg()
			},
			//画布
			drawImg(){
				let _this=this
				var canvas= uni.createCanvasContext("firstCanvas")
				uni.getImageInfo({
					src:_this.imgSrc2,
					success:function(res){
						let srcH2=res.height
						let srcW2=res.width   //获得图二宽高
							uni.getImageInfo({
								src:_this.imgSrc1,
								success:function(res){
								    let	 srcW1=res.width
									let  srcH1=res.height //获得图一宽高
										uni.getSystemInfo({
											success:function(res){

												let deviceW=319
												let deviceH=567 //获取设备宽高
												let srcW_p1= srcW1
												let srcH_p1= srcH1
												if(srcW1>deviceW){
													srcW_p1=deviceW

													srcH_p1=srcH_p1/(srcW1/deviceW)
												}
												// _this.picWidth=srcH_p1;
												// _this.picHeigth=srcW_p1;
												// this.view_class={
												// 	width:this.picWidth+'px',
												// 	height:this.picHeigth+'px'
												// }

													canvas.drawImage(_this.imgSrc1,0,0,srcW1,srcH1,_this.posX,_this.posY,srcW_p1*_this.scaleSize,srcH_p1*_this.scaleSize)
													canvas.drawImage(_this.imgSrc2,0,0,srcW2,srcH2,0,0,deviceW,deviceH)
													canvas.draw()
													
													
												
											}
										})
								}
							})
					}
					
				})
				
				
				
			},
			//小程序保存canvas图像
			saveImg(){
				if(this.saveIf==false){
					return
				}
				this.saveIf=false;
				let that=this
				uni.showLoading({
					title:"正在保存",
					mask:true,
				})
				uni.canvasToTempFilePath({ //保存到临时路径
					canvasId:'firstCanvas',
					success:(res)=>{
						uni.hideLoading();
						uni.saveImageToPhotosAlbum({//保存到相册
							filePath:res.tempFilePath,
							success:function(res){
								console.log(res);
								uni.showToast({
									title:'保存成功',
									success:function(){
										that.saveIf=true;
									}
								})
								that.setData={
									modalname:null
								}
							},
							fail:function(res){
								console.log(res)
								if(res.errMsg == 'saveImageToPhotosAlbum:fail auth deny'){
									uni.showModal({
										title:'请打开相册授权',
										success(res){
											if(res.confirm){
												uni.openSetting({
													success:function(data){
														that.saveIf=true;
													},
													fail:function(data){
														console.log("openSetting fail",data);
														that.saveIf=true;
													}
												});
											}else if(res.cancel){
												console.log("用户取消")
												that.saveIf=true;
											}
										}
									})
								}
							}
						})
					},
					fail:function(res){
						console.log(res)
						that.saveIf=true;
					}
				})
			}
		}
	}
</script>

<style>
	.canvas-border{
		margin: 0 auto;
		height: 567px;
		width: 319px;
	}
	.enlarge{
		width: 100%;
		height: 100%;
	}
	.backImg{
		opacity: 0;
		width: 100%;
		height: 100%;
		position: absolute;
		left: 0;
		right: 0;
	}
	.foot-Img{
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		align-items: center;
		background-color: #feebed;
		border-top: 3px solid #ffe0cc;
		position: fixed;
		bottom: 0;
		height: 120px;
		width: 100%;
	}
	.moveImg{
		z-index: 1;
		
	}
	.moveAre{
		overflow: hidden;
		width: 100%;
		height: 100%;
	}
	.fontImg{
		top: 0;
		left: 0;
		position: absolute;
		height: 100%;
		width: 100%;
	}
	page{
		width: 100%;
		height: 100%;
	}
	.canvas-content{
		background-color: #feebed;
		width: 100%;
		height: 100%;
	}
	.imgBox{
		width: 70px;
		height: 70px;
	}
	
canvas{
	width:100%;
	height:100%
}
</style>
