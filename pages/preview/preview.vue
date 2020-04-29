<template>
	<view class="black">
		<!-- src="http://122.152.205.72:88/superhero/MARVEL/Venom/cover.png" -->
		<image :src="cover" mode="widthFix" class="cover" @longpress="operator"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cover: ''
			}
		},
		methods: {
			operator() {
				let me = this;
				uni.showActionSheet({  //显示操作菜单
					itemList: ["保存图片"],
					success: (res) => {
						//res：tapIndex(用户点击的选项)  errMsg（错误信息）
						if (res.tapIndex == 0) {
							// console.log('保存成功');
							uni.showLoading({
								title: '保存中'
							});
							//保存图片	不支持h5
							uni.downloadFile({
								url: me.cover,
								success: function(result) {
									let tempFilePath = result.tempFilePath;
									// console.log(tempFilePath);
									
									//将图片保存至本地
									uni.saveImageToPhotosAlbum({
										filePath: tempFilePath,
										success: function() {
											uni.showToast({
												title: '保存成功',
												duration: 2000
											});
										},
										complete: function() {
											uni.hideLoading();
										}
									});
								}
							});
						}
					}
				});
			}
		},
		// 使用params可以接收到别的组件传出的参数
		onLoad(params) {
			let cover = params.cover;
			this.cover = cover;
			
			//通过api修改导航栏的属性
			uni.setNavigationBarColor({
				frontColor: "#ffffff",		//#FFFFFF 不支持大写
				backgroundColor: "#000000"
			});
		}
	}
</script>

<style>
.black { 
	background-color: #000000;
	width: 100%;
	height: 100%;
	
	display: flex;
	flex-direction: column;
	justify-content: center;
	position: fixed;
}
.cover {
	align-self: center;
}
</style>
