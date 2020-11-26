<template>
	<div class="home-page">
		<div class="header">
			<AppHeader />
		</div>
		<div class="content">
			<!-- 四个页面 -->
			<router-view />
		</div>
		<div class="nav-bar">
			<div class="item">
				<router-link to="/home/hot">
					<i class="iconfont iconshouye2"></i>
					首页
				</router-link>
			</div>
			<div class="item">
				<router-link to="/home/cate">
					<i class="iconfont iconleimupinleifenleileibie"></i>
					分类
				</router-link>
			</div>
			<div class="item">
				<router-link to="/home/cart">
					<i class="iconfont iconcart_icon"></i>
					购物车
				</router-link>
			</div>
			<div class="item">
				<router-link to="/home/user">
					<i class="iconfont iconpersonal"></i>
					我的
				</router-link>
			</div>
		</div>
	</div>
</template>

<script>
import AppHeader from "@/components/AppHeader";
export default {
	name: "HomePage",
	components: {
		AppHeader,
	},
};
</script>

<style lang="scss" scoped>
.home-page {
	.nav-bar {
		border-top: 1px solid rgba($color: #c39862, $alpha: 0.5);
		position: fixed;
		height: 50px;
		width: 100%;
		bottom: 0;
		left: 0;

		display: flex;
		justify-content: space-around;
		align-items: center;
		.item {
			flex-basis: 25%;

			// padding: 6px 0;
			i {
				display: block;
				font-size: 1.5rem;
				// color: rgb(218, 169, 37);
			}
			a {
				text-decoration: none;
				color: #5a5a5a;
			}
			&:last-child {
				border: none;
			}
			.router-link-active {
				color: #c39862;
			}
		}
	}
}

// 控制菜单样式
</style>
