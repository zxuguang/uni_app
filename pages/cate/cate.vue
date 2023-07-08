<template>
	<view>
		<view class="scroll-view-container">

			<scroll-view  scroll-y="true" :style="{height:wh +'px'}" class="left-scroll-view">
			<block v-for="(item,i) in cateList" :key="i">
				<view :class="['left-scroll-view-item', i===active ? 'active' : '']" @click="activeChange(i)">{{item.cat_name}}</view>
			</block>
			</scroll-view>

			<scroll-view scroll-y="true" :style="{height:wh +'px'}" class="right-scroll-view" :scroll-top="scrollTop">
			<view class="cate-lv2" v-for="(item2,i2) in cateList2">
				<view class="cate-lv2-tittle">/{{item2.cat_name}}/</view>
				<view class="cate-lv3-list">
					<view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
						<image :src="item3.cat_icon"></image>
						<text>{{item3.cat_name}}</text>
					</view>
				</view>
			</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh: 0,
				cateList :[],
				active:0,
				cateList2 :[],
				scrollTop : 0
			};
		},
		onLoad(){
			const sysInfo = uni.getSystemInfoSync()
			this.wh = sysInfo.windowHeight
			this.getCateList()
		},
		methods:{
			 async getCateList(){
				const {data:res}= await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/categories')
				if(res.meta.status!=200) return uni.$showMsg()
				this.cateList = res.message
				this.cateList2 =  res.message[0].children
			},
			activeChange(i){
				this.active = i
				this.cateList2 = this.cateList[i].children
				this.scrollTop = this.scrollTop ===  0? 1 : 0
			},
			gotoGoodsList(item){
				uni.navigateTo({
					url : '/subpkg/goods_list/goods_list?cid=' + item.cat_id
				})
			}
		}
	}
</script>

<style lang="scss">
.scroll-view-container{
	display: flex;
	.left-scroll-view{
		width: 120px;
	}
	.left-scroll-view-item{
		background-color: #aaa;
		line-height: 60px;
		text-align: center;
		font-size: 12px;
		&.active{
			background-color: #FFFFFF;
			position: relative;
			// 伪元素实现
			&::before{
				content: '';
				display: block;
				width: 3px;
				height: 30px;
				background-color: #C00000;
				position: absolute;
				top: 25%;
				left: 0;
				;
			}
		}
	}
	.cate-lv2-tittle{
		text-align: center;
		//zi hao 
		font-size: 12px;
		//jia cu
		font-weight: bold;
		//shang xia 15 px zuoyou 0
		padding: 15px 0;
	}
	.cate-lv3-list{

		display: flex;
		//zidonghuanhang
		flex-wrap: wrap;
		.cate-lv3-item{
		width: 33.33%;
		display: flex;
		flex-direction: column;
		justify-content:center;
		align-items: center;
			image{
				width: 60px;
				height: 60px;
			}
		}
	}
}
</style>
