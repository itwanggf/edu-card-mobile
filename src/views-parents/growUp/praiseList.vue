<template>
	<div class="pub">
		<van-nav-bar title="表扬榜" left-arrow @click-left="$router.go(-1)" @click-right="dateShow = true" fixed>
			<div class="date" slot="right">
				<span>{{filter.date}}</span><i></i>

			</div>
		</van-nav-bar>
		<div style="height: 46px;background: #f0f0f0;"></div>
		<div class="wrap">
			<div class="tabC">
				<div class="tabC-left" :class="activeT === 'left' ? 'activeT' : ''" @click="activeT = 'left'">
					<span>今日表扬</span>
				</div>
				<div class="tabC-right" :class="activeT === 'right' ? 'activeT' : ''" @click="activeT = 'right'">
					<span>历史表扬</span>
				</div>
			</div>
			<div class="list" v-if="activeT === 'left'" key="list1">
				<div class="message-item" v-for="(item, index) in list" :key="index">
					<div class="bg">
						<img :src="item.cardBackUrl" />
						<div class="tou"><img :src="item.src" v-if="item.src" /></div>
						<div class="name">{{item.stuName}}</div>
						<div class="cap">{{item.cardName}}</div>
					</div>
					<div class="pOrTime">
						<span>{{item.teacherName}}</span>
						<span>{{item.inputTime}}</span>
					</div>
				</div>
			</div>
			<div class="list" v-if="activeT === 'right'" key="list2">
				<div class="message-item" v-for="(item, index) in list1" :key="index">
					<div class="bg">
						<img :src="item.cardBackUrl" />
						<div class="tou"><img :src="item.src" v-if="item.src" /></div>
						<div class="name">{{item.stuName}}</div>
						<div class="cap">{{item.cardName}}</div>
					</div>
					<div class="pOrTime">
						<span>{{item.teacherName}}</span>
						<span>{{item.inputTime}}</span>
					</div>
				</div>
			</div>

		</div>


		<van-popup v-model="dateShow" position="bottom">
			<van-datetime-picker v-model="currentDate" type="date" @cancel="() => {this.filter.date = '选择日期';dateShow = false;activeT === 'right' && getData1()}" @confirm="(v) => {filter.date = $moment(v).format('YYYY-MM-DD'); dateShow = false;activeT === 'right' && getData1()}" />
		</van-popup>

	</div>
</template>

<script>
	import praiseApi from '../../assets/api/parents/praise.js'
	export default {
		data() {
			return {
				dateShow: false,
				activeT: 'left',
				currentDate: new Date(),
				list: [],
				list1: [],
				filter: {
					date: '选择日期'
				},
				loading: false,
				finished: false,
				currentPage: 1,
				pageSize: 15,
				total: 0,
				classInfo: {}
			}
		},
		components: {

		},
		created() {
			if (sessionStorage.getItem('class')) {
				this.classInfo = JSON.parse(sessionStorage.getItem('class'))
			}

			this.getData()
		},
		methods: {
			getData() {
				let par = {
					date: this.$moment(new Date()).format('YYYY-MM-DD')
				}
				this.$http.rq({
					url: praiseApi.praises,
					data: par
				}).then(res => {
					if (res.code === 200) {
						this.list = res.data
					} else {
						this.list = []
						this.$toast(res.message)
					}
				})
			},
			getData1() {
				let par = {
					date: this.filter.date === '选择日期' ? '' : this.filter.date
				}
				this.$http.rq({
					url: praiseApi.praises,
					data: par
				}).then(res => {
					if (res.code === 200) {
						this.list1 = res.data
					} else {
						this.list1 = []
						this.$toast(res.message)
					}
				})
			}
		},
		watch: {
			activeT(val){
				if(val === 'left'){
					this.getData()
				}else{
					this.getData1()
				}
			}
		},
		computed: {

		}

	}
</script>

<style lang="less" scoped>
	.pub {
		background: #f0f0f0;
		height: 100%;
		user-select: none;

		.date {
			width: 100%;
			height: 100%;
			display: flex;
			align-items: center;
			justify-content: center;

			&>i {
				display: block;
				width: 15.5px;
				height: 14.5px;
				background: url(../../assets/img/date.png) no-repeat;
				background-size: 100% 100%;
				margin-left: 5px;
			}

			span {
				font-size: 15px;
				color: #434964;
			}
		}

		.wrap {
			background: #f0f0f0;
			.tabC{
				margin: 10px 12px;
				height: 31.5px;
				display: flex;
				&>div{
					flex: 1;
					display: flex;
					align-items: center;
					justify-content: center;
					background-color: #ffffff;
					
					font-size: 15px;
					color: #c2c2c2;
				}
				.tabC-left{
					border-radius: 16px 0px 0px 16px;
					
				}
				.tabC-right{
					border-radius: 0 16px 16px 0;
				}
				.activeT{
					background-color: #4091fd;
					color: #ffffff;
				}
			}
		}

		.by {
			position: fixed;
			bottom: 84px;
			right: 15px;
			background: #d1eafc;
			width: 61px;
			height: 61px;
			border-radius: 50%;
			display: flex;
			align-items: center;
			justify-content: center;

			&>span {
				width: 52px;
				height: 52px;
				border-radius: 50%;
				display: flex;
				align-items: center;
				justify-content: center;
				background-color: #ffffff;
				box-shadow: 0px 0px 38px 0px rgba(20, 150, 239, 0.64);
				font-size: 15px;
				letter-spacing: 1px;
				color: #1496ef;
			}
		}

		.message-item {
			margin: 10px 12px;
			height: 170px;
			background-color: #ffffff;
			border-radius: 10px;
			box-shadow: 0px 0px 8px 1px rgba(109, 109, 109, 0.11);

			.bg {
				width: 100%;
				height: 120px;
				box-shadow: 0px 6px 13px 1px rgba(23, 106, 180, 0.2);
				border-radius: 10px;
				position: relative;

				&>img {
					display: inline-block;
					max-width: 100%;
					max-height: 100%;
				}

				.tou {
					position: absolute;
					width: 50px;
					height: 50px;
					border-radius: 50%;
					position: absolute;
					top: 18px;
					left: 28px;

					&>img {
						width: 100%;
						height: 100%;
						display: block;
						border-radius: 50%;
					}
				}

				.name {
					font-size: 18px;
					letter-spacing: 1px;
					color: #ffffff;
					position: absolute;
					top: 34px;
					left: 82.5px;
				}

				.cap {
					font-size: 27px;
					letter-spacing: 1px;
					color: #ffffff;
					position: absolute;
					top: 70px;
					left: 30px;
				}
			}

			.pOrTime {
				width: 100%;
				height: 50px;
				display: flex;
				justify-content: space-between;
				align-items: center;
				padding-left: 21px;
				padding-right: 16px;

				&>span:nth-child(1) {
					height: 14px;
					font-size: 15px;
					line-height: 20px;
					letter-spacing: 2px;
					color: #434964;
				}

				&>span:nth-child(2) {
					height: 10px;
					font-size: 12px;
					line-height: 20px;
					letter-spacing: 1px;
					color: #b2b4bf;
				}

			}
		}
	}
</style>
