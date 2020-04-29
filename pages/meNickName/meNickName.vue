<template>
	<view class="page page-fill">
		<form @submit="formSubmitNickname">
					
					<view class="page-block" style="margin-top: 20upx;">
						<input 
							type="text"
							name="nickname"
							:value="globalUser.nickname" 
							class="input"
							placeholder="请输入昵称"
							placeholder-class="graywords"
							maxlength="10"
							/>
					</view>
					
					<button type="primary" form-type="submit" class="submitBtn">提交</button>
					
				</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				globalUser: {}
			}
		},
		onLoad() {
			let me = this;
			let globalUser = me.getGlobalUser("globalUser");
			me.globalUser = globalUser;
		},
		methods: {
			formSubmitNickname(e) {
				let me = this;
				let nickname = e.detail.value.nickname;
				
				uni.request({
					url: me.serverUrl + "/user/modifyUserinfo?qq=309063773",
					data: {
						"userId": me.globalUser.id,
						"sex": me.globalUser.sex,
						"nickname": nickname,
						"birthday": me.globalUser.birthday
					},
					//拿到用户id token
					header: {
						//将用户id与token作为header发送给后端
						"headerUserId": me.globalUser.id,
						"headerUserToken": me.globalUser.userUniqueToken
					},
					method: "POST",
					success(res) {
						let resData = res.data;
						// console.log(resData);
						if (resData.status == 200) {
							//如果成功，获取最新的用户数据
							let userInfo = resData.data;
							//获取最新用户数据后覆盖原用户数据
							uni.setStorageSync("globalUser", userInfo);
							uni.navigateBack({	//返回上一页面
								delta: 1
							});
						} else if (resData.suatus == 502 || resData.suatus == 500) {
							uni.showToast({
								title: res.data.msg,
								image: "../../static/icos/error.png",
								duration: 2000
							});
						}
					}
				})
			}
		}
	}
</script>

<style>
/* 页面铺满屏幕 */
.page-fill {
	width:100%;
	height: 100%;
	position: absolute;
}

.graywords {
	color: #EAEAEA;
}

.input {
	height: 80upx;
	line-height: 80upx;
	width: 500upx;
	margin-left: 40upx;
}

.submitBtn {
	width: 95%;
	margin-top: 40upx;
}
</style>
