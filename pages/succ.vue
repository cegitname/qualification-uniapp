<template>
	<view >
		<cu-custom bgColor="bg-gradual-blue" :isBack="true">
		    <block slot="content">资质计算</block>
		</cu-custom>
		
		<view class="container">
			<view class="title">资质列表</view>
			<view class="item" >
				<view v-for="(item,i) in qlist" :key="i" class="qlist" >
					<view>资质名称 : {{item.zjName}} </view>
					<view>等级 ： {{item.level}}</view>
					<view>净资质 ：{{item.qualification}}</view>
				</view>
			</view>
			
			<view class="title">人员配置</view>
			
			<view class="item">
				
				<view>
					<view class="title">建造师专业</view>
					<view style="color:#DD524D;font-weight: bold;">总人数：{{zjs.nums}} </view>
					<view class="listItem" v-for="(item,i) in zjs.typelist" :key="i">
							<view class="zyInfo">
								<view class="infoL">
									<text class="itemname">
										{{item.name}}：
									</text>
									<text style="color:#DD524D;" >
										{{item.num}}
									</text>
								</view>
								<view class="infoR">
									<view class="infoR-pt">
									价格 ：
									</view>
									<input type="number" :value="item.p" @blur="changePrice" :ref="item.name" class="price"/>
								</view>
							</view>
					</view>
				</view>
				
				<view>
					<view class="title">工程师专业</view>
					<view style="color:#DD524D;font-weight: bold;">总人数：{{gcs.nums}}</view>
					<view class="listItem" v-for="(item,i) in gcs.typelist" :key="i" >
						<view class="zyInfo">
							<view class="infoL">
								<text class="itemname">
									{{item.name}}：
								</text>
								<text style="color:#DD524D;">
									{{item.num}}
								</text>
							</view>
							<view class="infoR">
								<view class="infoR-pt">
								价格 ：
								</view>
								<input type="number" :value="item.p" @blur="changePrice" :ref="item.name" class="price"/>
							</view>
						</view>
					</view>
				</view>
				
				<view>
					<view class="title">技工专业</view>
					<view style="color:#DD524D;font-weight: bold;">总人数：{{jg.nums}}</view>
					<view class="listItem" v-for="(item,i) in jg.typelist" :key="i">
						<view class="zyInfo">
							<view class="infoL">
								<text class="itemname">
									{{item.name}}：
								</text>
								<text style="color:#DD524D;" >
									{{item.num}}
								</text>
							</view>
							<view class="infoR">
								<view class="infoR-pt">
								价格 ：
								</view>
								<input type="number" :value="item.price" @blur="changePrice" :ref="item.name" class="price"/>
							</view>
						</view>
					</view>
				</view>
				
				<view class="title">社保</view>
				<view class="listItem">
				   <view>
					   <text style="color:#DD524D;font-weight: bold;">总人数:{{sbNums}}</text>
				   </view>
				   <view class="zyInfo">
				   	<view class="infoR">
				   		<view class="infoR-pt">
				   		社保单价 ：
				   		</view>
				   		<input type="number" :value="sbPrice" class="price"  @blur="inputSbPrice"/>
				   	</view>
				   </view>
				</view>
				
				<view class="title">代办费</view>
				<view class="zyInfo">
					<view class="infoR">
						<view class="infoR-pt">
						输入代办费：
						</view>
						<input type="number"  class="price" @blur="inputdbf"/>
					</view>
				</view>
				
				<view>
					<view class="title">总价</view>
					<view  class="price">{{totalprice}}</view>
				</view>
				
				
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				dataSource:"",
			   qlist:[],
			   zjs:{},
			   gcs:{},
			   jg:{},
			   sbNums:0,
			   sbPrice:1670,
			   dbPrice:0,
			   totalprice:0,
			   namelistarr:[],
			}
		},
		onShow:async function (){
			var datalist=[]
			const value =await uni.getStorageSync('data');
			if (value) {
				datalist=value
			}
			try {
			    uni.removeStorageSync('data');
			} catch (e) {
			   console.log(e)
			}
			var namelist=this.namelistarr
			var zjslist=[];
			var gcslist=[];
			var jglist = [];
			var jdarr=['机电工程总承包',
					   '电力工程总承包',
					   '输变电工程专业承包',
					   '城市及道路照明工程专业承包',
					   '机电安装专业承包',
					   '消防设施工程专业承包']
			var jszty=['环保工程专业承包','地基基础专业承包']
			var i =-1// 查看是否有机电专业  建筑变4+1
			var szgyArr=[]
			var szgyArrNums=0
			var szgyGcslist=[]
			
			var gjgArr=[]
			var gjgArrNums=0
			var gjgGcslist=[]
			
			var zxzsArr=[]
			var zxzsArrNums=0
			var zxzsGcslit=[]
			
			var mbjsjArr=[]
			var mbjsjArrNums=0
			var mbjsjGcslist=[]
			for(var q1 of namelist){
				var ii=jdarr.indexOf(q1)
				if(ii>=0){
					i=ii
				}
			}
			for(var q of namelist){
				for(var item of datalist ){
					if(q===item.zjName){
						console.log(item)
						this.qlist.push(item)
						/* 建造师 */
						if(item.zjName=="建筑工程总承包"&&i!==-1){
							item.staffing.constructors.type[0].num=4
						}
						if(item.zjName=="建筑工程总承包"&&i==-1){
							item.staffing.constructors.type[0].num=5
						}
						zjslist=zjslist.concat(item.staffing.constructors.type)
						/* 工程师 */
						if(item.zjName==="防水防腐保温工程专业承包"&&namelist.length==1){
							console.log(11111111111111111111)
							item.staffing.engineer.type=[
								{
									name:'结构',
									num:2,
									p:4000
								},
								{
									name:'材料/化工',
									num:1,
									p:4000
								},
							]
						}
						if(item.zjName==="市政公用工程总承包"){
							szgyArr = item.staffing.engineer.type
							szgyArrNums=item.staffing.engineer.nums
						}else{
							szgyGcslist=szgyGcslist.concat(item.staffing.engineer.type)	
						} 
						
						if(item.zjName==="钢结构工程专业承包"){
							gjgArr = item.staffing.engineer.type
							gjgArrNums=item.staffing.engineer.nums
						}else {
							gjgGcslist=gjgGcslist.concat(item.staffing.engineer.type)	
						} 
						
						if(item.zjName==="装修装饰专业承包"){
							zxzsArr = item.staffing.engineer.type
							zxzsArrNums=item.staffing.engineer.nums
						}else {
							zxzsGcslit=zxzsGcslit.concat(item.staffing.engineer.type)
						}
						
						if(item.zjName==="模板脚手架"){
							mbjsjArr = item.staffing.engineer.type
							mbjsjArrNums=item.staffing.engineer.nums
						}else{
							mbjsjGcslist=mbjsjGcslist.concat(item.staffing.engineer.type)
						}
						gcslist=gcslist.concat(item.staffing.engineer.type)
						// console.log(gcslist)
						/* 技工 */
						jglist=jglist.concat(item.staffing.mechanic.type)
					}
				}
			}
			this.zjs = await this.getDetailNum(zjslist)
		
			if(szgyArr.length>0){
				console.log('市政公用工程总承包')
				gcslist=await this.getBqqGcs(szgyGcslist,szgyArr,szgyArrNums)
			}
			if(gjgArr.length>0){
				console.log('钢结构工程专业承包')
				gcslist=await this.getBqqGcs(gjgGcslist,gjgArr,gjgArrNums)
			}
			if(zxzsArr.length>0){
				console.log('装修装饰专业承包')
				console.log(zxzsGcslit)
				gcslist=await this.getBqqGcs(zxzsGcslit,zxzsArr,zxzsArrNums)
			}
			if(mbjsjArr.length>0){
				console.log('模板脚手架')
				gcslist=await this.getBqqGcs(mbjsjGcslist,mbjsjArr,mbjsjArrNums)
			}
			// return
			this.gcs = await this.getDetailNum(gcslist)
			this.gcs = await this.removeTy(this.gcs)
			this.jg = await this.getDetailNum(jglist)
			this.calculateTotalPrice(this.zjs,this.gcs,this.jg,this.dbPrice)
			
		},
		methods: {
			removeTy:function(gcs){
				console.log("执行了removeTy")
				var arr=gcs.typelist
				var zdh = -1;
				var zdhkz = -1;
				var jxsb = -1;
				var jx = -1;
				var nums=0
				arr.forEach((item,i,array)=>{
					if(item.name=="自动化控制"){
						zdhkz=i
					}
					if(item.name=="自动化"){
						zdh=i
					}
					if(item.name=="机械设备"){
						jxsb=i
					}
					if(item.name=="机械"){
						jx=i
					}
					if(jxsb!==-1&&jx!==-1){
						array.splice(jx,1)
					}
					if(zdhkz!==-1&&zdh!==-1){
						array.splice(zdh,1)
					}
				})
				for(var item2 of arr){
					nums+=item2.num
				}
				gcs.typelist=arr
				gcs.nums=nums
				return gcs 
			},
			setTy:function (typelist,tname){
				console.log("执行了setTy")
					var tynum=0
					var tynum1=0
					for(var item0 of typelist){
						tynum+=item0.num
						if(item0.name==tname){
							tynum1=item0.num
						}
						
					}
					var reduceVal = tynum-tynum1// 除了建造师的相加和
					if(reduceVal<tynum1){
						var val0 = tynum1-reduceVal
						this.sortobj(typelist)
						typelist[0].num+=val0
					}else{
						typelist.forEach((item,i,arr)=>{
							if(item.name==tname){
								arr.splice(i,1)
							}
						})
					}
					
					return typelist
			},
			getBqqGcs:function(gcslist,bqqArr,nums){
			/* 
				gcslist 合并后的工程师列表
				bqqArr  当前对象的工程师列表
				nums    当前对象的工程师总人数
			 */
				console.log("执行了getBqqGcs")
				console.log(gcslist)
				console.log(bqqArr)
				console.log(nums)
				gcslist=this.getUniarr(gcslist) // 去重
				var gcsaddnum=0
				for(var gcslistitem of gcslist){ //合并后的工程师
					for(var szgyArrItem of bqqArr){ //该对象的工程师
						if(gcslistitem.name===szgyArrItem.name){//如果合并后里面的工程师 和 当前对象工程师相等
							gcsaddnum+=gcslistitem.num //数量等于 合并后的 这一项的人数
						}
					}
				}
				
				if(gcsaddnum===0){
					gcslist=gcslist.concat(bqqArr)
				}else if(gcsaddnum<=nums){
					var szReduceRes=nums-gcsaddnum
					this.sortobj(gcslist)
					gcslist[0].num+=szReduceRes
				}
				return gcslist
			},
			getTotalP:function (list) {
				console.log("执行了getTotalP")
							var resP=0;
							var nums=0;
							for(var listItem of list.typelist){
								var x = (listItem.p)*(listItem.num)
								nums+=listItem.num
								resP+=x
							}
							return {resP,nums}
			},
			sortobj:function(arr){
				console.log("执行了sortobj")
							arr.sort((a,b)=>{
								return a.p-b.p
							})
				return arr
			},
			getUniarr:function (data) {
				console.log("执行了getUniarr")
				var arr = [];
			   for(var i = 0 ; i < data.length; i++){
				   var ts = false;
				   for(var j = 0 ; j < arr.length ; j++){
					   if(data[i].name == arr[j].name){
						   if(data[i].num >arr[j].num){
							   arr[j].num = data[i].num;
						   }
						   ts = true;
					   }
				   }
				   if(!ts){
					   arr[j] = data[i];
				   }
			   }
			   return arr
			},
			inputSbPrice: function (e){
				console.log("执行了inputSbPrice")
				this.sbPrice=e.detail.value
				this.calculateTotalPrice(this.zjs,this.gcs,this.jg,this.dbPrice)
			},
			changePrice:function (e) {
				console.log("执行了changePrice")
				var type=e.currentTarget.dataset.ref
				var newPrice=e.detail.value
				for(var jzsitem of this.zjs.typelist){
					if(type==jzsitem.name){
						jzsitem.p=newPrice
					}
				}
				for(var gcsitem of this.gcs.typelist){
					if(type==gcsitem.name){
						gcsitem.p=newPrice
					}
				}
				for(var jgitem of this.jg.typelist){
					if(type==jgitem.name){
						jgitem.price=newPrice
					}
				}
				this.calculateTotalPrice(this.zjs,this.gcs,this.jg,this.dbPrice)
			},
			inputdbf:function(e){
				this.dbPrice=Number(e.detail.value)
				this.calculateTotalPrice(this.zjs,this.gcs,this.jg,this.dbPrice)
			},
			getDetailNum:async function(typelist){
				console.log("执行了getDetailNum")
				// console.log("getDetailNum")
				// 专业去重
				var a1,b1;
				var av1,av2,bv1,bv2; 
				// 判断如果有建总和机电 那就让建造工程人数为4
				var nums =0
				// 如果机电工程和一二级机电
				var jdgcNumbers =0; //机电工程人数
				var jdgcIndex=0; //机电工程下表
				var yijiJdNumbers = 0;//一级
				var erjiJdNumbers = 0;//二级
				var jdshenyuNumber = 0; //剩余人数
				typelist=this.getUniarr(typelist)//去重
				if(typelist.length>1){
					typelist = await this.setTy(typelist,'建造师')
					typelist = await this.setTy(typelist,'工程序列中级')
				}
				typelist.forEach((value,index,array)=>{
					if(value.name =='自动化控制'){
						av1=true
					}  
					if(value.name =='自动化'){
						av2=true
					}  
					if(value.name =='机械设备'){
						bv1=true
					}  
					if(value.name =='机械'){
						bv2=true
					}  
					if(av1&&av2){a1=true}
					if(bv1&&bv2){b1=true}
					if(a1&&value.name==='自动化'){
						array.splice(index,1)
					}
					if(b1&&value.name==='机械'){
						array.splice(index,1)
					}
					if(value.name=="机电工程"){
						jdgcNumbers=value.num
						jdgcIndex=index
					}
					if(value.name=="一级机电"){
						yijiJdNumbers=value.num
					}
					if(value.name=="二级机电"){
						erjiJdNumbers=value.num
					}
					if(jdgcNumbers>0&&yijiJdNumbers>0){
						jdshenyuNumber=jdgcNumbers-yijiJdNumbers
						if(jdshenyuNumber<=0){
							array.splice(jdgcIndex,1)
						}else if(erjiJdNumbers>0){
							jdshenyuNumber=jdshenyuNumber-erjiJdNumbers
							if(jdshenyuNumber<=0){
								array.splice(jdgcIndex,1)
							}else{
								array[jdgcIndex].num=jdshenyuNumber
							}
						}
					}
					
				})
				for(var item of typelist){
					if(item){
						nums+=item.num
					}
				}
				return {
					typelist,
					nums
				}
			},
			calculateTotalPrice:function (zjs,gcs,jg,dbPrice) {
				var a= this.getTotalP(zjs);  //建造师
				
				var b= this.getTotalP(gcs); //工程师
				
				var jgPrice=jg.typelist[0].price; //技工单价
				var jgNums=jg.typelist[0].num //技工人数
				var c=jgNums*jgPrice;    //技工总价
				
				this.sbNums=a.nums+b.nums+jgNums //总人数
				var d = this.sbNums*this.sbPrice //社保总价
				this.totalprice=a.resP+b.resP+c+ d + dbPrice//总价格
			},
		},
		onLoad:async function (e) {
			this.namelistarr = e.qlist.split(",")
		}
		
	}
</script>

<style>
.container{
		padding: 40rpx;
	}
.title {
	font-size: 32rpx;
	font-weight: bold;
	margin: 20rpx 0; 
}
.item{
	border:  1px solid #999999;
	padding: 20rpx;
	font-size: 28rpx;
}
.qlist{
	border-bottom: 1px solid #C8C7CC; 
	margin-bottom: 25rpx;
	padding-bottom: 10rpx;
}
.qlist view{
	margin-bottom: 10rpx;
}
.price{
	width: 50%;
	height: 100%;
	display: flex;
	align-items: center;
	color: #DD524D;
	display: inline-block;
}
.zyInfo{
	display: flex;
	border-bottom: 1px solid #DDDDDD;
	margin-bottom: 5px;
}
.infoL{
	width: 50%;
	display: flex;
	align-items: center;
}
.infoR{
	flex: 1;
	display: flex;
}
.infoR-pt{
	height: 100%;
	display: flex;
	align-items: center;
}
</style>
