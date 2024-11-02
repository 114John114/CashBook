<template>
	<view class="container">
		<view class="filter-bar">
			<picker 
				v-model="selectedContent" 
				:range="uniqueContents" 
				@change="onContentChange"
			>
				<view class="filter-input">{{ selectedContent }}</view>
			</picker>
		</view>
		<view class="sa-list">
			<view class="item" v-for="(item, index) in filteredDataList" :key="index">
				<view class="count">
					<view class="sa-flex-x">
						<view>日期: {{item.create_time}}</view>
						<view class="week">({{item.create_time | weekFormat}})</view>
					</view>
				</view>
				<view class="sa-flex-x space content">
					<view class="sa-flex-x space">
						<view class="sa-flex-x center cover" :class="item.type==1 ? 'income':'expend'">
							<image :src="'../../static/icon/'+item.icon+'.png'" mode="aspectFill"></image>
						</view>
						<view class="info">
							<view class="name">{{item.content}}</view>
							<view class="desc" v-if="item.remarks">{{item.remarks}}</view>
						</view>
					</view>
					<view class="sa-flex-x money" :class="item.type==1 ? 'income':'expend'">
						<text class="symbol">{{item.type==1 ? '+':'-'}}</text>
						<text>{{item.money}}</text>
					</view>
				</view>
			</view>
		</view>
		<view class="sa-flex-y center sa-error" v-if="filteredDataList.length == 0">
			<view class="info">
				<image class="cover" src="@/static/nocon.png" mode="aspectFit"></image>
			</view>
		</view>
	</view>
</template>

<script>
import format from '@/utils/format';
export default {
	data() {
		return {
			sumCount: 0,
			sumExpend: 0,
			sumIncome: 0,
			totalExpend: 0,
			totalIncome: 0,
			totalbalance: 0,
			dataList: [],
			nowTime: {
				all: '',
				year: '0000',
				month: '00',
				day: '00'
			},
			selectedContent: '选择内容',
			uniqueContents: []
		}
	},
	onLoad() {
		const str = format.getSysDate();
		const date = str.split('-');
		this.nowTime = {all: str,year: date[0],month: date[1],day: date[2]};
	},
	onShow() {
		this.getData();
	},
	filters: {
		weekFormat(val) {
			return format.getWeekDay(val);
		}
	},
	computed: {
		filteredDataList() {
			return this.dataList.filter(item => 
				item.content === this.selectedContent || this.selectedContent === '选择内容'
			);
		}
	},
	methods: {
		getData() {
			uni.getStorage({
				key: 'sa_storage_bill',
				success: res => {
					let data = JSON.parse(res.data);
					this.dataList = data.filter(item => item.type == 1);
					this.total();
					this.uniqueContents = [...new Set(this.dataList.map(item => item.content))]; // 获取唯一内容列表
				}
			})
		},
		total() {
			const data = this.dataList;
			const group = format.arrayGroup(data, 'type');
			if (Object.keys(group).length > 0) {
				const incomeMoney = group[1] ? group[1].reduce((sum, item) => sum + Number(item.money), 0) : 0;
				this.totalIncome = incomeMoney.toFixed(2);
				this.sumIncome = group[1] ? group[1].length : 0;
			}
			this.sumCount = data.length;
			this.totalbalance = this.totalIncome;
		},
		onContentChange(event) {
			this.selectedContent = this.uniqueContents[event.detail.value]; // 更新选中的内容
		}
	}
}
</script>

<style lang="less">
	.filter-bar {
		padding: 10rpx 20rpx;
		background-color: #f5f5f5;
		border-radius: 10rpx;
	}
	.filter-input {
		height: 40rpx;
		line-height: 40rpx;
		font-size: 28rpx;
		color: #333;
	}
	.sa-list {
		margin-top: 20rpx;
		.item {
			background-color: #fff;
			margin: 0 20rpx 20rpx 20rpx;
			padding: 30rpx 0 10rpx 0;
			border-radius: 20rpx;
			.count {
				margin: 0 30rpx;
				padding-bottom: 20rpx;
				font-size: 28rpx;
				border-bottom: 1px solid #f5f5f5;
				.week {
					margin-left: 10rpx;
				}
			}
			.content {
				margin: 0 30rpx;
				padding: 20rpx 0;
			}
			.cover {
				width: 80rpx;
				height: 80rpx;
				border-radius: 50%;
				&.expend {
					background-color: #07c160;
				}
				&.income {
					background-color: #f1b73a;
				}
				image {
					width: 40rpx;
					height: 40rpx;
				}
			}
			.info {
				width: 360rpx;
				margin-left: 30rpx;
				width: 300rpx;
				.name {
					font-size: 32rpx;
					color: #000;
				}
				.desc {
					font-size: 28rpx;
					color: #999;
				}
			}
			.money {
				&.expend {
					color: #000;
				}
				&.income {
					color: #f1b73a;
				}
				.symbol {
					margin-right: 5rpx;
					font-weight: bold;
				}
			}
			&:last-child{
				margin-bottom: 0;
			}
		}
	}
	.sa-error {
		height: 100%;
		text-align: center;
		.info {
			width: 400rpx;
			padding-top: 300rpx;
			.cover {
				height: 180rpx;
				width: 100%;
				margin-bottom: 20rpx;
			}
			.text {
				color: #a7a7a7;
				font-size: 32rpx;
			}
		}
	}
</style>
