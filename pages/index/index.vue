<template>
	<view class="indexContainer">
	  <!-- 轮播图区域 -->
	  <swiper class="banners" indicator-dots indicator-color="ivory" indicator-active-color="#d43c33">
	    <swiper-item v-for="(item, index) in bannerList" :key="index">
	      <image :src="item.pic"></image>
	    </swiper-item>
	  </swiper>
	
	  <!-- 五个图标导航区域 -->
	  <view class="navContainer">
	    <view class="navItem" @click="toRecommendSong">
	      <text class="iconfont icon-meirituijian"></text>
	      <text>每日推荐</text>
	    </view>
	    <view class="navItem">
	      <text class="iconfont icon-gedan1"></text>
	      <text>歌单</text>
	    </view>
	    <view class="navItem">
	      <text class="iconfont icon-icon-ranking"></text>
	      <text>排行榜</text>
	    </view>
	    <view class="navItem">
	      <text class="iconfont icon-diantai"></text>
	      <text>电台</text>
	    </view>
	    <view class="navItem">
	      <text class="iconfont icon-zhiboguankanliangbofangsheyingshexiangjixianxing"></text>
	      <text>直播</text>
	    </view>
	  </view>
	
	  <!-- 推荐歌曲区域-->
	  <view class="recommendContainer">
	  <!-- 头部区域 -->
	  <NavHeader title="推荐歌曲" nav="为你精心推荐"></NavHeader>
	  <!-- 内容区 -->
	 <scroll-view class="recommendScroll" enable-flex scroll-x>
	    <view class="scrollItem" v-for="item in recommendList">
	      <image :src="item.picUrl"></image>
	      <text>{{item.name}}</text>
	    </view>
	  </scroll-view>
	  </view>
	
	  <!-- 排行榜区域 -->
	  <view class="topList">
	    <!-- 头部区域 -->
	    <NavHeader title="排行榜" nav="热歌风向标"></NavHeader>
	    <!-- 内容区域 -->
	   <swiper class="topListSwiper" circular next-margin="50rpx" previous-margin="50rpx">
	      <swiper-item v-for="item in topList" :key="item.name">
	        <view class="swiperItem">
	          <view class="title">{{item.name}}</view>
	        <view class="musicItem" v-for="(musicItem, index) in item.tracks" :key="musicItem.id">
	          <image :src="musicItem.al.picUrl"></image>
	          <text class="count">{{index + 1}}</text>
	          <text class="musicName">{{musicItem.name}}</text>
	        </view>
	        </view>
	      </swiper-item>
	    </swiper>
	  </view>
	</view>
</template>

<script>
	import request from '../../utils/request.js'
	import NavHeader from '../../components/NavHeader/NavHeader.vue'
	export default {
		data() {
			return {
				bannerList: [], //轮播图数据
				recommendList: [], //推荐歌单
				topList: [], //排行榜数据
			}
		},
		async onLoad() {
			let result = await request('/banner',{type:2})
			this.bannerList = result.banners
			// 	//获取推荐歌单数据
			let recommendListData = await request('/personalized',{limit:10})
			this.recommendList = recommendListData.result
				

			// 	//获取排行版数据
				let resultArr = [];
				let topListData = await request('/toplist')

				for (let i = 0; i < 5; i++) {
				  let id = topListData.list[i].id
				  let topListItems = await request('/playlist/detail',{id:id})
				  let topListItem = {name: topListItems.playlist.name, tracks: topListItems.playlist.tracks.slice(0,3)}
				  resultArr.push(topListItem)
				  //不需要等待5次请求全部结束才更新，用户体验较好，但是渲染次数会多一些
					this.topList = resultArr
				}
			// 	//更新topList的状态值，放在此处更新会导致发送请求的过程中页面长时间白屏， 用户体验差
			// 	this.setData({
			// 	  topList: resultArr
			// 	})
		},
		methods: {
		//跳转至recommendSong页面的回调
		  toRecommendSong(){
			uni.navigateTo({
			  url: '/pages/recommendSong/recommendSong',
			})
		  },
		},
		components:{
			NavHeader
		}
	}
</script>

<style>
	/* pages/index/index.wxss */
	.banners{
	  width: 100%;
	  height: 300rpx;
	}
	
	.banners image{
	  width: 100%;
	  height: 300rpx;
	}
	
	/* 五个图标导航区域 */
	.navContainer {
	  display: flex;
	}
	
	.navItem {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	  width: 20%;
	}
	
	.navItem .iconfont {
	  width: 100rpx;
	  height: 100rpx;
	  border-radius: 50%;
	  text-align: center;
	  line-height: 100rpx;
	  background: rgb(240, 19, 19);
	  font-size: 50rpx;
	  color: #fff;
	  margin: 20rpx 0;
	}
	
	.navItem text {
	  font-size: 26rpx;
	}
	
	/* 推荐歌曲 */
	.recommendContainer{
	  padding: 20rpx;
	}
	
	/* 推荐内容区 */
	.recommendScroll{
	  display: flex;
	  height: 300rpx;
	  white-space: nowrap;
	  width: 100%;
	}
	
	.scrollItem{
	  display: inline-block;
	  width: 200rpx;
	  margin-right: 20rpx;
	}
	
	.scrollItem image{
	  width: 200rpx;
	  height: 200rpx;
	  border-radius: 10rpx;
	}
	
	.scrollItem text{
	  font-size: 26rpx;
	  /* 单行文本溢出隐藏 省略号代替 */
	  /* display: block;
	  white-space: nowrap;
	  overflow: hidden;
	  text-overflow: ellipsis; */
	
	   /* 多行文本溢出隐藏 省略号代替 */
	   overflow: hidden;
	   text-overflow: ellipsis;
	   display: -webkit-box;
	   -webkit-box-orient: vertical; /* 设置对齐模式 */
	   -webkit-line-clamp: 2; /* 设置多行的行数 */
	}
	
	/* 排行榜 */
	.topList {
	  padding: 20rpx;
	}
	
	.topListSwiper {
	  height: 400rpx;
	}
	
	.swiperItem {
	  width: 96%;
	  background: #fbfbfb;
	}
	
	.swiperItem .title{
	  font-size: 30rpx;
	  line-height: 80rpx;
	}
	
	.musicItem {
	  /* 当一个元素设置为flex， 其子元素会自动成为block元素 */
	  display: flex;
	  margin-bottom: 20rpx  ;
	}
	
	.musicItem image{
	  width: 100rpx;
	  height: 100rpx;
	  border-radius: 6rpx;
	} 
	
	.musicItem .count{
	  width: 100rpx;
	  height: 100rpx;
	  text-align: center;
	  line-height: 100rpx;
	}
	
	.musicItem .musicName{
	  height: 100rpx;
	  line-height: 100rpx;
	  max-width: 400rpx;
	  white-space: nowrap;
	  overflow: hidden;
	  text-overflow: ellipsis;
	}
</style>
