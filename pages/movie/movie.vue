<template>
	<view class="page">
		<!-- 视频播放 start -->
		<view class="player">
			<video 
			id="myTrailer"
			:src="trailerInfo.trailer" 
			:poster="trailerInfo.poster" 
			controls 
			class="movie"
			></video>
		</view>
		<!-- 视频播放 end -->
		
		<!-- 影片基本信息 start -->
		<view class="movie-info">			<!-- 拼接cover，将cover作为参数传出 -->
			<navigator :url="'../preview/preview?cover=' + trailerInfo.cover">
				<image 
				:src="trailerInfo.cover" 
				class="movie-cover"
				></image>
			</navigator>
			<!-- 详细信息 -->
			<view class="movie-desc">
				<view class="movie-title">{{trailerInfo.name}}</view>
				<view class="basic-info">{{trailerInfo.basicInfo}}</view>
				<view class="basic-info">{{trailerInfo.originalName}}</view>
				<view class="basic-info">{{trailerInfo.totalTime}}</view>
				<view class="basic-info">{{trailerInfo.releaseDate}}</view>
				
				<!-- 评分 -->
				<view class="score-block">
					<view class="score-big">
						<view class="score-words">综合评分：</view>
						<view class="movie-score">{{trailerInfo.score}}</view>
					</view>
					
					<view class="score-stars">
						<!-- 
						 关于block的解释：
						 页面加载完成最先读取的到的trailerInfo.score是data里所定义的对象，
						 所以读取到的只有undefined，导致星星组件计算错误，星星数量无法显示，
						 利用v-if判断当trailerInfo.score >= 0时再显示星星组件，解决问题。
						 -->
						<block v-if="trailerInfo.score >= 0">
							<trailerStars :innerScore="trailerInfo.score" show="0"></trailerStars>
						</block>
						<view class="praise-counts">
							{{trailerInfo.prisedCounts}} 人点赞
						</view>
					</view>
					
				</view>
			</view>
		</view>
		<!-- 影片基本信息 end -->
		
		<!-- 分割线 -->
		<view class="line-wapper">
			<view class="line"></view>
		</view>
		
		<!-- 剧情介绍 start -->
		<view class="plots-block">
			<view class="plots-title">剧情介绍</view>
			<view class="plots-desc">{{trailerInfo.plotDesc}}</view>
		</view>
		<!-- 剧情介绍 end -->
		
		<!-- 演职人员 start -->
		<view class="scroll-block">
			<view class="plots-title">演职人员</view>
				<scroll-view scroll-x="true" class="scroll-list">
					<view class="director-wapper" v-for="(director, staffIndex) in directorArray">
						<!-- 导演照片 -->
						<image
						:src="director.photo" 
						class="single-actor"
						mode="aspectFill"
						@click="lookStaffs"
						:data-staffIndex="staffIndex"
						></image>
						<view class="director-name">{{director.name}}</view>
						<view class="director-actName">{{director.actName}}</view>
					</view>
					
					<view class="actor-wapper" v-for="(actor, actorIndex) in actorArray">
						<!-- 演员照片 -->
						<image
						:src="actor.photo" 
						class="single-actor"
						mode="aspectFill"
						@click="lookStaffs"
						:data-staffIndex="actorIndex + directorArray.length"
						></image>
						<!-- 从0累加 并加上导演数据的长度 -->
						<view class="actor-name">{{actor.name}}</view>
						<view class="actor-actName">{{actor.actName}}</view>
					</view>
				</scroll-view>
		</view>
		<!-- 演职人员 end -->
		
		<!-- 剧照 start -->
		<view class="scroll-block">
			<view class="plots-title">剧照</view>
				<scroll-view scroll-x="true" class="scroll-list">
					<image 
					v-for="(img, imgIndex) in plotPicsArray"
					:src="img" 
					class="plots-image"
					mode="aspectFill"
					@click="lookMe"
					:data-imgIndex="imgIndex"
					></image>
				</scroll-view>
		</view>
		<!-- 剧照 end -->
	</view>
</template>

<script>
	import trailerStars from '../../components/trailerStars.vue';
	
	export default {
		data() {
			return {
				trailerInfo: {},
				plotPicsArray: [],	//剧照列表
				directorArray: [],	//导演列表
				actorArray: [],		//演员列表
			}
		},
		onLoad(params) {
			let me = this;
			//获取上一个页面传入的参数
			let trailerId = params.trailerId;
			
			//通过api修改导航栏的属性
			uni.setNavigationBarColor({
				frontColor: "#ffffff",
				backgroundColor: "#000000"
			});
			
			//获取预告片的详细信息
			let serverUrl = me.serverUrl; //方式二
			
			//请求影片基本信息数据
			uni.request({
				url: serverUrl + '/search/trailer/'+ trailerId + '?qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						let trailerInfo = res.data.data;
						me.trailerInfo = trailerInfo;
						//转换后端传来的剧照字符串转换为JSON数组
						let plotPicsArray = JSON.parse(trailerInfo.plotPics);
						me.plotPicsArray = plotPicsArray;
					}
				}
			});
			
			//请求影片导演数据
			uni.request({
				url: serverUrl + '/search/staff/'+ trailerId + '/1?qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						me.directorArray = res.data.data;
					}
				}
			});
			
			//请求影片演员数据
			uni.request({
				url: serverUrl + '/search/staff/'+ trailerId + '/2?qq=' + 309063773,
				method: "POST",
				success: (res) => {
					//获取数据之前先判断是否请求成功（200）
					if (res.data.status == 200) {
						me.actorArray = res.data.data;
					}
				}
			});
		},
		//页面渲染完成，获得视频上下文对象
		onReady() {
			this.videoContext = uni.createVideoContext('myTrailer');
		},
		//页面被隐藏时暂停
		onHide() {
			this.videoContext.pause();
		},
		//页面被显示时继续播放
		onShow() {
			if (this.videoContext) {
				this.videoContext.play();
			}
		},
		//分享	只支持小程序，分享到微信群或微信好友
		onShareAppMessage() {
			let me = this;
			return {
			      title: me.trailerInfo.name,
			      path: '/pages/movie/movie?trailerId=' + me.trailerInfo.id
			    }
		},
		//监听导航栏的按钮
		onNavigationBarButtonTap(e) {
			//拿到分享需要的内容
			let me = this;
			let trailerInfo = me.trailerInfo;
			let trailerId = trailerInfo.id;
			let trailerName = trailerInfo.name;
			let cover = trailerInfo.cover;
			let poster = trailerInfo.poster;
			// "http://imovietrailer.com/pages/movie/movie?trailerId=" + trailerId
			
			let index = e.index;
			if (index == 0) {
				uni.share({
				    provider: "weixin",
				    scene: "WXSenceTimeline",
				    type: 0,
				    href: "http://www.imovietrailer.com/#/pages/movie/movie?trailerId=" + trailerId,
				    title: "电影预告：《" + trailerName + "》",
				    summary: "电影预告：《" + trailerName + "》",
				    imageUrl: poster,
				    success: function (res) {
				        console.log("success:" + JSON.stringify(res));
				    },
				    fail: function (err) {
				        console.log("fail:" + JSON.stringify(err));
				    }
				});
			}
		},
		components: {
			trailerStars
		},
		methods: {
			lookMe (e) {
				let me = this;
				let imgIndex = e.currentTarget.dataset.imgindex;
				
				uni.previewImage({
					urls: me.plotPicsArray,
					current: me.plotPicsArray[imgIndex]
				});
			},
			
			lookStaffs (e) {
				let me = this;
				let staffIndex = e.currentTarget.dataset.staffindex;
				
				//拼接导演与演员的数组，成为一个新数组
				let directorArray = me.directorArray;
				let actorArray = me.actorArray;
				let newStaffArray = [];
				newStaffArray = newStaffArray.concat(directorArray).concat(actorArray);
				let urls = [];
				for (let i = 0; i < newStaffArray.length; i++) {
					let tempPhoto = newStaffArray[i].photo;
					urls.push(tempPhoto);
				}
				uni.previewImage({
					urls: urls,
					current: urls[staffIndex]
				});
			}
		}
	}
</script>

<style>
@import url("movie.css");
</style>
