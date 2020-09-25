<template>
	<view class="bg-gradual-blue">
	
		<scroll-view scroll-y class="DrawerPage " :class="modalName=='viewModal'?'show':''">
			<cu-custom bgColor="bg-gradual-blue" >
				<block slot="content">资质计算</block>
			</cu-custom>	
		<image src="../../static/banner.png" class="banner" ></image>
		
		<view class="cu-list menu card-menu margin-bottom-xl shadow-lg " style="position: relative;" v-if="qList.length==0">
		   <view class="cu-item "  v-for="(item,index) in tip" :key="index">
		    <view class="content">
		     <text class="text-grey " style="margin-right: 5px;">{{index+1}}.</text>
		     <text class="text-grey">{{item}}</text>
		    </view>
		   </view>
		   <!-- <image src="../../static/tipbg.jpeg" class="bg-tip"></image> -->
	    </view>
		<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg mylist" v-else>
			<view class="cu-item " v-for="(item,index) in qList" :key="index">
				<view class="content">
					<text class="cuIcon-github text-grey"></text>
					<text class="text-grey">{{item}}</text>
					<text class="lg text-gray cuIcon-deletefill listicon" @click="deleteItem(item)"></text>
				</view>
			</view>
		</view>	
		
	<!-- 	<uni-fab
		            :pattern="pattern"
		            :content="content"
					vertical="bottom"
		            @trigger="trigger"
		        ></uni-fab> -->
		
		<view class='text-center ksjs bg-white'  >
			<view class='cu-btn bg-blue  lg block shadow radius margin-xl' @tap="showModal" data-target="viewModal">
				选择资质
			</view>
			<view class='cu-btn  bg-blue lg block shadow radius margin-xl' @tap="jump" data-target="viewModal">
				开始计算
			</view>
		</view>
		
		</scroll-view>
		<view class="DrawerClose" :class="modalName=='viewModal'?'show':''" @tap="hideModal">
			<text class="cuIcon-pullright"></text>
		</view>
		<scroll-view scroll-y class="DrawerWindow bg-cyan light" :class="modalName=='viewModal'?'show':''" style="background: linear-gradient(45deg, #0081ff, #1cbbb4);">
			<view class="mySwitchBox">
				<view class="mySwitchBoxinner ">
					<text class="text-white margin-right">全选 </text>
					<switch class='red ' @change="SwitchB" :class="switchB?'checked':''" :checked="switchB?true:false"  color="#e54d42"></switch>
					<text class="text-white margin-left">全不选 </text>
				</view>
			</view>
			<checkbox-group @change="CheckboxChange">
				<view class="cu-list menu card-menu  shadow-lg" v-for="(item,index) in list" :key="index">
					<view class="cu-item " >
						<view class="content">
							<text class="cuIcon-github text-grey"></text> 
							<text class="text-grey">{{item.value}}</text>
							<checkbox :value="item.value" :checked="item.checked"  class="myckbox"/>
						</view>
					</view>
				</view>
			</checkbox-group>
		</scroll-view>
	
	</view>
</template>

<script>
	import data from '@/common/data.js'
	import uniFab from '@/components/uni-fab/uni-fab.vue';
	export default {
		components:{
			uniFab
		},
		data() {
			return {
				modalName:null,
				list:data.checkbox,
				switchB: false,
				qList:[],//资质列表
				horizontal:'right',
				content:[
					{
						iconPath:'../../static/home.png',
						selectedIconPath:"../../static/homeactive.png",
						text:"测试",
						active:false
					},
					{
						iconPath:'../../static/home.png',
						selectedIconPath:"../../static/homeactive.png",
						text:"测试",
						active:false
					},
					{
						iconPath:'../../static/home.png',
						selectedIconPath:"../../static/homeactive.png",
						text:"测试",
						active:false
					}
				],
				tip:[
				     
				     "选择资质,点开始计算获得结果",
				     "匹配结果单价可更改,自动计算总价",
				     "结果仅作为参考,有误请联系作者",
					 "个人作品,免费使用"
				    ]
			};
		},
		onShow() {
			uni.setStorage({
			    key: 'data',
			    data:data.data,
			    success: function () {
			        console.log('setStorage success');
			    }
			});
		},
		methods: {
			showModal(e) {
				this.modalName = e.currentTarget.dataset.target
			},
			hideModal(e) {
				this.modalName = null
			},
			tabSelect(e) {
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			},
			sortobj:function(arr){
				console.log("执行了sortobj")
							arr.sort((a,b)=>{
								return a.index-b.index
							})
				return arr
			},
			CheckboxChange(e) {
				var arrlist=[]
				this.qList=e.detail.value
				this.list.forEach((val,i,arr)=>{
					for(var item of e.detail.value){
						if(val.value==item){
							arrlist.push(val)
						}
					}
				})
				arrlist=this.sortobj(arrlist)
				var newArr = []
				for(var aitem of arrlist){
					newArr.push(aitem.value)
				}
				this.qList=newArr
				
				
			},
			SwitchB(e) {
				this.switchB = e.detail.value
				this.list.forEach((val,i,arr)=>{
					val.checked=!val.checked
				})
			},
			deleteItem(e){
				this.qList.forEach((val,i,arr)=>{
					if(val==e){
						arr.splice(i,1)
					}
				})
			},
			jump:function () {
				if(this.qList.length==0){
					uni.showToast({
						title:'先选择资质后再计算',
						icon:'none'
					})
					return
				}
				uni.navigateTo({
					url:'../succ?qlist='+this.qList
				})
			},
		},
	}
</script>

<style>
	.banner{
		width: 100%;
		height: 400rpx;
	}
	.ksjs{
		width: 100%;
		position: fixed;
		bottom: 0;
		z-index: 99;
	}
	.mylist{
		margin-bottom: 300rpx;
	}
	page {
		background:#FFFFFF;
		width: 100vw;
		overflow: hidden;
	}

	.DrawerPage {
		position: fixed;
		width: 100vw;
		height: 100vh;
		left: 0vw;
		background: #FFFFFF;
		transition: all 0.4s;
	}

	.DrawerPage.show {
		transform: scale(0.9, 0.9);
		left: 85vw;
		box-shadow: 0 0 60upx rgba(0, 0, 0, 0.2);
		transform-origin: 0;
	}

	.DrawerWindow {
		position: absolute;
		width: 85vw;
		height: 100vh;
		left: 0;
		top: 0;
		transform: scale(0.9, 0.9) translateX(-100%);
		opacity: 0;
		pointer-events: none;
		transition: all 0.4s;
		padding: 150upx 0 90upx 0;
	}

	.DrawerWindow.show {
		transform: scale(1, 1) translateX(0%);
		opacity: 1;
		pointer-events: all;
	}

	.DrawerClose {
		position: absolute;
		width: 40vw;
		height: 100vh;
		right: 0;
		top: 0;
		color: transparent;
		padding-bottom: 30upx;
		display: flex;
		align-items: flex-end;
		justify-content: center;
		background-image: linear-gradient(90deg, rgba(0, 0, 0, 0.01), rgba(0, 0, 0, 0.6));
		letter-spacing: 5px;
		font-size: 50upx;
		opacity: 0;
		pointer-events: none;
		transition: all 0.4s;
	}

	.DrawerClose.show {
		opacity: 1;
		pointer-events: all;
		width: 15vw;
		color: #fff;
	}

	.DrawerPage .cu-bar.tabbar .action button.cuIcon {
		width: 64upx;
		height: 64upx;
		line-height: 64upx;
		margin: 0;
		display: inline-block;
	}

	.DrawerPage .cu-bar.tabbar .action .cu-avatar {
		margin: 0;
	}

	.DrawerPage .nav {
		flex: 1;
	}

	.DrawerPage .nav .cu-item.cur {
		border-bottom: 0;
		position: relative;
	}

	.DrawerPage .nav .cu-item.cur::after {
		content: "";
		width: 10upx;
		height: 10upx;
		background-color: currentColor;
		position: absolute;
		bottom: 10upx;
		border-radius: 10upx;
		left: 0;
		right: 0;
		margin: auto;
	}

	.DrawerPage .cu-bar.tabbar .action {
		flex: initial;
	}
	.content{
		position: relative;
		
	}
	.listicon{
		position: absolute;
		right: 0;
	}
	.myckbox{
		position: absolute;
		right: 0;
	}
	.mySwitchBox{
		width: 100%;
		height: 100rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		top:50rpx;
	}
	.mySwitchBoxinner{
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.mytip{
		height: 300rpx;
		
	}
	.uni-fab--leftBottom{
		bottom: 40% !important;
	}
	.bg-tip{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}
</style>
