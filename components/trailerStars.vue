<template name="trailerStars">
	<view class="movie-score-wapper">
		<!-- <image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-gray.png" class="star-ico"></image>
		<view class="movie-score">
			9.1
		</view> -->
		<image
		v-for="yellow in yellowScore"
		src="/static/icos/star-yellow.png" 
		class="star-ico"
		></image>
		
		<image
		v-for="gray in grayScore"
		src="/static/icos/star-gray.png" 
		class="star-ico"
		></image>
		
		<view class="movie-score" v-if="show == 1">
			{{innerScore}}
		</view>
	</view>
</template>

<script>
	export default {
		name:'trailerStars',
		data() {
			return {
				yellowScore: 0,
				grayScore: 5
			};
		},
		props: {
			innerScore: 0,	//外部传入分数
			show: 0		//是否需要显示具体分数 1：显示 	0：不显示
		},
		//组件创建完成后调用
		created() {
			let tempScore = 0;
			if (this.innerScore != null && this.innerScore != undefined && this.innerScore != '') {
				tempScore = this.innerScore;
			}
			
			//星星数计算
			let yellowScore = parseInt(tempScore / 2);	//总分10分 星星5颗
			let grayScore = 5 - yellowScore;
			
			this.yellowScore = yellowScore;
			this.grayScore = grayScore;
		}
	}
</script>

<style scoped>
.movie-score-wapper {
	display: flex;
	flex-direction: row;
}
.star-ico {
	width: 20upx;
	height: 20upx;
	margin-top: 6upx;
}
.movie-score {
	font-size: 12px;
	color: grey;
	margin-left: 8upx;
}
</style>
