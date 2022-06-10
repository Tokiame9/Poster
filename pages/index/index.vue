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
	<view class="sign_border"  v-if="pageTwo">
		<p v-if="pageTwo">相框</p>
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
			<view v-if="pageTwo" class="canvas-border"@longtap.stop="saveImg()"  @touchstart.stop='start' @touchmove.stop="move" @touchend="moveEnd" >
				<canvas canvas-id="firstCanvas" @longpress="saveImg()" v-if="pageTwo" disable-scroll="true">
				</canvas>
			</view>
		
		<!-- <movable-view damping="10000" animation="false"  @change="drawUpdata" class="moveImg max" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" direction="all">
			
		</movable-view> -->
		
		<movable-view v-if="pageTwo" animation="false" calss="enlarge" :style="'height:'+picWidth+'px'+';'+'width:'+picHeigth+'px;'" scale="true" @scale="scaler">
			
		</movable-view>
		</movable-area>
		
	</view>
	<img class="downLoad" src="/static/dowload.png" alt="" v-if="imgOnShow">
	
	<view class="foot-Img" v-if="pageThree">
		<img width="100%" height="100%" src="/static/footer.png" alt="">
		<p class="word_bottom">上海体育学院</p>
	</view>
	<view class="foot-ImgTwo"  v-if="pageTwo">
		<view class="scroll_box">
		<view v-for="item in imgBox" :key="item.preImg" class="imgBox_pre">
			<img :src="item.preImg" alt="" @tap="switchImg(item.srcImg)">
		</view>
		</view>
	</view>
</view>
</template>

<script>
	export default {
		data() {
			return {
				guideOnShow:true,
				imgOnShow:false,
				imgPath:'',
				pageOne:true,
				pageTwo:false,
				pageThree:true,
				boxWidth:0,
				boxHeight:0,
				imgSrc:'',
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
					},
					{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
					},
					{
						preImg:'/static/pre_border/3.png',
						srcImg:'/static/3.png'
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
				picWidth:100,
				picHeigth:100,
				filePath:'',
				imgSrc2:'/static/1.png',
				canvas:{}
			}
		},
		onLoad() {
			// document.body.addEventListener('touchmove',function(e){
			// 	e.preventDefault();
			// },{passive:false});
			this.noPullDown(".foot-ImgTwo")
			this.test()//页面加载时获取canvas容器宽高
			let _this=this;
			uni.getStorage({
				key: 'guideUI',
				success: function (res) {
					_this.guideOnShow=false;
					console.log('123')
				}
			});
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
			hideImg(){
				this.guideOnShow=false;
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
												canvas.setFillStyle('#fff')
												canvas.fillRect(0,0,deviceW,deviceH)
												canvas.drawImage(_this.imgSrc1,0,0,srcW1,srcH1,_this.posX,_this.posY,srcW_p1*_this.scaleSize,srcH_p1*_this.scaleSize)
												canvas.drawImage(_this.imgSrc2,0,0,srcW2,srcH2,0,0,deviceW,deviceH)
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
								thar.saveIf=true;
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
									thar.saveIf=true;
								}
							})
						}
						
					thar.saveIf=true;
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
										thar.saveIf=true;
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
											thar.saveIf=true;
										}
									})
								}
								
							thar.saveIf=true;
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
					}).exec()
				
			},
			edit(){
				uni.navigateTo({
					url:"edit?imgSrc="+imgSrc,
				})
			},
			savePicture(base64) {
				var arr = base64.split(',');
				var bytes = atob(arr[1]);
				let ab = new ArrayBuffer(bytes.length);
				let ia = new Uint8Array(ab);
				for (let i = 0; i < bytes.length; i++) {
					ia[i] = bytes.charCodeAt(i);
				}
				var blob = new Blob([ab], { type: 'application/octet-stream' });
				var url = URL.createObjectURL(blob);
				var a = document.createElement('a');
				a.href = url;
				a.download = new Date().valueOf() + ".png";
				var e = document.createEvent('MouseEvents');
				e.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
				a.dispatchEvent(e);
				URL.revokeObjectURL(url);
			},
			upLoadImg(){
				var _this=this;
				uni.chooseImage({
					count:1,
					sizeType:['original'],
					sourceType:['album'],
					success:function(res){
						_this.test()
						_this.imgSrc= res.tempFilePaths;
						_this.imgSrc1=_this.imgSrc[0];
						_this.pageTwo=true;
						_this.pageOne=false;
						_this.guideOnShow=true;
						_this.pageThree=false;
						uni.getStorage({
							key: 'guideUI',
							success: function (res) {
								_this.guideOnShow=false;
								console.log('123')
							}
						});
						
						_this.drawImg()
					}
				})
			}
		}
	}
</script>

<style>
	.scroll_box{
		display: flex;
		flex-direction: row;
		
	}
	.imgGui{
		position: absolute;
		z-index: 99999;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}
	.downLoad{
		width: 140px;
		height: 29px;
		user-select: none;
		position: absolute;
		left: 50%;
		transform: translate(-70px,0);
		bottom: 10%;
	}
	.downLoad image{
		
	}
	.pageOne{
		background-color: #fff;
		height: 100%;
		border: 6px solid #ff8826;

		border-radius: 10px;
	}
	.resImgBox{
		z-index: 99999;
		display: flex;
		position: absolute;
		align-items: center;
		width: 100%;
		height: 100%;
		background-color: rgba(16, 16, 16, 0.6);
	}
	.imgResult{
		z-index: 9999;
		width: 100%;
top: 0;
left: 0;
	}
	.word_bottom{
		text-align: center;
		width: 100%;
		position: absolute;
		bottom: 15%;
		color: #fff;
	}
	.word{
		left: 0;
		height: 20px;
		border-radius: 10px;
		border-bottom: 6px solid #f1d272;
		width: 100%;
		bottom: -16px;
		position: absolute;
	}
	.word p{
		font-size: 20px;
		font-weight: 900;
		color: #ff8826;
		text-align: center;
		width: 100%;
	}
	.shu{
		position: absolute;
		transform: translate(-2.5px,-35px);
		left: 50%;
		top: 50%;
		width: 0px;
		height: 70px;
		border-left: 5px solid #ff8826;
	}
	.heng{
		position: absolute;
		transform: translate(-35px,-2.5px);
		left: 50%;
		top: 50%;
		width: 70px;
		height: 0px;
		border-top: 5px solid #ff8826;
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
		top: 5%;
		margin: 0 auto;
		border-radius: 6px;
		width: 44.218vh;
		height: 76.61vh;
		background-color: #fff;
	}

.foot-Img{
	position: fixed;
	bottom: 0;
	height:10%;
	width: 100%;
}
.foot-Img image{
	width: 100%;
	height: 100%;
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
	.button_back{
			z-index: 1;
			width:70px;
			height: 40px;
			background-color: #ff6a05;
			position: absolute;
			left: -2px;
			top: 80px;
			border-top-right-radius: 25px;
			border-bottom-right-radius: 25px;
			border-left: 0px;
			border: 2px solid #feedcb;
		}
		.button_back p{
			color: #fff;
			margin-left: 10px;
			line-height: 40px;
		}
		.button_save{
			color: #fff;
				z-index: 1;
				width:70px;
				height: 40px;
				background-color: #ff6a05;
				position: absolute;
				right: -2px;
				bottom: 100px;
				border-top-left-radius: 25px;
				border-bottom-left-radius: 25px;
				border-left: 0px;
				border: 2px solid #feedcb;
			}
			.button_save p{
				margin-left: 10px;
				line-height: 40px;
			}
			.sign_border p{
				font-size: 2.5vh;
				line-height: 5.6vh;
				text-align: center;
				font-weight: 900;
				text-shadow: #f1d272 1px 0 0, #f1d272 0 1px 0, #f1d272 -1px 0 0, #f1d272 0 -1px 0;
			}
			.sign_border{
					color: #000;
						z-index: 1;
						width:70px;
						height: 5.6vh;
						background-color: #feebed;
						position: absolute;
						left: 0;
						bottom: 100px;
						border-top-left-radius: 15px;
						border-top-right-radius: 15px;
						border: 4px solid #f1d272;
						border-bottom: none;
					
			}
		.canvas-border{
			
			margin: 0 auto;
			height: 100%;
			height: 100%;
		}
		.enlarge{
			z-index: 998;
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
		.foot-ImgTwo{
			overflow: auto;
			display: flex;
			flex-direction: row;
			align-items: center;
			background-color: #feebed;
			border-top: 5.6vh solid #ffe0cc;
			position: fixed;
			bottom: 0;
			height: 100px;
			width: 100%;
		}
		.moveImg{
			z-index: 1;
			
		}
		.moveAre{
			border-radius: 10px;
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
		.imgBox_pre{
			margin-left: 40px;
			width: 70px;
			height: 70px;
		}
		.imgBox_pre img{
			width: 100%;
			height: 100%;
		}
		.coverLayer{
			z-index: 9999;
			position: absolute;
			width: 100%;
			height: 100%;
			background-color: rgba(0, 0, 0, 0.6);
		}
	canvas{
		width:100%;
		height:100%
	}
</style>
