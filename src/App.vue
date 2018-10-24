<template>
  <div>
    <a-locale-provider :locale="locale">
      <a-layout id="app" style="min-height: 100vh">
        <a-layout-sider
          collapsible
          v-model="collapsed"
          :style="{ overflow: 'auto', height: '100vh', position: 'fixed', left: 0 }"
        >
          <my-menu></my-menu>
        </a-layout-sider>
        <a-layout :style="{ marginLeft: '200px' }">
          <a-layout-header style="background: #fff; padding: 0 16px">
            <a-tag v-for="tag in openTags" closable @click="backUrl(tag.path)" @close="closeTag(tag.path, $event)" :key="tag.path">{{ tag.text }}</a-tag>
          </a-layout-header>
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
            <!-- <router-view></router-view> -->
            <!-- 子路由里面的 router-view 管不着 -->
            <keep-alive :exclude="['user-list-view']">
              <router-view></router-view>
            </keep-alive>
          </a-layout-content>
          <a-layout-footer style="text-align: center">
            Ant Design ©2018 Created by Ant UED
          </a-layout-footer>
        </a-layout>
      </a-layout>
    </a-locale-provider>
  </div>
</template>

<script>
import MyMenu from "@/components/MyMenu.vue";
import zhCN from 'ant-design-vue/lib/locale-provider/zh_CN';
import { mapState } from "vuex";

export default {
  name: 'app',
  components: {
    MyMenu
  },
  data() {
    return {
      collapsed: false,
      breadcrumbs: [],
      locale: zhCN,
    }
  },
  computed: mapState(['openTags']),
  watch: {
    '$route': function(to, from){ // eslint-disable-line
      this.breadcrumbs = to.matched.map(route => ({name: route.name, ...route.meta}))
    }
  },
  methods: {
    closeTag(path, e) {
      e.preventDefault()
      this.$store.commit('removeTag', path);
    },
    backUrl(path){
      this.$router.push(path);
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
