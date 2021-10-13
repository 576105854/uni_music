<template>
	<view class="header">
	  <text class="title">{{title}}</text>
	  <view>
	    <text>{{nav}}</text>
	    <text class="more">查看更多</text>
	  </view>
	</view>
</template>

<script>
	export default {
		props: {
			// 检测类型 + 其他验证
			title: {
				type: String,
				default: '我是title默认值'
			},
			nav:{
			      type: String,
			      value: '我是nav默认值'
			}
		}
	}
</script>

<style>
	.header{
	  padding-bottom: 20rpx;
	}
	
	.header .title{
	  font-size: 32rpx;
	  line-height: 80rpx;
	  color: #666;
	}
	
	.header .more{
	  float: right;
	  border: 1rpx solid #333;
	  padding: 10repx 20rpx;
	  font-size: 24rpx;
	  border-radius: 30rpx;
	}
</style>
