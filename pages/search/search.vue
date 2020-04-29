<template>
	<view class="page">
		<!-- 搜索栏 -->
		<view class="search-block">
			<view class="search-ico-wapper">
				<image src="../../static/icos/search.png" class="search-ico"></image>
			</view>
			<view class="search-input">
				<input 
				confirm-type="search"
				@confirm="searchMe"
				placeholder="什么都不输入可以查看所有片源~" 
				maxlength="10" 
				class="search-text" 
				focus
				/>
			</view>
		</view>
		
		<!-- 预告片列表 -->
		<view class="movie-list page-block">
			<view 
			v-for="trailer in trailerList"
			class="movie-wapper">
				<image 
				:data-trailerId="trailer.id"
				@click="showTrailer"
				:src="trailer.cover" 
				class="poster"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				trailerList: [],
				keywords:'',
				page: 1,	//当前地址页
				totalPages: 1	//总页数
			}
		},
		onLoad() {
			this.refresh();
		},
		methods: {
			//获取预告片数据
			refresh () {
				//获取数据过程中阻止用户操作页面
				uni.showLoading({
					mask:true,
					title:"请稍后..."
				});
				uni.showNavigationBarLoading();	//虽然h5隐藏了导航条，但是小程序不能隐藏，使用一个导航条加载动画，增加用户交互。
				
				//声明this
				let me = this;
				//获取服务器地址
				// let serverUrl = common.serverUrl; //方式一
				let serverUrl = me.serverUrl; //方式二
				
				//请求预告片列表数据
				uni.request({
					url: serverUrl + '/search/list?keywords=&page=&pageSize=&qq=' + 309063773,
					method: "POST",
					success: (res) => {
						//获取数据之前先判断是否请求成功（200）
						if (res.data.status == 200) {
							let trailerList = res.data.data.rows;
							me.trailerList = trailerList;
						}
					},
					complete: () => {
						uni.hideLoading();	//成功获取数据后隐藏loading
						uni.hideNavigationBarLoading();	//成功获取数据后隐藏导航条刷新动画
					}
				});
			},
			//搜索功能
			searchMe (e) {
				let me = this;
				//获取搜索内容
				let value = e.detail.value;
				//搜索前将trailerList清空
				me.trailerList = [];
				
				me.keywords = value;
				me.pagedTrailerList(value, 1, 15);	//内容	从第一页开始搜索 显示数据条数
			},
			//分页功能
			pagedTrailerList (keywords, page, pageSize) {
				//获取数据过程中阻止用户操作页面
				uni.showLoading({
					mask:true,
					title:"请稍后..."
				});
				uni.showNavigationBarLoading();	//虽然h5隐藏了导航条，但是小程序不能隐藏，使用一个导航条加载动画，增加用户交互。
				
				//声明this
				let me = this;
				//获取服务器地址
				// let serverUrl = common.serverUrl; //方式一
				let serverUrl = me.serverUrl; //方式二
				
				//请求预告片列表数据
				uni.request({
					url: serverUrl + '/search/list?keywords='+	keywords
													+'&page='+	page
													+'&pageSize='+ pageSize
													+'&qq=' + 309063773,
					method: "POST",
					success: (res) => {
						if (res.data.status == 200) {
							let tempList = res.data.data.rows;
							me.trailerList = me.trailerList.concat(tempList);
							me.totalPages = res.data.data.total;
							me.page = page;
							uni.hideLoading();	//成功获取数据后隐藏loading
							uni.hideNavigationBarLoading();	//成功获取数据后隐藏导航条刷新动画
					}
				}
			});
		},
		//下拉加载下一页
		onReachBottom () {
			let me = this;
			
			let page = me.page + 1;	//查询下一页
			let keywords = me.keywords;	//获得当前页面中data里的搜索值
			let totalPages = me.totalPages;	//获取总页数
			
			//如果当前需要分页的分页数和总页数相等，就不分页
			if (page > totalPages) {
				return;
			}
			
			//执行分页函数
			me.pagedTrailerList(keywords, page, 15);
		},
		showTrailer (e) {
			let trailerId = e.currentTarget.dataset.trailerid;
			//页面跳转api
			uni.navigateTo({
				url: "../movie/movie?trailerId=" + trailerId
			})
		},
	}
}
</script>

<style>
	@import url("search.css");
</style>
