<template>
	
<view class="mainPage">
	
	<view class="coverLayer" v-if="guideOnShow" @tap="hideImg">
		
	</view>
	<view class="button_back" @tap="back" v-if="pageTwo">
		<p v-if="pageTwo">返回</p>
	</view>
	<view class="button_save" @tap="saveImg()" v-if="pageTwo">
		<p v-if="pageTwo">完成</p>
	</view>
	<view class="imgBox" >
		<img v-if="imgOnShow" class="imgResult" :src="imgPath" alt="" @longpress="savePngImg" @tap.stop="hideImg">
		<view class="pageOne" v-if="pageOne" @tap="upLoadImg()">
			<img class="imgGui" src="/static/uploadGui.png" alt="" v-if="guideOnShow" @tap.stop="hideImg">
			
			<view class="shu" v-if="!imgOnShow">
			</view>
			<view class="heng" v-if="!imgOnShow">
				
			</view>
			<view class="word" @click="test" v-if="pageOne">
			</view>
		</view>
		
		<movable-area class="moveAre" :scale-area="pageTwo" v-if="pageTwo">
			<img class="imgGui" v-if="guideOnShow" src="/static/canvasGui.png" alt="" @tap.stop="hideImg">
			<view v-if="pageTwo" class="canvas-border"@longtap="saveImg()" @touchstart='start' @touchmove="move" @touchend="moveEnd" >
				<canvas canvas-id="firstCanvas" @longpress="saveImg()" v-if="pageTwo">
				</canvas>
			</view>
		
		<!-- <movable-view damping="10000" animation="false"  @change="drawUpdata" class="moveImg max" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" direction="all">
			
		</movable-view> -->
		
		<movable-view v-if="pageTwo" animation="false" calss="enlarge" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" scale="true" @scale="scaler">
			
		</movable-view>
		</movable-area>
		
	</view>
	<img class="downLoad" src="/static/dowload.png" alt="" v-if="imgOnShow">
	
	<view class="foot-Img" >
		<img width="100%" height="100%" src="/static/footer.png" alt="">
		<p class="word_bottom">上海体育学院</p>
	</view>
	<view class="foot-ImgTwo"  v-if=false>
		<view v-for="item in imgBox" :key="item.preImg" class="imgBox_pre">
			<img :src="item.preImg" alt="" @tap="switchImg(item.srcImg)">
		</view>
	</view>
</view>
</template>
<script>
	export default {
		data() {
			return {
				title: 'Hello',
				imgSrc:'',
				imgBox:[
					{
						id:1,
						preImg:'/static/pre_border/1.png',
						srcImg:'/static/0001.png'
					},
					{
						id:2,
						preImg:'/static/pre_border/2.png',
						srcImg:'/static/2.png'
					},
					{
						id:3,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						id:4,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						id:5,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},{
						id:6,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						id:7,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						id:8,
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						id:9,
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
				picWidth:100,
				picHeigth:100,
				filePath:'',
				imgSrc2:'/static/0001.png',
				canvas:{},
				imgMask:'/static/mask/kouzhao_mask.png',
				imgKouzhao:'/static/kouzhao.png',
				imgBack:'/static/back.png'
			}
		},
		onLoad() {

		},
		methods: {
			noPullDown(selector) {
			    //禁止页面拖动
			    document.querySelector('body').addEventListener('touchmove', function (e) {
			        if(!selector) {
			            e.preventDefault()
			        } else {
			            // 如果需要部分区域可以滑动，则需传入需要滑动地方的类名
			            if (!document.querySelector(selector).contains(e.target)) {
			                e.preventDefault()
			            }
			        }
			    }, { passive: false })
			},
			
			back(){
				this.pageTwo=false;
				this.pageOne=true;
				this.imgOnShow=false;
				this.saveIf=true;
				this.pageThree=true;
				
			},
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
				e.preventDefault();
				e.stopPropagation();
				// document.body.addEventListener('touchmove',function(e){
				// 	e.preventDefault();
				// },{passive:false});
				if(this.saveIf){
				let touch= e.touches[0]
				this.offSetX=(touch["pageX"]-this.posStartX);
				this.offSetY=(touch["pageY"]-this.posStartY);
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
			hideImg(){
				
				this.guideOnShow=false;
				this.drawImg();
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
												let deviceW=_this.boxWidth
												let deviceH=_this.boxHeight //获取设备宽高
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
												canvas.clearRect(0,0,_this.boxWidth,_this.boxHeight)
												// canvas.setFillStyle('#fff')
												// canvas.fillRect(0,0,deviceW,deviceH)
												
												canvas.drawImage(_this.imgSrc1,0,0,srcW1,srcH1,_this.posX,_this.posY,srcW_p1*_this.scaleSize,srcH_p1*_this.scaleSize)
												canvas.globalCompositeOperation="destination-in"
												canvas.drawImage(_this.imgMask,0,0,500,320,deviceW/2-125,deviceH/2-80,250,160)
												canvas.globalCompositeOperation="source-over"
												canvas.drawImage(_this.imgSrc2,0,0,srcW2,srcH2,0,0,deviceW,deviceH)
												canvas.drawImage(_this.imgKouzhao,0,0,500,320,deviceW/2-125,deviceH/2-80,250,160)
												canvas.globalCompositeOperation="destination-atop"
												canvas.drawImage(_this.imgBack,0,0,srcW2,srcH2,0,0,deviceW,deviceH)
												canvas.globalCompositeOperation="source-over"
												canvas.font="bold 30px Arial"
												// canvas.fillStyle("#fff")
												canvas.fillStyle="#fff"
												canvas.fillText("秦若洋",20,deviceH-20)
												canvas.draw()
												_this.canvas=canvas
											}
										})
								}
							})
					}
					
				})
				
				
				
			},
			//小程序保存canvas图像
			savePngImg(){
				let that=this;
				uni.showLoading({
					title:"正在保存",
					mask:true,
				})
				const type = uni.getSystemInfoSync().uniPlatform
				if(type=="app"||type=="web"){
					uni.hideLoading()
				}
				uni.saveImageToPhotosAlbum({//保存到相册
					filePath:that.imgPath,
					success:function(res){
						uni.hideLoading()
						console.log(res);
						uni.showToast({
							title:'保存成功',
							success:function(){
								that.saveIf=true;
							},
							fail:function(){
								that.saveIf=true;
							}
							
						})
						that.setData={
							modalname:null
						}
					},
					fail:function(res){
						console.log(res)
						uni.hideLoading()
						that.saveIf=true;
						if(res.errMsg == 'saveImageToPhotosAlbum:fail auth deny'){
							uni.showModal({
								title:'请打开相册授权',
								success(res){
									that.saveIf=true;
									if(res.confirm){
										uni.openSetting({
											success:function(data){
											},
											fail:function(data){
												console.log("openSetting fail",data);
											}
										});
									}else if(res.cancel){
										console.log("用户取消")
										that.saveIf=true;
									}
								},
								fail:function(){
									that.saveIf=true;
								}
							})
						}
						
					that.saveIf=true;
					}
				})
			},
			saveImg(){
				if(this.saveIf==false){
					return
				}
				this.pageTwo=false
				let that=this
				uni.showLoading({
					title:"正在保存",
					mask:true,
				})
				uni.canvasToTempFilePath({ //保存到临时路径
					canvasId:'firstCanvas',
					success:(res)=>{
						uni.hideLoading();
						const type = uni.getSystemInfoSync().uniPlatform
						console.log(type)
						that.imgPath=res.tempFilePath;
						that.imgOnShow=true;
						that.saveIf=true;
						that.pageThree=true;
						if(type=="app"||type=="web"){
							// var oA = document.createElement("a");
							// oA.download = ''; // 设置下载的文件名，默认是'下载'
							// oA.href = res.tempFilePath;
							// document.body.appendChild(oA);
							// oA.click();
							// oA.remove(); // 下载之后把创建的元素删除
							// that.saveIf=true;
							uni.setStorage({
								key: 'guideUI',
								data: true,
								success: function () {
									console.log('success');
								}
							});
							that.pageTwo=false
							
							return
							}
						uni.saveImageToPhotosAlbum({//保存到相册
							filePath:res.tempFilePath,
							success:function(res){
								console.log(res);
								uni.showToast({
									title:'保存成功',
									success:function(){
										that.saveIf=true;
										uni.setStorage({
											key: 'guideUI',
											data: true,
											success: function () {
												console.log('success');
											}
										});
									},
									fail:function(){
										that.saveIf=true;
									}
									
								})
								that.setData={
									modalname:null
								}
							},
							fail:function(res){
								console.log(res)
								that.saveIf=true;
								if(res.errMsg == 'saveImageToPhotosAlbum:fail auth deny'){
									uni.showModal({
										title:'请打开相册授权',
										success(res){
											that.saveIf=true;
											if(res.confirm){
												uni.openSetting({
													success:function(data){
													},
													fail:function(data){
														console.log("openSetting fail",data);
													}
												});
											}else if(res.cancel){
												console.log("用户取消")
												that.saveIf=true;
											}
										},
										fail:function(){
											that.saveIf=true;
										}
									})
								}
								
							that.saveIf=true;
							}
						})
					},
					fail:function(res){
						console.log(res)
						that.saveIf=true;
						
						that.pageOne=true
					}
				})
			},
			test(){
				uni.createSelectorQuery().in(this).select(".imgBox").boundingClientRect(
					data=>{
						this.boxWidth=data.width;
						this.boxHeight=data.height;
						console.log(this.boxWidth)
						console.log(this.boxHeight)
						this.posY=data.height/2;
					}).exec()
				
			},

			edit(){
				uni.navigateTo({
					url:"edit?imgSrc="+imgSrc,
				})
			},
			upLoadImg(){
				
				var _this=this;
				_this.drawImg()
				uni.chooseImage({
					count:1,
					sizeType:['original'],
					sourceType:['album'],
					success:function(res){
						_this.imgSrc= res.tempFilePaths;
						uni.navigateTo({
							url: 'zhufu?imgSrc='+_this.imgSrc})
						_this.imgSrc1=_this.imgSrc[0];
						_this.pageTwo=true;
						_this.pageOne=false;
						_this.guideOnShow=true;
						_this.pageThree=false;
						_this.drawImg();
						uni.getStorage({
							key: 'guideUI',
							success: function (res) {
								_this.guideOnShow=false;
								_this.drawImg()
								console.log('123')
							},
							fail: function(res){
								_this.drawImg()
							}
							
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
