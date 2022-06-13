<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	.imgBox img{
		width: 100%;
		height: 100%;
	}
	.moveImg{
		z-index: -99;
	}
	*{
		margin: 0;
		padding: 0;
	}
	/*每个页面公共css */
	uni-image{
		width: 100%;
	}
	uni-page-body{
		height: 100%;
	}
</style>
