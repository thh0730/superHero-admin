<template>
	<view class="page">
		<!-- 头部轮播图 -->
		<swiper :indicator-dots="true" :autoplay="true" class="carousel">
			<!-- <swiper-item>
				<image src="../../static/carousel/batmanvssuperman.png" mode="" class="carousel"></image>
			</swiper-item>
			<swiper-item>
				<image src="../../static/carousel/spiderman.png" mode="" class="carousel"></image>
			</swiper-item> -->
			<swiper-item v-for="carousel in carouselList">
				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + carousel.movieId">
					<image :src="carousel.image" mode="" class="carousel"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 轮播图end -->

		<!-- 热门超英 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/hot.png" class="hot-ico"></image>
				<view class="hot-title">
					热门超英
				</view>
			</view>
		</view>

		<scroll-view scroll-x="true" class="page-block scroll-hot">
			<view class="single-poster" v-for="superhero in hotSpuerheroList">
				<view class="poster-wapper">
					<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + superhero.id">
						<image :src="superhero.cover" class="poster"></image>
					</navigator>
					<view class="movie-name">
						{{superhero.name}}
					</view>

					<!-- 星级组件 -->
					<trailerStars :innerScore="superhero.score" show="1"></trailerStars>

				</view>
			</view>
		</scroll-view>
		<!-- 热门超英 end -->

		<!-- 热门预告 start -->
		<!-- 标题部分 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/interest.png" class="hot-ico"></image>
				<view class="hot-title">
					热门预告
				</view>
			</view>
			<view class="hot-movies page-block">
				<video 
				v-for="trailer in hotTrailerList" 
				:src="trailer.trailer" 
				:poster="trailer.poster" 
				controls class="hot-movies-single"
				:id="trailer.id"
				:data-playingIndex="trailer.id"
				@play="iAmPlaying"
				></video>
			</view>
		</view>
		<!-- 热门预告 end -->

		<!-- 猜你喜欢 start -->
		<!-- 1.标题 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/guess-u-like.png" class="hot-ico"></image>
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>

		<view class="page-block guess-u-like">
			<view class="single-like-movie" v-for="(guess,gIndex) in guessULikeList">
				<!-- 2.海报 -->
				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + guess.id">
					<image :src="guess.cover" class="like-moive-poster"></image>
				</navigator>
				
				<!-- 3.电影简介 -->
				<view class="movie-desc">
					<view class="movie-title">
						{{guess.name}}
					</view>
					<trailerStars :innerScore="guess.score" show="0"></trailerStars>
					<view class="movie-info">
						{{guess.basicInfo}}
					</view>
					<view class="movie-info">
						{{guess.releaseDate}}
					</view>
				</view>
				<!-- 3.点赞 -->
				<view class="movie-oper" :data-gIndex="gIndex" @click="praiseMe">
					<image src="../../static/icos/praise.png" class="praise-ico"></image>
					<view class="praise-me">
						点赞
					</view>
					<!-- 点赞动画 -->
					<view :animation="animationDataArr[gIndex]" class="praise-me animation-opacity">
						+1
					</view>
				</view>
			</view>
		</view>
		<!-- 猜你喜欢 end -->
	</view>
</template>

<script>
	import common from "../../common/common.js";
	import trailerStars from "../../components/trailerStars.vue";

	export default {
		data() {
			return {
				carouselList: [],
				hotSpuerheroList: [],
				hotTrailerList: [],
				animationData: {},
				guessULikeList: [],
				animationDataArr: [
					{},{},{},{},{}
				]
			}
		},
		onLoad() {
			//声明this
			let me = this;
			//获取服务器地址
			// let serverUrl = common.serverUrl; //方式一
			let serverUrl = me.serverUrl; //方式二

			//请求轮播图数据
			uni.request({
				url: serverUrl + '/index/carousel/list?qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						let carouselList = res.data.data;
						me.carouselList = carouselList;
					}
				}
			});

			//请求热门超英数据
			uni.request({
				url: serverUrl + '/index/movie/hot?type=superhero&qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						let hotSpuerheroList = res.data.data;
						me.hotSpuerheroList = hotSpuerheroList;
					}
				}
			});

			//请求热门预告片视频数据
			uni.request({
				url: serverUrl + '/index/movie/hot?type=trailer&qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						let hotTrailerList = res.data.data;
						me.hotTrailerList = hotTrailerList;
					}
				}
			});
			
			me.refresh();
			/*
			//请求猜你喜欢列表数据
			uni.request({
				url: serverUrl + '/index/guessULike?qq=' + 309063773,
				method: "POST",
				success: (res) => {
					console.log('猜你喜欢');
					console.log(res.data);
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						let guessULikeList = res.data.data;
						me.guessULikeList = guessULikeList;
					}
				}
			});
			*/
			
			//创建临时动画对象
			//条件编译	ios、安卓 || 微信小程序
			// #ifdef APP-PLUS || MP-WEIXIN
			this.animation = uni.createAnimation();
			// #endif
		},
		onUnload() {
			//页面关闭时清除动画数据
			this.animation = {};
			this.animationDataArr = [
				{},{},{},{},{}
			];
		},
		onHide() {
			//当页面被隐藏
			if (this.videoContext) {
				this.videoContext.pause();
			}
		},
		//监听用户下拉操作的生命周期函数
		onPullDownRefresh() {
			this.refresh();
		},
		methods: {
			//实现点赞动画效果
			praiseMe (e) {
				// #ifdef APP-PLUS || MP-WEIXIN
				//通过dataset获取的内容全都是小写
				//拿到用户所点击的那个数组元素的索引
				let gIndex = e.currentTarget.dataset.gindex;
				//构建动画数据，并且通过step来表示这组动画的完成
				this.animation.translateY(-60).opacity(1).step({
					duration:200
				});
				//导出动画数据到view组件，实现组件效果
				this.animationData = this.animation;
				//用户点击哪个导出哪个
				this.animationDataArr[gIndex] = this.animationData.export();
				
				//还原动画
				setTimeout(function() {
					this.animation.translateY(0).opacity(0).step({
					duration:0
				});
				//导出动画数据到view组件，实现组件效果
				this.animationData = this.animation;
				//用户点击哪个导出哪个
				this.animationDataArr[gIndex] = this.animationData.export();
				}.bind(this), 400);
				// #endif
			},
			//刷新
			refresh () {
				//获取数据过程中阻止用户操作页面
				uni.showLoading({
					mask:true
				});
				uni.showNavigationBarLoading();	//虽然h5隐藏了导航条，但是小程序不能隐藏，使用一个导航条加载动画，增加用户交互。
				
				//声明this
				let me = this;
				//获取服务器地址
				// let serverUrl = common.serverUrl; //方式一
				let serverUrl = me.serverUrl; //方式二
				
				//请求猜你喜欢列表数据
				uni.request({
					url: serverUrl + '/index/guessULike?qq=' + 309063773,
					method: "POST",
					success: (res) => {
						//获取数据之前先判断是否请求成功（200）
						if (res.data.status == 200) {
							let guessULikeList = res.data.data;
							me.guessULikeList = guessULikeList;
						}
					},
					complete: () => {
						uni.hideLoading();	//成功获取数据后隐藏loading
						uni.stopPullDownRefresh();	//成功获取数据后隐藏刷新动画
						uni.hideNavigationBarLoading();	//成功获取数据后隐藏导航条刷新动画
					}
				});
			},
			//播放一个视频时需要暂停其他视频
			iAmPlaying(e) {
				let me = this;
				let trailerId = "";
				if (e) {
					trailerId = e.currentTarget.dataset.playingindex;
					me.videoContext = uni.createVideoContext(trailerId);
				}
				let hotTrailerList = me.hotTrailerList;
				for (let i = 0; i < hotTrailerList.length; i++) {
					let tempId = hotTrailerList[i].id;
					if (tempId != trailerId) {
						uni.createVideoContext(tempId).pause();
					}
				}
			},	
		},
		components: {
			trailerStars
		}
	}
</script>

<style>
	@import url("index.css");
</style>
