<template>
	<view class="container">
		<view class="sa-user-info">
			<view class="sa-flex-x space desc">
				<view class="userinfo">
					<view class="nickname" @click="editUsername">{{ username }}</view>
					<view class="sign">更换个人信息</view>
				</view>
				<view class="cover" @click="changeAvatar">
					<image :src="avatarUrl" mode="aspectFill"></image>
				</view>
			</view>
		</view>
		<view class="sa-menu">
			<button hover-class="" @click="bindEmail">
				<view class="sa-flex-x space item brtop">
					<view class="sa-flex-x space name">
						绑定邮箱
						<view v-if="email" class="email-display">：{{ email }}</view>
					</view>
					<view class="drow">></view>
				</view>
			</button>
			<button hover-class="" @click="showRegistrationDate">
				<view class="sa-flex-x space item line">
					<view class="sa-flex-x space name">
						显示注册日期
						<view class="registration-date">：{{ registrationDate }}</view>
					</view>
					<view class="drow">></view>
				</view>
			</button>
			<button hover-class="" @click="url('index/scheme')">
			    <view class="sa-flex-x space item line">
			        <view class="sa-flex-x space name">理财方案</view>
			        <view class="drow">></view>
			    </view>
			</button>

			<button hover-class="" @click="url('index/article?id=0')">
				<view class="sa-flex-x space item brbot line">
					<view class="sa-flex-x space name">关于我们</view>
					<view class="drow">></view>
				</view>
			</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			username: '用户_bwaUN', // 默认值
			avatarUrl: '@/static/avatar.png', // 默认值
			email: '',
			registrationDate: '2023-01-02' // 默认值
		}
	},
	onLoad() {
		this.loadUserInfo();
	},
	methods: {
		url(url) {
			uni.navigateTo({ url: `/pages/${url}` });
		},
		loadUserInfo() {
			const userInfo = uni.getStorageSync('userInfo');
			if (userInfo) {
				this.username = userInfo.username || this.username;
				this.avatarUrl = userInfo.avatarUrl || this.avatarUrl;
				this.email = userInfo.email || this.email;
				this.registrationDate = userInfo.registrationDate || this.registrationDate;
			}
		},
		editUsername() {
			uni.showModal({
				title: '更改用户名',
				content: '请输入新的用户名',
				editable: true,
				success: (res) => {
					if (res.confirm && res.content) {
						this.username = res.content;
						this.saveUserInfo();
					}
				}
			});
		},
		changeAvatar() {
			uni.chooseImage({
				count: 1,
				success: (res) => {
					const filePath = res.tempFilePaths[0];
					this.avatarUrl = filePath; // 直接使用选择的图片路径
					this.saveUserInfo();
				}
			});
		},
		bindEmail() {
			uni.showModal({
				title: '绑定邮箱',
				content: '请输入您的邮箱地址',
				editable: true,
				success: (res) => {
					if (res.confirm && res.content) {
						this.email = res.content;
						this.saveUserInfo();
					}
				}
			});
		},
		saveUserInfo() {
			const data = {
				username: this.username,
				avatarUrl: this.avatarUrl, // 直接保存选择的图片路径
				email: this.email,
				registrationDate: this.registrationDate
			};
			uni.setStorageSync('userInfo', data); // 保存到本地存储
		},
		showRegistrationDate() {
			uni.showToast({
				title: `注册日期: ${this.registrationDate}`,
				icon: 'none'
			});
		}
	}
}
</script>

<style lang="less">
	.sa-user-info {
		background-color: #fff;
		padding: 40rpx;
		margin: 0 20rpx 20rpx;
		border-radius: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);
		.desc {
			.cover {
				width: 120rpx;
				height: 120rpx;
				background-color: #f3f3f3;
				border: 1px solid #fff;
				border-radius: 50%;
				image {
					width: 100%;
					height: 100%;
					border-radius: 50%;
				}
			}
			.userinfo {
				width: 360rpx;
				.nickname {
					font-size: 36rpx;
					color: #333;
					font-weight: bold;
					cursor: pointer;
				}
				.sign {
					color: #999;
					font-size: 28rpx;
					margin-top: 5rpx;
				}
			}
		}
	}
	.sa-menu {
		margin: 20rpx;
		.item {
			height: 120rpx;
			padding: 0 40rpx;
			background-color: #fff;
			border-radius: 10rpx;
			box-shadow: 0 1rpx 5rpx rgba(0, 0, 0, 0.1);
			transition: background-color 0.3s;
			&:hover {
				background-color: #f7f7f7;
			}
			.name {
				font-size: 32rpx;
				display: flex;
				justify-content: space-between;
				align-items: center;
			}
			.email-display, .registration-date {
				font-size: 28rpx;
				color: #555;
				margin-top: 5rpx;
			}
			.drow {
				font-size: 32rpx;
				color: #999;
			}
			&.brtop {
				border-radius: 20rpx 20rpx 0 0;
			}
			&.brbot {
				border-radius: 0 0 20rpx 20rpx;
			}
			&.line:after {
				position: absolute;
				top: 0;
				left: 0;
				right: 0;
				width: 100%;
				height: 2rpx;
				content: '';
				background-image: linear-gradient(45deg, rgba(0,0,0,0), rgba(0,0,0,.1), rgba(0,0,0,0));
			}
		}
	}
</style>
