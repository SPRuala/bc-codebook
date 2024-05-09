<template>
	<view class="body">
			<view class="myInfo">
				<view class="photo">
					<image src="/static/txzsb.png" style="height: 80px;width: 80px; border-radius: 50%;"/>
				</view>
				<view class="myTall">
					<text>
						<text style="color: forestgreen;">Hi~我是菠菜~ <br>
						欢迎来到我的密码本 <br>
						</text> 
						<text style="color: orange; font-style: oblique;">
							这是一个将密码存入本地sqlite数据库的项目
						</text>
						<br>
						<text style="color: forestgreen;">
							希望它能给你的密码一个安全的家
						</text>
						<br>
						<text style="color: orange; font-style: italic">
						怎么找到我 ? : 
						&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alac2002@qq.com
						&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://ruala.fun/
						</text>
					</text>
				</view>
			</view>
			<view class="projectInfo">
				<h4 style="color: forestgreen;margin-top: 0.5rem; margin-left: 1rem;">主要功能</h4>
				<view class="projectInfoContent">
					<text>● ⏰省事的copy密码</text>
					<text style="margin-top: 0.5rem;">● 🚄一键删除应用密码</text>
					<text style="margin-top: 0.5rem;">● 📋人性化的密码修改表单</text>
					<text style="margin-top: 0.5rem;">● 🎊格式化的简洁密码列表</text>
					<text style="margin-top: 0.5rem;">● ✨不同的导入/导出格式可供选择</text>
					<text style="margin-top: 0.5rem;">● 🏠拥有本地密码表，无需担心安全</text>
				</view>
				<br>
			</view>
			<view class="more">
				<h4 style="margin-top: 0.5rem; margin-left: 1rem; color: forestgreen; margin-bottom: 0.5rem;">更多</h4>
				<view class="moreContent">
					<text style="margin-left: 1.5rem;">
						本项目已上传至github仓库:
					</text>
					<text style="margin-left: 1rem;color: orange; font-style: oblique;">
						https://github.com/SPRuala/bc-codebook
					</text>
					<br>
					<br>
					<text style="margin-left: 1.5rem;">密码数据库地址: </text>
					<text style="word-wrap: break-word;margin-left: 1rem;color: orange; font-style: oblique;">
						Android/data/ruala.BCCodebook/apps/__UNI__9B79C4B/doc/code-book-db
					</text>
				</view>
			</view>
			<view class="thanks">
				<h5 style="padding-left: 1rem;margin-top: 0.5rem; color: forestgreen;">特别鸣谢</h5>
				<view class="thanksContent">
					<text style="padding-left: 1.5rem;color: forestgreen;font-size: 12px;">感谢@Xiii_i图标贡献</text> <br>
					<text style="padding-left: 1.5rem; color: forestgreen;font-size: 12px;">感谢@soladuor提供的测试数据与导入导出格式</text>
				</view>
			</view>
	</view>
</template>

<script setup>

</script>

<style>
.body{
	background-color: rgb(190, 239, 175);
	width: 100%;
	height: 100vh;
	display: flex; /* 弹性盒子*/
	flex-flow: column; /* 弹性盒子内子元素纵向排列*/
	align-items: center; /* 子元素横向居中*/
}
.myInfo{
	background-color: rgb(255, 255, 255,0.4);
	width: 90%;
	border-radius: 25px;
	margin-top: 1rem; 
	display: flex;
	flex-direction: row;
}
.photo{
	margin-left: 0.5rem;
	margin-top: 0.5rem;
}
.myTall{
	margin-left: 1rem;
}
.projectInfo{
	margin-top: 1rem;
	background-color: rgb(255, 255, 255,0.4);
	width: 90%;
	border-radius: 25px;
	display: flex;
	flex-flow: column;
}
.projectInfoContent{
	display: flex;
	flex-flow: column;
	margin-left: 3rem;
	margin-top: 0.5rem;
	color: forestgreen;
}
.thanks{
	background-color: rgb(255, 255, 255,0.4);
	margin-top: 1rem;
	border-radius: 25px;
	width: 90%;
}
.thanksContent{
	margin-bottom: 0.5rem;
}
.more{
	background-color: rgb(255, 255, 255,0.4);
	margin-top: 1rem;
	border-radius: 25px;
	width: 90%;
	display: flex;
	flex-flow: column;
}
.moreContent{
	color: forestgreen;
	margin-bottom: 1rem;
}
</style>
