<template>
	<view class="body">
		<!-- 搜索框 -->
		<view class="search">
			<uni-search-bar @confirm="search" @input="input" @clear="flushList" @cancel="flushList"></uni-search-bar>
		</view>
		
		<view class="main">
			<!-- 弹窗打开按钮 -->
			<view class="codeButton"> 
				<icon ></icon>
				<view class="openAddCodeButton">
					<button @click="openAddCode" size="mini">新增密码</button>
				</view>
				&nbsp;
				<view class="openImportButton">
					<button @click="openImport" size="mini">导入密码集</button>
				</view>
				&nbsp;
				<view class="openExportButton">
					<button @click="openExport" size="mini">导出密码</button>
				</view>
			</view>
			
			<!-- 密码列表 -->
			<view class="codeList" >
				<uni-list v-for="(item,index) in listData" :key='index'>
					<uni-list-item style="background-color: rgb(190, 239, 175, 0.2);width: 300px;" :title="item.appName" :note="'账号: '+item.account+'\n'+'密码: '+item.password" clickable @click="updateCode(item.id,item.appName,item.account,item.password)">
						<template v-slot:footer>
							<view class="deleteButton">
								<button @tap.stop="openDeletePopup(item.id)" size="mini">delete</button> <br>
								<button @tap.stop="copy(item.password)" size="mini">copy密码</button>
							</view>
						</template>
					</uni-list-item>
				</uni-list>
			</view>
		
			<br>
			<view class="tips">
				<text @click="createTable">第一次使用请点击</text>
			</view>
			<br>
			<view class="about">
				<text @click="toInfoPage">关于应用</text>
			</view>
			<br>
		
			<!-- 弹窗本体 -->
			<uni-popup ref="popup" :mask-click="false">
				<uni-card title="添加密码">
					<!-- 这里写一个表单 -->
					<text>请在此添加密码。</text>
					<view>
						<uni-forms :modelValue="formData" >
							<uni-forms-item label="应用名称" name="appName">
								<uni-easyinput type="text" v-model="formData.appName" placeholder="该密码作用的应用.." />
							</uni-forms-item>
							<uni-forms-item label="账号" name="account">
								<uni-easyinput type="text" v-model="formData.account" placeholder="账号/用户名.." />
							</uni-forms-item>
							<uni-forms-item label="密码" name="password">
								<uni-easyinput type="text" v-model="formData.password" placeholder="对应密码.." />
							</uni-forms-item>
						</uni-forms>
						<button @click="submit">Submit</button> <br>
						<button @click="cancel">Cancel</button>
					</view>
				</uni-card>
			</uni-popup>
			
			<!-- 删除询问弹窗本体 -->
			<uni-popup ref="deletePopup" :mask-click="false">
				<uni-card>
					<span>确定删除吗?</span>
					<button @click="deleteTableData">DELETE</button> <br>
					<button @click="deletePopupCancel">Cancel</button>
				</uni-card>
			</uni-popup>
			<!-- 导入密码弹窗本体 -->
			<uni-popup ref="importPopup" :mask-click="false">
				<uni-card>
					<text>
						<text style="color: red;">导入格式:</text>
						<text style="float: right; color: green;" @click="changeToN">切换</text><br>
						应用名称1,账号1,密码1 <br>
						应用名称2,账号2,密码2 <br>
						<text style="color: red;">
							注：如果上述参数中含有英文逗号(,)请用英文双引号("")包裹该参数。确保光标位于最后一个字符后!!
						</text>
						<br>
						例如：<br>
						应用:test-app  账号:test  密码:test,12345 <br>
						则导入格式为：<br>
						test-app,test,"test,12345"
					</text>
					<uni-easyinput clearable type="textarea" v-model="importInputValue" placeholder="请输入内容" maxlength="-1"></uni-easyinput>

					<br>
					<button @click="submitImport">Submit</button> <br> 
					<button @click="importPopupCancel">Cancel</button> 
				</uni-card>
			</uni-popup>
			<!-- 导入密码弹窗本体(N) -->
			<uni-popup ref="N_importPopup" :mask-click="false">
				<uni-card>
					<text>
						<text style="color: red;">导入格式:</text>
						<text style="float: right; color: green;" @click="changeToDefault">切换</text>
						<br>
						应用名称1 \n
						账号1 \n
						密码1 \n\n
						应用名称2\n
						账号2 \n
						密码2 \n
						<text style="color: red;">
							注：如果某应用无账号/密码,请用任意字符代替。
						</text>
						<br>
					</text>
					<uni-easyinput clearable type="textarea" v-model="importInputValue" placeholder="请输入内容" maxlength="-1"></uni-easyinput>
			
					<br>
					<button @click="N_submitImport">Submit</button> <br> 
					<button @click="importPopupCancel">Cancel</button> 
				</uni-card>
			</uni-popup>
			<!-- 导出密码弹窗本体 -->
			<uni-popup ref="exportPopup" :mask-click="false">
				<uni-card>
					<span>
						<text>
							<text style="color: red;">导出格式:</text> 
							<text style="float: right; color: green;" @click="changeToNExport">切换</text><br>
							一组密码中的每个属性使用空格分隔,每组密码之间使用换行显示 <br>
							应用名称1,账号1,密码1 <br>
							应用名称2,账号2,密码2 <br>
							例如：<br>
							应用名称:test-app  账号:test  密码:test,12345 <br>
						</text>
						<!-- <text>{{exportContent}}</text> -->
						<uni-easyinput type="textarea" v-model="exportContent" maxlength="-1"></uni-easyinput>
					</span>
					<br>
					<button @click="exportCode">一键copy</button> <br>
					<button @click="exportPopupCancel">Cancel</button>
				</uni-card>
			</uni-popup>
			<!-- 导出密码弹窗本体(N) -->
			<uni-popup ref="N_exportPopup" :mask-click="false">
				<uni-card>
					<span>
						<text>
							<text style="color: red;">导出格式:</text> 
							<text style="float: right; color: green;" @click="changeToDefaultExport">切换</text><br>
							一组密码中的每个属性使用换行分隔,每组密码之间使用换行显示 <br>
							应用名称1\n账号1\n密码1\n\n
							应用名称2... <br>
						</text>
						<!-- <text>{{exportContent}}</text> -->
						<uni-easyinput type="textarea" v-model="exportContent" maxlength="-1"></uni-easyinput>
					</span>
					<br>
					<button @click="exportCode">一键copy</button> <br>
					<button @click="exportPopupCancel">Cancel</button>
				</uni-card>
			</uni-popup>
			
			<!-- 修改密码弹窗本体 -->
			<uni-popup ref="updatePopup" :mask-click="false">
				<uni-card title="修改密码">
					<!-- 这里写一个表单 -->
					<text>请在此修改密码。</text>
					<view>
						<uni-forms :modelValue="updateForm" >
							<uni-forms-item label="应用名称" name="appName">
								<uni-easyinput type="text" v-model="updateForm.appName" :placeholder="updateForm.appName" disabled />
							</uni-forms-item>
							<uni-forms-item label="账号" name="account">
								<uni-easyinput type="text" v-model="updateForm.account" placeholder="账号/用户名.." />
							</uni-forms-item>
							<uni-forms-item label="密码" name="password">
								<uni-easyinput type="text" v-model="updateForm.password" placeholder="对应密码.." />
							</uni-forms-item>
						</uni-forms>
						<button @click="update">UPDATE</button> <br>
						<button @click="updateCancel">Cancel</button>
					</view>
				</uni-card>
			</uni-popup>
		</view>
	</view> 

</template>

<script setup>
import { onMounted,reactive,ref } from 'vue';
import DB from '../../static/sqlite.js'

/* 关于密码列表 */
onMounted(()=>{
	openSQL();
	setTimeout(selectTableData,500);
})
var listData=ref([])
//打开数据库
function openSQL(){
	console.log("点击打开数据库");
	//查询有没有打开数据库
	let open = DB.isOpen();
	console.log("数据库状态",open ? "开启":"关闭");
	if(!open){
		DB.openSqlite()
		.then(res => {
			console.log("数据库已打开");
		})
		.catch(error => {
			console.log("数据库开启失败");
		})
	}
}
// 创建表
function createTable() {
    let open = DB.isOpen();
	if (open) {
		openSQL();
	let sql ='"id" INTEGER PRIMARY KEY AUTOINCREMENT,"appName" text,"account" text,"password" text';
    // 创建表 DB.createTable(表名, 表的列)
    DB.createTable("code_table", sql)
	.then(res => {
    console.log("创建code_table表成功");
	uni.showToast({
		title:"创建密码表成功",
		icon:'none'
	})
    })
    .catch(error => {
    console.log("创建表失败");
    });
    } else {
    console.log("数据库未打开");
    }
}
//新增表数据(test)
function insertTableData(){
	let open = DB.isOpen();
	if (open) {
		let arr = [{
			appName: formData.appName,
			account: formData.account,
			password: formData.password
		},
		]
		arr.map(item => {
			let sql =`'${item.appName}','${item.account}','${item.password}'`
			let condition = "'appName','account','password'";
			// 新增 DB.insertTableData(表名, 对应表头列的数据)
			DB.insertTableData("code_table",sql,condition)
				.then(res => {
					console.log("新增成功");
				})
				.catch(error => {
					console.log("失败",error);
				})
		})
	}else{
		console.log("数据库未打开");
	}
}
//查询表数据
function selectTableData(){
	let open = DB.isOpen();
	if(open){
		// 查询表 DB.selectTableData(表名,查询条件列名,查询条件列值)
		DB.selectTableData("code_table")
		.then(res => {
			console.log("contact",res);
			listData.value=res;
		})
		.catch(error =>{
			console.log("查询失败",error);
		})
	}else{
			console.log("数据库未打开");
		}
}


/* 关于弹窗表单 */
let formData = reactive({
  appName: ``,
  account: ``,
  password: ``
})
function submit(){
	console.log(formData.appName);
	insertTableData()
	popup.value.close()
	selectTableData()
}
function cancel(){
	popup.value.close()
}

/* 关于弹窗 */
const popup = ref()
function openAddCode(){
	popup.value.open()
}
/* 关于删除弹窗 */
const deletePopup = ref()
// 接收传递的要删除的id值
let deleteId=ref('')
function openDeletePopup(id){
	deletePopup.value.open()
	console.log("id"+id);
	deleteId.value=id
}
function deletePopupCancel(){
	deletePopup.value.close()
}
/* 关于导入弹窗 */
const importPopup = ref()
const N_importPopup = ref()
let importInputValue = ref()
function openImport(){
	importPopup.value.open() 
}
function importPopupCancel(){
	importPopup.value.close()
	N_importPopup.value.close()
}
function submitImport(){
	console.log(importInputValue.value);
	let open =DB.isOpen()
	if(open){
		var codeArr=[]; //存所有账号的数组
		var str= importInputValue.value;
		var lines = str.split('\n');
		//对每一组账号密码进行处理
		lines.forEach(function(line){
			var regex = /,(?=(?:[^"]*"[^"]*")*[^"]*$)/
			var matches = line.split(regex);
			console.log(matches[2][0]);
			var ac = matches[1]
			if(matches[1][0]=='"' && matches[2].substr(-1)=='"'){
				console.log(`原本账号为:${matches[2]}`);
				ac = matches[1].match(/"([^"]*)"/)[1]
				console.log(`ac为:${ac}`);
			}
			var pass = matches[2]
			if(matches[2][0]=='"' && matches[2].substr(-1)=='"'){
				console.log(`原本密码为:${matches[2]}`);
				pass = matches[2].match(/"([^"]*)"/)[1] /* match匹配完会自动拼接在原本字符串后形成两段超长字符串,使用[1]匹配到第二段字符串*/
				console.log(`pass为:${pass}`);
			}
			var everyAccount = {
				appName: matches[0],
				account: ac,
				password: pass
			};
			codeArr.push(everyAccount)
		})
		console.log(codeArr);
		codeArr.map(item => {
			let sql = `'${item.appName}','${item.account}','${item.password}'`
			let condition = "'appName','account','password'";
			DB.insertTableData("code_table",sql,condition)
			.then(res => {
				console.log("新增成功");
				importPopup.value.close()
			})
			.catch(error => {
				console.log("失败",error);
			})
		})
	}else{
		console.log("数据库未打开");
	}
	selectTableData()
}
function changeToN(){
	importPopup.value.close()
	N_importPopup.value.open()
}
function changeToDefault(){
	N_importPopup.value.close()
	importPopup.value.open()
}
function N_submitImport(){
		console.log(importInputValue.value);
		let open =DB.isOpen()
		if(open){
			var codeArr=[]; //存所有账号的数组
			var str= importInputValue.value;
			var lines = str.split('\n\n');
			//对每一组账号密码进行处理
			lines.forEach(function(line){
				var matches = line.split('\n');
				var everyAccount = {
					appName: matches[0],
					account: matches[1]==undefined?"":matches[1],
					password: matches[2]==undefined?"":matches[2]
				};
				codeArr.push(everyAccount)
			})
			console.log(codeArr);
			codeArr.map(item => {
				let sql = `'${item.appName}','${item.account}','${item.password}'`
				let condition = "'appName','account','password'";
				DB.insertTableData("code_table",sql,condition)
				.then(res => {
					console.log("新增成功");
					N_importPopup.value.close()
				})
				.catch(error => {
					console.log("失败",error);
				})
			})
		}else{
			console.log("数据库未打开");
		}
		selectTableData()
}

/* 关于导出弹窗 */
const exportContent = ref('')
const exportPopup = ref()
const N_exportPopup = ref()
function openExport(){
	exportPopup.value.open()
	getExportContent()
}
function exportPopupCancel(){
	exportPopup.value.close()
	N_exportPopup.value.close()
}
function exportCode(){
	console.log("进行导出");
	uni.setClipboardData({
		data:exportContent.value
	})
}
function getExportContent(){
	for(var p in listData.value){//遍历json数组，p为索引
		console.log(listData.value[p].account + " " + listData.value[p].password);
		exportContent.value+='应用名称:'+listData.value[p].appName
								+'  '
								+'账号:'+listData.value[p].account
								+'  '
								+'密码:'+listData.value[p].password+'\n'
	}
} 
function N_getExportContent(){
		for(var p in listData.value){//遍历json数组，p为索引
		console.log(listData.value[p].account + " " + listData.value[p].password);
		exportContent.value+=listData.value[p].appName
								+'\n'
								+listData.value[p].account
								+'\n'
								+listData.value[p].password+'\n\n'
	}
}
function changeToNExport(){
	exportPopup.value.close()
	N_exportPopup.value.open()
	exportContent.value=''
	N_getExportContent()
}
function changeToDefaultExport(){
	N_exportPopup.value.close()
	exportPopup.value.open()
	exportContent.value=''
	getExportContent()
}


/* 关于搜索 */
const inputValue=ref("")
//根据应用名查询数据
function selectTableDataByAppName(appName){
	let open = DB.isOpen();
	if(open){
		DB.selectTableDataMH("code_table","appName",appName)
		.then(res => {
			console.log("contact表数据",res);
			listData.value = res;
		})
		.catch(error => {
			console.log("查询失败",error);
		});
	}else{
		console.log("数据库未打开");
	}
}

function search(res){
	/* 在这调用根据应用名搜索 */
	selectTableDataByAppName(res.value)
	uni.showToast({ 
		title: '菠菜查找中..',
		icon: 'none',
		image:"../../static/aaa.png",
		mask: true,
		duration: 3000,
		});
} 
function input(res) {
		console.log('----input:', res)
}
//点击清除和取消时触发的事件(刷新)
function flushList () {
	console.log("点击了清除");
	selectTableData()
}
/* 关于删除 */
// 删除表数据
function deleteTableData() {
    let open = DB.isOpen();
    if (open) {
    // 删除表 DB.deleteTableData(表名,查询条件列名,查询条件列值)
        DB.deleteTableData("code_table", "id", deleteId.value)
            .then(res => {
				console.log("deleteId="+deleteId.value);
                console.log("删除成功");
				deletePopup.value.close()
                selectTableData();
            })
            .catch(error => {
                console.log("删除失败", error);
            });
    } else {
        console.log("数据库未打开");
    }
}

/* 关于复制 */
function copy(password){
	uni.setClipboardData({
		data:password
	})
}

/* 关于修改密码 */
const updatePopup=ref()
const updateForm=reactive({
	id:``,
	appName:``,
	account:``,
	password:``
})
function updateCode(id,appName,account,password){
	updatePopup.value.open()
	console.log(`修改了${id}${appName}`);
	updateForm.id = id
	updateForm.appName = appName
	updateForm.account = account
	updateForm.password = password
}
function updateCancel(){
	updatePopup.value.close()
}
function update(){
	console.log(updateForm.id+","+updateForm.appName);
	console.log(updateForm.account);
	updateTableData()
	updatePopup.value.close()
}
function updateTableData(){
	let open = DB.isOpen();
	if(open){
		let data = `account= '${updateForm.account}',password= '${updateForm.password}'`
		DB.updateTableData("code_table",data,"id",updateForm.id)
		.then(res => {
			console.log("更新成功");
			selectTableData()
		})
		.catch(error =>{
			console.log("修改失败",error);
		})
	}else{
		console.log("数据库未打开");
	}
}
function toInfoPage(){
	uni.navigateTo({
		url: '/pages/index/info'
	})	
}

</script>

<style>
.body{
	display: flex;
	flex-flow: column;
	height: 100vh;
	background-color: rgb(190, 239, 175);
}
.main{
	display: flex; /* 弹性盒子*/
	flex-flow: column; /* 子组件纵向布局*/
	align-items: center; /* 子组件居中*/
	justify-content: center;
	background-color: rgb(190, 239, 175);
}
.search{
	width: 100%;
	background-color: rgb(190, 239, 175);
}
.codeButton{
	display: flex;
	margin: 10px;
}
button{
	background-color: rgb(145, 189, 128,0.7);
	color: rgb(57, 83, 59);
}
.codeList{
	border-radius: 20px;
}
.tips{
	color: orange;
}
.about{
	color: skyblue;
}
</style>
