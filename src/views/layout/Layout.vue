<template>
  <el-container style="height: 100%">
    <!-- 头部 -->
    <el-header
      style="text-align: right; font-size: 12px; background-color: whitesmoke"
    >
      
    </el-header>
    <el-container>
      <!-- 左侧栏 -->
      <el-aside width="200px">
        <el-menu>
          <!--如果菜单(menu)是true 循环侧栏路由列表  -->
          <template v-for="item in menuData" v-if="item.meta.menu">
            <!-- 这里必须设置index,相当唯一标识这个菜单标签，否则菜单列表点击后随意展开 -->
            <el-submenu :index="item.meta.funcNode" :key="item.meta.icon">
              <template slot="title"
                ><i :class="item.meta.icon"></i>{{ item.meta.title }}</template
              >
              <!-- 如果菜单有孩子菜单，则循环孩子菜单 -->
              <template v-for="itemC in item.children" v-if="item.children">
                <el-menu-item
                  :index="itemC.meta.funcNode"
                  @click="clickMenu(itemC)"
                  :key="itemC.meta.funcNode"
                  ><i :class="itemC.meta.icon"></i
                  >{{ itemC.meta.title }}</el-menu-item
                >
              </template>
            </el-submenu>
          </template>
        </el-menu>
      </el-aside>
      <!-- 内容渲染 -->
      <el-main style="background-color: white">
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>
 
 <script>
export default {
  data() {
    return {
      /*
          获取挂载的routes
         （$router相当于一个全局的路由器对象，里面含有很多属性和子对象 ）
         （options是一个对象，有成员：1、data函数成员  2、methods对象成员  3、模板template    4、挂载元素el
         5、生命周期钩子   6、props属性声明   7、computed计算成员    8、watch监视成员）
         */
      menuData: this.$router.options.routes,
      
    };
  },
  methods: {
    clickMenu(item) {
      this.$router.push({ path: item.path }); //跳转的路由对象
      //this.$router.push({name:item.name})    通过name跳转
    },
  },
};
</script>
 
 <style>
.el-header {
  background-color: #b3c0d1;
  color: #333;
  line-height: 60px;
}

.el-aside,
.el-menu,
.el-submenu,
.el-menu-item {
  background-color: rgb(238, 241, 246);
}

body {
  margin: 0px;
}
</style>	