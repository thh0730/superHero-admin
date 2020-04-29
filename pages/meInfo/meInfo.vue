<template>
	<view class="page-fill">
		<!-- 用户信息 start-->
		<view class="page-block info-list">
			<!-- 头像 -->
			<view class="item-wapper face-line-upbottom" @click="operator">
				<view class="info-words">头像</view>

				<view class="right-wapper">
					<image :src="globalUser.faceImage" class="face"></image>

					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<!-- 分割线 -->
				<view class="line"></view>
			</view>

			<!-- 昵称 -->
			<view class="item-wapper" @click="modifyNickName">
				<view class="info-words">昵称</view>

				<view class="right-wapper">
					<view class="gray-fields">
						{{globalUser.nickname}}
					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<view class="line"></view>
			</view>

			<!-- 生日 -->
			<view class="item-wapper" @click="modifyBirthday">
				<view class="info-words">生日</view>

				<view class="right-wapper">
					<view class="gray-fields">
						{{globalUser.birthday}}
					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<view class="line"></view>
			</view>

			<!-- 性别 -->
			<view class="item-wapper" @click="modifySex">
				<view class="info-words">性别</view>

				<view class="right-wapper">
					<view class="gray-fields">
						<view v-if="globalUser.sex==1">
							男
						</view>
						<view v-else-if="globalUser.sex==0">
							女
						</view>
						<view v-else>
							未选择
						</view>
					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

		</view>
		<!-- 用户信息 end -->

		<!-- 清理缓存以及退出 start -->
		<view class="footer-wapper">
			<view class="footer-words" @click="cleanStorage">
				清理缓存
			</view>
			<view class="footer-words" style="margin-top: 10upx;" @click="logout">
				退出登录
			</view>
		</view>
		<!-- 清理缓存以及退出 end -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				globalUser: {}
			}
		},
		onShow() {
			let me = this;
			let globalUser = me.getGlobalUser("globalUser");
			me.globalUser = globalUser;
		},
		methods: {
			//性别相关
			modifySex() {
				uni.navigateTo({
					url: "../sex/sex"
				})
			},
			//生日相关
			modifyBirthday() {
				uni.navigateTo({
					url: "../meBirthday/meBirthday"
				})
			},
			//昵称相关
			modifyNickName() {
				uni.navigateTo({
					url: "../meNickName/meNickName",
				});
			},
			// 头像相关
			operator() {
				let me = this;
				let globalUser = me.getGlobalUser("globalUser"); //全局用户对象

				uni.showActionSheet({
					itemList: ["预览头像", "从相册选择头像"],
					success(res) {
						let index = res.tapIndex;
						if (index == 0) {
							//预览
							let faceArr = [];
							faceArr.push(globalUser.faceImage);
							uni.previewImage({
								urls: faceArr,
								current: faceArr[0]
							})
						} else if (index == 1) {
							//上传头像
							uni.chooseImage({
								count: 1,
								sizeType: ["compressed"],
								sourceType: ["album"],
								success(res) {
									// 获得临时路径
									var tempFilePath = res.tempFilePaths[0];
									// #ifdef H5
									uni.navigateTo({
										url: "../meFace/meFace?tempFilePath=" + tempFilePath
									})
									// #endif
									// #ifndef H5
									uni.navigateTo({	//图片剪裁插件
										url: "../faceCrop/faceCrop?tempFilePath=" + tempFilePath
									})
									// #endif
								}
							});
						};
					}
				})
			},
			cleanStorage() { //清理缓存
				uni.clearStorage();
				uni.showToast({
					title: "清理成功！",
					mask: false,
					duration: 1500
				});

			},
			//退出
			logout() {
				let me = this;
				let globalUser = me.getGlobalUser("globalUser");

				let serverUrl = me.serverUrl; //方式二
				//请求数据
				uni.request({
					url: serverUrl + '/user/logout?userId=' + globalUser.id + '&qq=' + 309063773,
					method: "POST",
					success: (res) => {
						//获取数据之前先判断是否请求成功（200）
						if (res.data.status == 200) {
							//退出成功
							uni.removeStorageSync("globalUser");
							uni.switchTab({
								url: "../me/me"
							});
						}
					}
				});
			}
		}
	}
</script>

<style>
	@import url("meInfo.css");
</style>
