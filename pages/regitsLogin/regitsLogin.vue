<template>
	<view class="body">
		<form @submit="formSubmit">
			<!-- 头像 -->
			<view class="face-wapper">
				<image src="../../static/icos/default-face.png" class="face"></image>
			</view>
			
			<!-- tips -->
			<view class="tips-wapper">
				<view class="third-words">tips: 新用户输入账号密码即注册</view>
			</view>
			
			<view class="info-wapper">
				<label for="" class="words-lbl">账号</label>
				<input name="username" type="text" value="" class="input" placeholder="请输入用户名" placeholder-class="graywords" />
			</view>

			<view class="info-wapper">
				<label for="" class="words-lbl">密码</label>
				<input name="password" type="text" password="true" value="" class="input" placeholder="请输入密码" placeholder-class="graywords" />
			</view>

			<button form-type="submit" type="primary" style="margin-top: 60upx; width: 70%;">注册/登录</button>
		</form>

		<!-- 第三方登录H5不支持，所以隐藏掉 -->
		<!-- #ifndef H5 -->
		<view class="third-wapper">

			<view class="third-line">
				<view class="single-line">
					<view class="line"></view>
				</view>

				<view class="third-words">第三方账号登录</view>

				<view class="single-line">
					<view class="line"></view>
				</view>
			</view>

			<view class="third-icos-wapper">
				<!-- 5+app 用qq/微信/微博 登录 小程序用微信小程序登录 h5不支持 -->
				<!-- #ifdef APP-PLUS -->
				<image src="../../static/icos/QQ.png" class="third-ico" style="margin-left: 0upx;" data-logintype="qq" @click="appOAuthLogin"></image>
				<image src="../../static/icos/weibo.png" class="third-ico" style="margin-left: 80upx;" data-logintype="sinaweibo" @click="appOAuthLogin"></image>
				<!-- #endif -->

				<!-- #ifdef MP-WEIXIN -->
				<button open-type='getUserInfo' @getuserinfo="wxLogin" class="third-btn-ico">
				</button>
				<!-- #endif -->
			</view>
		</view>
		<!-- #endif -->

	</view>
</template>

<script>
	export default {
		data() {
			return {

			}
		},
		methods: {
			appOAuthLogin(e) {
				let me = this;
				// 获取用户的登录类型
				let logintype = e.currentTarget.dataset.logintype;
				// 授权登录
				uni.login({
					provider: logintype,
					success(loginRes) {

						// 授权登录成功以后，获取用户的信息
						uni.getUserInfo({

							provider: logintype,
							success(info) {
								// console.log(JSON.stringify(info));

								let userInfo = info.userInfo;
								// console.log(userInfo);
								let face = "";
								let nickname = "";
								let openIdOrUid = "";
								if (logintype == "weixin") {
									face = userInfo.avatarUrl;
									nickname = userInfo.nickName;
									openIdOrUid = userInfo.openId;
								} else if (logintype == "qq") {
									openIdOrUid = userInfo.openId;
									nickname = userInfo.nickname;
									face = userInfo.figureurl_qq_2;
									console.log('face:' + face);
									console.log('nickname:' + nickname);
									console.log('openIdOrUid:' + openIdOrUid);
								} else if (logintype == "sinaweibo") {
									openIdOrUid = userInfo.id;
									nickname = userInfo.nickname;
									face = userInfo.avatar_large;
									console.log('face:' + face);
									console.log('nickname:' + nickname);
									console.log('openIdOrUid:' + openIdOrUid);
								}
								let url = me.serverUrl + "/appUnionLogin/" + logintype + "?qq=309063773"
								// 调用开发者后台，执行一键注册或登录
								uni.request({
									url: url,
									data: {
										"openIdOrUid": openIdOrUid,
										"nickname": nickname,
										"face": face
									},
									method: "POST",
									success(result) {
										console.log(url);
										console.log(result);
										// console.log('aaaaaa');
										if (result.data.status == 200) {
											let userInfo = result.data.data;
											// 保存用户信息到全局的缓存中
											uni.setStorageSync("globalUser", userInfo);
											// 切换页面跳转，使用tab切换的api
											uni.switchTab({
												url: "../me/me"
											});
										}
									}
								})

							}
						})

					}
				});
			},
			
			// 因特殊原因暂时没法实现
			wxLogin(e) {
				let me = this;
				// console.log(e);
				//通过button的微信开放能力获取用户的基本信息
				let userInfo = e.detail.userInfo;
				// console.log(userInfo);
				//实现微信登录
				uni.login({
					provider: "weixin",
					success(loginResult) {
						// console.log(loginResult);
						//获取微信登录的code：授权码
						let wxCode = loginResult.code;
						//获取微信登录的code后发送请求
						//设置登录到对应的小程序，可以根据后端去实现业务参数
						let loginToWhichMP = 1;
						//发送请求
						// uni.request({
						// 	// url: me.serverUrl + "/stu/mpWXLogin/" + wxCode + "?qq=" + 309063773,	微信小程序的appid和秘钥是腾讯官方的，大家申请的小程序肯定是不一样的。需要后端配合实现
						// 	data: {
						// 		"avatarUrl": userInfo.avatarUrl,
						// 		"nickName": userInfo.nickName,
						// 		"whichMP": loginToWhichMP
						// 	},
						// 	method:"POST",
						// 	success(userResult) {
						// 		console.log(userResult);
						//保存用户信息到全局的缓存中
						// let userInfo = userResult.data.data;
						// uni.setStorageSync("globalUser", userInfo);
						// //注册/登录成功后跳转
						// uni.switchTab({
						// 	url: "../me/me"
						// });
						// 	}
						// });
					}
				})
			},
			formSubmit(e) {
				let me = this;
				let username = e.detail.value.username;
				let password = e.detail.value.password;
				// console.log(userName);
				// console.log(passWord);

				//发起注册/登录请求
				let serverUrl = me.serverUrl; //方式二

				//请求数据
				uni.request({
					url: serverUrl + '/user/registOrLogin?qq=' + 309063773,
					data: {
						"username": username,
						"password": password
					},
					method: "POST",
					success: (res) => {
						//获取数据之前先判断是否请求成功（200）
						if (res.data.status == 200) {
							//保存用户信息到全局的缓存中
							let userInfo = res.data.data;
							// console.log(userInfo);
							uni.setStorageSync("globalUser", userInfo);
							//注册/登录成功后跳转
							uni.switchTab({
								url: "../me/me"
							});
						} else if (res.data.status == 500) {
							uni.showToast({
								title: res.data.msg,
								duration: 2000,
								image: "../../static/icos/error.png"
							})
						}
					}
				});
			}
		}
	}
</script>

<style>
	@import url("regitsLogin.css");
</style>
