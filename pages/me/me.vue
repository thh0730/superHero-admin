<template>
	<view class="page page-fill">
		<view class="header">
			<!-- 头像 -->
			<!-- 判断用户是否登录，登录后显示默认头像 -->
			<view v-if="userIsLogin">
				<image :src="userInfo.faceImage" class="face"></image>
			</view>
			<view v-else>
				<image src="http://122.152.205.72:88/group1/M00/00/05/CpoxxFw_-5-AFyVyAABLIH8xBTw233.png" class="face"></image>
			</view>
			
			<!-- 用户信息 -->
			<view class="info-wapper" v-if="userIsLogin">
				<view class="nickname">
					{{userInfo.nickname}}
				</view>
				<view class="nav-info">ID: {{userInfo.id}}</view>
			</view>
			<view v-else>
				<navigator url="../regitsLogin/regitsLogin">
					<view class="nickname regist-login">
						注册/登录
					</view>
				</navigator>
			</view>
			
			<!-- 设置 -->
			<view class="set-wapper" v-if="userIsLogin">
				<navigator url="../meInfo/meInfo">
					<image src="../../static/icos/settings.png" class="settings"></image>
				</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userIsLogin: false,
				userInfo: {}
			}
		},
		onShow() {
			let me = this;
			//用户状态切换
			// let userInfo = uni.getStorageSync("globalUser");
			// if (userInfo != null && userInfo != "" && userInfo != undefined) {
			// 	me.userIsLogin = true;
			// 	//数据绑定修改用户昵称
			// 	me.userInfo = userInfo;
			// } else {
			// 	me.userIsLogin = false;
			// 	me.userInfo = {};
			// }
			
			//使用已挂载的方法获取用户信息
			let userInfo=me.getGlobalUser("globalUser");
			if (userInfo != null) {
				me.userIsLogin = true;
				me.userInfo = userInfo;
			} else {
				me.userIsLogin = false;
				me.userInfo = {};
			}
		},
		methods: {
			
		}
	}
</script>

<style>
@import url("me.css");
</style>
