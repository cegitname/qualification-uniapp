<template>
	<view>
		<cu-custom bgColor="bg-gradual-blue">
			<block slot="content">资质计算</block>
		</cu-custom>
		<view class="banner">
			<image src="../../static/banner.png" class="bannerImg"></image>
		</view>
		<view class="container">
			<view class="listItem">
				<view class="itemname">选择资质 :</view>
				<view class="itemValue">
					<picker  @change="bindPickerChange" :value="index" :range="qualificationList">
						<view class="uni-input">{{qualificationList[index]}}</view>
					</picker>
				</view>
			</view>
			<button type="primary" class="add" @click="clearList">清空</button>
			<view class="qlist">
				<view v-for="(item,i) in qList" :key="i" >
					{{item}}
				</view>
			</view>
			<button type="primary" class="add" @click="jump">完成</button>
		</view>
	</view>
</template>

<script>
	import data from '@/common/data.js'
	
	export default {
		components:{
		},
		data() {
			return {
				qualificationList:data.qualification,
				index: 0,
				qList:[],//资质列表
				bgColorList:[
				    {color:"#f37402",scale:"0%"},
				    {color:"#00f",scale:"100%"}
				]
			}
		},
		onShow:function(){
			uni.setStorage({
			    key: 'data',
			    data:data.data,
			    success: function () {
			        console.log('setStorage success');
			    }
			});
		},
		methods: {
			bindPickerChange: function(e) {
				this.index = e.target.value
				if(this.index==0){
					return
				}
				this.qList.push(data.qualification[e.target.value])
				this.qList=this.unique5(this.qList)
			},
			jump:function () {
				if(this.qList.length==0){
					return
				}
				uni.navigateTo({
					url:'../succ?qlist='+this.qList
				})
			},
			unique5:function (arr){
			  var x = new Set(arr);
			 return [...x];
			},
			clearList:function () {
				this.qList=[]
			}
		},
			
	}
</script>

<style>
	.container{
		padding: 40rpx;
	}
	.listItem{
		display: flex;
		width: 100%;
		padding-bottom: 20rpx;
		border-bottom: 1px solid #999999;
	}
	.banner{
		width: 100vw;
		height: 270px;
		position: relative;
	}
	.bannerImg{
		width: 100%;
		height: 100%;
	}
	.itemname{
		width:30%;
	}
	.itemValue{
		flex: 1;
	}
	.add{
		margin: 20rpx;
	}
	
	.qlist{
		width: 100%;
		height:210rpx;
		overflow: auto;
		border: 1px solid #999999;
		margin-top: 20rpx;
		padding: 20rpx;
		box-sizing: border-box;
	}
</style>
