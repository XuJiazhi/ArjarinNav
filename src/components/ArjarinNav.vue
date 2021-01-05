<template>
  <div class="Nav">
    <!-- 搜索栏主体 -->
    <div class="navContainer">
      <el-menu
        :default-active="activeIndex2"
        class="el-menu-demo"
        mode="horizontal"
        background-color="#545c64"
        text-color="#fff"
        active-text-color="#ffd04b"
      >
        <!-- “回到首页” -->
        <el-menu-item index="0"><span class="home">HOME</span></el-menu-item>
        <!-- 第一栏默认内容“日历” -->
        <el-menu-item index="1">日历</el-menu-item>

        <!-- 通过循环访问数组，获得每个栏目的名字与下标 -->
        <!-- 并通过鼠标指针浮入事件来获得子栏目所属栏目的第一个下标 -->
        <el-submenu
          v-for="(page, pageIndex) in pageList"
          :key="pageIndex"
          :index="pageIndex.toString()"
          @mouseenter="handleSelectPageName(pageIndex)"
        >
          <!-- 双击栏目名触发编辑栏目名称的弹框显示 -->
          <template slot="title">
            <span
              @mouseenter="handleSelectPageName(pageIndex)"
              @dblclick="editPageDialogVisible = true"
            >
              {{ page }}</span
            ></template
          >
          <!-- 子栏目下拉框 -->
          <!-- 通过循环访问数组，获得每个子栏目的名字与下标 -->
          <!-- 子栏目通过二维数组存储，二维数组第一项为子栏目所属栏目的下标，第二项是子栏目在下拉框第几行 -->
          <!-- 双击子栏目名触发编辑子栏目名称的弹框显示 -->
          <ul>
            <li
              v-for="(subpage, subpageIndex) in subpageList[pageIndex]"
              :key="subpageIndex"
              :index="subpageIndex.toString()"
              @dblclick="editSubpageDialogVisible = true"
              @click="handleSelectSubpageName(pageIndex, subpageIndex)"
            >
              {{ subpage }}
            </li>
          </ul>
          <!-- 添加子栏目按钮 -->
          <button @click="addSubpageDialogVisible = true">
            <i class="el-icon-plus"></i>
          </button>
        </el-submenu>
        <!-- 添加栏目按钮 -->
        <el-menu-item index="" @click="addPageDialogVisible = true">
          <i class="el-icon-circle-plus-outline"></i>
        </el-menu-item>
      </el-menu>
      <!-- 静态头像 -->
      <div class="avatar">
        <img src="@/assets/icon/akari.png" alt="avatar" />
      </div>
    </div>
    <!-- 弹框部分 -->
    <!-- 添加导航栏栏目弹框 -->
    <el-dialog title="新的页面：" :visible.sync="addPageDialogVisible">
      <input type="text" v-model="newPage" />
      <button @click="addPage">添加页面</button>
    </el-dialog>

    <!-- 添加导航栏子栏目弹框 -->
    <el-dialog title="新的子页面：" :visible.sync="addSubpageDialogVisible">
      <input type="text" v-model="newSubpage" />
      <button @click="addSubpage">添加子页面</button>
    </el-dialog>

    <!-- 编辑栏目弹框 -->
    <el-dialog title="新的名字：" :visible.sync="editPageDialogVisible">
      <input type="text" v-model="newPageName" />
      <button @click="() => changePageName()">修改名称</button>
      <button @click="delPage">删除该子页面</button>
    </el-dialog>

    <!-- 编辑子栏目弹框 -->
    <el-dialog title="新的名字：" :visible.sync="editSubpageDialogVisible">
      <input type="text" v-model="newSubpageName" />
      <button @click="() => changeSubpageName()">修改名称</button>
      <button @click="delSubpage">删除子页面</button>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "ArjarinNav",
  data() {
    return {
      activeIndex: "1",
      activeIndex2: "1", //这是element-ui自带的一个属性，表明一开始默认激活的栏目列为“1”号列

      pageList: [], //栏目列表
      subpageList: [[]], //子栏目列表（二维数组，第一项为所属栏目，第二项为在下拉框中的所在行）

      pageIndex: -1,
      subpageIndex: -1,
      newPageName: "", //修改栏目名时输入的新的栏目名
      newSubpageName: "", //修改子栏目名时输入的新的子栏目名
      newPage: "新的页面", //新栏目
      newSubpage: "新的子页面", //新的子栏目

      // 所有弹框：
      addPageDialogVisible: false,
      editPageDialogVisible: false,
      addSubpageDialogVisible: false,
      editSubpageDialogVisible: false,
    };
  },
  methods: {
    //增
    addPage() {
      try {
        this.pageList.push(this.newPage); //添加栏目时，给储存栏目名的数组追加一个字符串
        this.subpageList.push([]); //同时储存子栏目的二维数组加一行
        this.addPageDialogVisible = false; //添加栏目完毕，添加栏目弹框消失
        // this.$emit("addPage", this.newPage);
      } catch (error) {
        console.log(error);
      }
    },
    addSubpage() {
      try {
        this.subpageList[this.pageIndex].push(this.newSubpage); //添加子栏目时，给储存子栏目的二维数组的对应某一行下追加一列
        this.addSubpageDialogVisible = false; //添加子栏目完毕，添加子栏目弹框消失
        // this.$emit("changeIndex", [this.pageIndex + 1, this.subpageIndex + 1]);
        // this.$emit("addSubpage", this.newSubpageName);
      } catch (error) {
        console.log(error);
      }
    },
    //删
    delPage() {
      this.pageList.splice(this.pageIndex, 1); //删除储存栏目名的数组的对应位置
      this.subpageList.splice(this.pageIndex, 1); //储存子栏目名的数组删去对应的行
      // this.$emit("delPage", this.pageIndex + 1);
      this.editPageDialogVisible = false; //删除栏目完毕，编辑栏目弹框消失
    },
    delSubpage() {
      this.subpageList[this.pageIndex].splice(this.subpageIndex, 1); //删除储存子栏目名的二维数组的对应元素
      // let index = [this.pageIndex + 1, this.subpageIndex + 1];
      // this.$emit("delSubpage", index);
      this.editSubpageDialogVisible = false; //删除子栏目完毕，编辑子栏目弹框消失
    },
    // “查”
    handleSelectPageName(pageIndex) {
      this.pageIndex = pageIndex; //获得栏目所属下标
      // console.log(this.pageIndex + 1, 0);
      // this.$emit("changeIndex", [this.pageIndex + 1, 0]);
    },
    handleSelectSubpageName(pageIndex, subpageIndex) {
      this.pageIndex = pageIndex; //获得子栏目所属行下标
      this.subpageIndex = subpageIndex; //获得子栏目所属列下标
      // console.log(this.pageIndex + 1, this.subpageIndex + 1);
      // this.$emit("changeIndex", [this.pageIndex + 1, this.subpageIndex + 1]);
    },
    // 改
    changePageName() {
      this.pageList[this.pageIndex] = this.newPageName;
      //修改栏目名时，即把储存栏目名的数组的对应位置重新赋值为用户新输入的值
      this.pageIndex = -1; //
      this.editPageDialogVisible = false;
    },
    changeSubpageName() {
      this.subpageList[this.pageIndex][this.subpageIndex] = this.newSubpageName;
      //修改子栏目名时，即把储存子栏目名的二维数组的对应位置重新赋值为用户新输入的值
      this.pageIndex = -1; //
      this.subpageIndex = -1; //
      this.editSubpageDialogVisible = false;
    },
  },
};
</script>

<style>
.head {
  float: right;
  margin: 10px;
}
body {
  margin: 0;
}
.new-name-input {
  display: none;
}
.el-icon-circle-plus-outline {
  color: #ffffff;
}
.navContainer {
  display: flex;
  flex-direction: row;
  align-items: center;
  overflow: hidden;
  height: 60px;
  background-color: #545c64;
}
.home {
  text-shadow: 0 0 0.2em #ffd04b, -0 -0 0.2em #ffd04b;
  font-size: 1.1em;
}
.avatar img {
  height: 40px;
  width: 40px;
  border-radius: 50%;
  background-color: #fff;
}
.avatar {
  margin: 0 20px;
}
.el-menu-demo {
  flex: 1;
}
</style>
