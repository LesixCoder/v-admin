<template>
  <a-layout id="app" style="min-height: 100vh">
    <a-layout-sider
      collapsible
      v-model="collapsed"
    >
      <my-menu></my-menu>
    </a-layout-sider>
    <a-layout>
      <a-layout-header style="background: #fff; padding: 0" />
      <a-layout-content style="margin: 0 16px">
        <a-breadcrumb style="margin: 16px 0">
          <a-breadcrumb-item v-for="breadcrumb in breadcrumbs" :key="breadcrumb.name"> 
            <template v-if="breadcrumb.no_breadcrumb_link">
              {{ breadcrumb.text }}
            </template>
            <template v-else>
              <router-link :to="{name: breadcrumb.name}">{{ breadcrumb.text }}</router-link>
            </template>
          </a-breadcrumb-item>
        </a-breadcrumb>
        <router-view></router-view>
      </a-layout-content>
      <a-layout-footer style="text-align: center">
        Ant Design ©2018 Created by Ant UED
      </a-layout-footer>
    </a-layout>
  </a-layout>
</template>

<script>
import MyMenu from "@/components/MyMenu.vue";

export default {
  name: 'app',
  components: {
    MyMenu
  },
  data() {
    return {
      collapsed: false,
      breadcrumbs: []
    }
  },
  watch: {
    '$route': function(to, from){ // eslint-disable-line
      this.breadcrumbs = to.matched.map(route => ({name: route.name, ...route.meta}))
    }
  }
}
</script>

<style>
#app .logo {
  height: 32px;
  background: rgba(255, 255, 255, 0.2);
  margin: 16px;
}
</style>
