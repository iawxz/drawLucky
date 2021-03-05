<template>
	<view class="lucky">
		<view class="title">
			<image src="../../static/event/lucky/title.png" mode="aspectFill"></image>
		</view>
		<view class="turntable">
			<view class="gift" v-for="(item,index) in giftlist" :key="index">
				<view class="giftBox">
					<image :src="item.image" mode="aspectFill" v-if="item.drawType"></image>
					<text :class="{nogift:!item.drawType}">{{item.name}}</text>
				</view>

				<view class="giftMask" v-if="index == roll"></view>
			</view>
			<view class="draw" @click="draw" v-if="!isDraw"></view>
			<view class="draw drawing" v-else></view>
		</view>
		<view class="chances">
			<text>您还有{{chance}}次机会</text>
		</view>
		<view class="button">
			<view class="btn rule" @click="maskShow(1)">
				<text>抽奖规则</text>
			</view>
			<view class="btn record" @click="maskShow(2)">
				<text>中奖记录</text>
			</view>
		</view>
		<view class="mask" v-if="isMask!=0">
			<view class="ruleBox" v-if="isMask==1">
				<view class="title">
					<image src="../../static/event/lucky/star.png" mode="aspectFill"></image>
					<text>抽奖规则</text>
					<image src="../../static/event/lucky/star.png" mode="aspectFill"></image>
				</view>
				<view class="textBox">
					<text>
						1、必须是在APP登录后才可参与抽奖<br />
						2、每人仅可抽一次<br />
						3、抽奖页面不可转发，仅限APP内抽奖<br />
					</text>
				</view>
			</view>

			<view class="recordBox" v-if="isMask==2">
				<view class="title">
					<image src="../../static/event/lucky/star.png" mode="aspectFill"></image>
					<text>中奖记录</text>
					<image src="../../static/event/lucky/star.png" mode="aspectFill"></image>
				</view>
				<view class="textBox">
					<view class="time">
						<text class="point"></text>
						<text>03月2日 14:04</text>
					</view>
					<view class="prize">
						<text>1元购全年企业法律服务套餐</text>
					</view>
				</view>
			</view>
			<view class="getGiftBox" v-if="isMask==3">
				<view class="title" v-if="gift.drawType">
					恭喜抽中<br />
					{{gift.name}}
				</view>
				<view class="title" v-else>
					很遗憾<br />
					您与奖品擦肩而过
				</view>
				<image src="../../static/event/lucky/getgift.png" mode="aspectFill" @click="getGift" v-if="gift.drawType"></image>
				<image src="../../static/event/lucky/nogift.png" mode="aspectFill" v-else></image>
			</view>
			<image class="close" src="../../static/event/lucky/close.png" mode="aspectFill" @click="maskShow(0)"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				giftlist: [{
					name: "一元购",
					drawType: true, //中奖情况，false没中奖，true中奖
					image: require('../../static/event/lucky/gift_1.png')
				}, {
					name: "10元抵用券",
					drawType: true,
					image: require('../../static/event/lucky/gift_3.png')
				}, {
					name: "200元抵用券",
					drawType: true,
					image: require('../../static/event/lucky/gift_3.png')
				}, {
					name: "谢谢惠顾",
					drawType: false,
					image: ''
				}, {
					name: "半价套餐券",
					drawType: true,
					image: require('../../static/event/lucky/gift_2.png')
				}, {
					name: "50元抵用券",
					drawType: true,
					image: require('../../static/event/lucky/gift_3.png')
				}, {
					name: "300元抵用券",
					drawType: true,
					image: require('../../static/event/lucky/gift_3.png')
				}, {
					name: "谢谢惠顾",
					drawType: false,
					image: ''
				}, ],
				chance: 1,
				isMask: 0, //0不展示，1规则，2记录，3中奖界面
				isDraw: false, //是否正在摇奖，false未摇，true摇
				roll: 0, //从0开始滚动
				result: "", //抽奖结果
				interval: '', //按时间间隔持续调用函数
				timeout: '', //定时器
				gift: {}, //获得的奖品详情
			};
		},
		methods: {
			// 开始抽奖
			draw() {
				if (this.chance > 0) {
					this.chance--
					this.isDraw = true
					clearInterval(this.interval)
					this.interval = setInterval(() => {
						this.roll++
						if (this.roll >= 8) {
							this.roll = 0
						}
					}, 200);
					// 5秒中后出随机数结果
					this.timeout = setTimeout(() => {
						this.getResult()
						this.stopDraw()
						clearTimeout(this.timeout)
					}, 5000)
				} else {
					uni.showToast({
						title: '已无抽奖机会',
						icon: 'none'
					})
				}
			},
			// 随机数获得最终结果
			getResult() {
				this.result = Math.floor(Math.random() * 8);
			},
			// 抽奖结束
			stopDraw() {
				var stop = setInterval(() => {
					if (this.roll == this.result) {
						clearInterval(this.interval)
						clearInterval(stop)
						this.isDraw = false
						this.gift = this.giftlist[this.result]
						setTimeout(() => {
							this.maskShow(3)
						}, 1000)
					}
				}, 10)
			},
			// 遮罩层展示
			maskShow(type) {
				this.isMask = type
			},
			// 获取奖品
			getGift() {
				uni.showToast({
					title: '领取成功',
					icon: 'none'
				})
			}
		},
	}
</script>

<style lang="scss">
	// 引入字体
	@font-face {
		font-family: 'jiangxizhuokai';
		src: url('~@/static/font/jiangxizhuokai.TTF');
	}

	.lucky {
		width: 100%;
		height: 100vh;
		background-image: url(../../static/event/lucky/back.png);
		background-repeat: no-repeat;
		background-position: center;
		background-size: cover;
		position: relative;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;

		.title {			
			image {
				width: 436rpx;
				height: 121rpx;
				display: block;
				margin: 0 auto 50rpx;
			}
		}

		.turntable {
			width: 650rpx;
			height: 650rpx;
			position: relative;
			margin: 0 auto;
			background-image: url(../../static/event/lucky/turntableBack.png);
			background-repeat: no-repeat;
			background-position: center;
			background-size: cover;

			.gift,
			.draw {
				width: 172rpx;
				height: 172rpx;
				overflow: hidden;
				position: absolute;
				background: linear-gradient(0deg, #FFE5E5 0%, #FFFFFF 100%);
				box-shadow: 0rpx 2rpx 8rpx 0rpx rgba(0, 0, 0, 0.31);
				border-radius: 10rpx;
			}

			.gift {
				.giftBox {
					position: relative;

					image {
						width: 172rpx;
						height: 110rpx;
					}

					text {
						display: block;
						margin-top: 12rpx;
						text-align: center;
						font-size: 26rpx;
						font-family: jiangxizhuokai;
						font-weight: 400;
						color: #323232;
					}

					.nogift {
						display: block;
						width: 61rpx;
						height: 69rpx;
						margin: 50rpx auto;
						font-size: 30rpx;
						font-family: jiangxizhuokai;
						font-weight: 400;
						color: #323232;
					}
				}
			}

			.gift:nth-child(1) {
				top: 53rpx;
				left: 56rpx;
			}

			.gift:nth-child(2) {
				top: 53rpx;
				left: 239rpx;
			}

			.gift:nth-child(3) {
				top: 53rpx;
				right: 56rpx;
			}

			.gift:nth-child(4) {
				top: 236rpx;
				right: 56rpx;
			}

			.gift:nth-child(5) {
				bottom: 59rpx;
				right: 56rpx;
			}

			.gift:nth-child(6) {
				bottom: 59rpx;
				left: 239rpx;
			}

			.gift:nth-child(7) {
				bottom: 59rpx;
				left: 56rpx;
			}

			.gift:nth-child(8) {
				top: 236rpx;
				left: 56rpx;
			}

			.draw {
				top: 236rpx;
				left: 239rpx;
				background-image: url(../../static/event/lucky/draw.png);
				background-repeat: no-repeat;
				background-position: center;
				background-size: cover;
			}

			.drawing {
				background-image: url(../../static/event/lucky/draw_ing.png);
			}

			.giftMask {
				width: 100%;
				height: 100%;
				position: absolute;
				top: 0;
				left: 0;
				background: rgba($color: #000000, $alpha: 0.2);
			}

		}

		.chances {
			width: 280rpx;
			height: 50rpx;
			margin: 0 auto;
			background: #F0352D;
			border-radius: 0rpx 0rpx 25rpx 25rpx;
			text-align: center;
			line-height: 50rpx;

			text {
				font-size: 28rpx;
				font-family: PingFang SC;
				font-weight: 800;
				letter-spacing: 5rpx;
				color: #FFFFFF;
			}
		}

		.button {
			margin-top: 80rpx;
			margin-bottom: 150rpx;
			display: flex;
			align-items: center;
			justify-content: center;

			.btn {
				width: 260rpx;
				height: 80rpx;
				background: #FFFFFF;
				box-shadow: 0rpx 4rpx 9rpx 0rpx rgba(255, 98, 60, 0.6);
				border-radius: 10rpx;
				text-align: center;
				line-height: 80rpx;

				text {
					font-size: 28rpx;
					font-family: PingFang SC;
					font-weight: 800;
				}
			}

			.rule {
				margin-right: 80rpx;
				color: #FF623C;
			}

			.record {
				background-color: #FF623C;
				color: #FFFFFF;
			}
		}

		.mask {
			width: 100%;
			height: 100vh;
			background-color: rgba($color: #000000, $alpha: 0.6);
			position: absolute;
			top: 0;
			left: 0;

			.ruleBox,
			.recordBox {
				width: 444rpx;
				height: 582rpx;
				background: #F9C437;
				border-radius: 20rpx;
				margin: 336rpx auto 0;
				padding: 39rpx 48rpx;

				.title {
					margin-bottom: 30rpx;
					display: flex;
					justify-content: center;

					image {
						width: 51rpx;
						height: 44rpx;
					}

					text {
						font-size: 30rpx;
						font-family: PingFang SC;
						font-weight: 800;
						color: #FFFFFF;
					}
				}

				.textBox {
					font-size: 24rpx;
					font-family: PingFang SC;
					font-weight: 800;
					color: #FFFFFF;
					line-height: 35rpx;

					.time {
						position: relative;

						.point {
							position: absolute;
							left: -20rpx;
							top: 50%;
							transform: translateY(-50%);
							display: block;
							width: 8rpx;
							height: 8rpx;
							background: #FFFFFF;
							border-radius: 50%;
						}
					}
				}
			}

			.getGiftBox {
				width: 500rpx;
				height: 600rpx;
				position: relative;
				background-image: url(../../static/event/lucky/redbag.png);
				background-repeat: no-repeat;
				background-size: cover;
				background-position: center;
				margin: 296rpx auto 0;

				.title {
					text-align: center;
					font-size: 30rpx;
					font-family: jiangxizhuokai;
					font-weight: 400;
					color: #323232;
					line-height: 40rpx;
					position: absolute;
					top: 269rpx;
					left: 50%;
					transform: translateX(-50%);
				}

				image {
					width: 165rpx;
					height: 165rpx;
					position: absolute;
					bottom: 18rpx;
					left: 50%;
					transform: translateX(-50%);
				}
			}

			.close {
				width: 50rpx;
				height: 50rpx;
				margin-top: 32rpx;
				position: relative;
				left: 50%;
				transform: translateX(-50%);
			}
		}
	}
</style>
