<script setup>
import {
  Management,
  Promotion,
  UserFilled,
  User,
  Crop,
  EditPen,
  SwitchButton,
  CaretBottom,
  HomeFilled
} from '@element-plus/icons-vue'
import avatar from '@/assets/default.png'
import {userInfoService} from '@/api/user.js'
import useUserInfoStore from "@/stores/userInfo.js";
const userInfoStore=useUserInfoStore();
//调用函数获取用户详细信息
const getUserInf = async ()=>{
  let result = await userInfoService();
  //存储到pinia
  userInfoStore.setInfo(result.data);
}
getUserInf()
//条目被点击后，调用
import {useRouter} from 'vue-router'
import {ElMessage, ElMessageBox} from "element-plus";
import {userTokenStore} from '@/stores/token.js'
const router = useRouter();
const tokenStore=userTokenStore();
const handleCommand = (command)=>{
  if(command==='logout'){
    //退出登录
    ElMessageBox.confirm(
        '你确认退出登录码？',
        '温馨提示',
        {
          confirmButtonText: '确认',
          cancelButtonText: '取消',
          type: 'warning',
        }
    )
        .then(async () => {
          //退出
          //清空pinia中的token和个人信息
          tokenStore.removeToken()
          userInfoStore.removeInfo()
          //跳转到登录页面
          await router.push('/login')
          ElMessage({
            type: 'success',
            message: '退出成功',
          })
        })
        .catch(() => {
          //用户点击了取消
          ElMessage({
            type: 'info',
            message: '取消退出',
          })
        })
  }else{
    //路由
    router.push('/user/'+command)
  }
}
</script>

<template>
  <!-- element-plus中的容器-->
  <el-container class="layout-container">
    <!-- 左侧菜单 -->
    <el-aside width="200px">
      <div class="el-aside__logo"></div>
      <!--element-plus中的菜单标签-->
      <el-menu active-text-color="#f3d19e" background-color="#337ecc"  text-color="#f3d19e"
               router>
        <el-menu-item index="/index">
          <el-icon>
            <HomeFilled />
          </el-icon>
          <span>首页</span>
        </el-menu-item>
        <el-menu-item index="/article/category">
          <el-icon>
            <Management />
          </el-icon>
          <span>我的书架</span>
        </el-menu-item>
        <el-menu-item index="/article/manage">
          <el-icon>
            <Promotion />
          </el-icon>
          <span>我的文章</span>
        </el-menu-item>
        <el-sub-menu >
          <template #title>
            <el-icon>
              <UserFilled />
            </el-icon>
            <span>个人中心</span>
          </template>
          <el-menu-item index="/user/info">
            <el-icon>
              <User />
            </el-icon>
            <span>基本资料</span>
          </el-menu-item>
          <el-menu-item index="/user/avatar">
            <el-icon>
              <Crop />
            </el-icon>
            <span>更换头像</span>
          </el-menu-item>
          <el-menu-item index="/user/resetPassword">
            <el-icon>
              <EditPen />
            </el-icon>
            <span>重置密码</span>
          </el-menu-item>
        </el-sub-menu>
      </el-menu>
    </el-aside>
    <!-- 右侧主区域 -->
    <el-container>
      <!-- 头部区域 -->
      <el-header>
        <div>用户：<strong>{{userInfoStore.info.nickname}}</strong></div>
        <el-dropdown placement="bottom-end" @command="handleCommand">
                    <span class="el-dropdown__box">
                        <el-avatar :src="userInfoStore.info.userPic?userInfoStore.info.userPic:avatar" />
                        <el-icon>
                            <CaretBottom />
                        </el-icon>
                    </span>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item command="info" :icon="User">基本资料</el-dropdown-item>
              <el-dropdown-item command="avatar" :icon="Crop">更换头像</el-dropdown-item>
              <el-dropdown-item command="resetPassword" :icon="EditPen">重置密码</el-dropdown-item>
              <el-dropdown-item command="logout" :icon="SwitchButton">退出登录</el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>
      </el-header>
      <!-- 中间区域 -->
      <el-main>
<!--        <div style="width: 1290px; height: 570px;border: 1px solid red;">-->
<!--          内容展示区-->
<!--        </div>-->
        <router-view></router-view>
      </el-main>
      <!-- 底部区域 -->
      <el-footer>开发者:2058096897@qq.com</el-footer>
    </el-container>
  </el-container>
</template>

<style lang="scss" scoped>
.layout-container {
  height: 100vh;

  .el-aside {
    background-color: #337ecc;

    &__logo {
      height: 120px;
      background: url('@/assets/logo .png') no-repeat center / 120px auto;
    }

    .el-menu {
      border-right: none;
    }
  }

  .el-header {
    background-color: #fff;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .el-dropdown__box {
      display: flex;
      align-items: center;

      .el-icon {
        color: #999;
        margin-left: 10px;
      }

      &:active,
      &:focus {
        outline: none;
      }
    }
  }

  .el-footer {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
    color: #666;
  }
}
</style>