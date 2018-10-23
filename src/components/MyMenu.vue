<template>
    <div>
      <div class="logo" />
      <a-menu theme="dark" :defaultSelectedKeys="['home']" mode="inline" v-model="selectKeys" @click="handleClick">
        <template v-for="menu in menus">
          <a-sub-menu
            :key="menu.route.name"
            v-if="menu.route.children && menu.show"
          >
            <span slot="title">
              <a-icon :type="menu.route.meta.icon" />
              <span v-text="menu.route.meta.text"></span>
            </span>
            <a-menu-item 
              v-for="submenu in menu.route.children" 
              :key="submenu.name"
              v-if="submenu.show"
            >{{ submenu.meta.text }}</a-menu-item>
          </a-sub-menu>
          <a-menu-item 
            :key="menu.route.name"
            v-if="menu.show && !menu.route.children"
          >
            <a-icon :type="menu.route.meta.icon" />
            <span v-text="menu.route.meta.text"></span>
          </a-menu-item>
        </template>
      </a-menu>
    </div>
</template>

<script>
// import { menuConfig } from "../router";
import { mapState } from "vuex";

export default {
  data() {
    return {
      selectKeys: []
    }
  },
  computed: mapState(['menus']),
  mounted() {
    console.log(this.menus); // eslint-disable-line
    // 解决高亮bug
    window.resetSelectKeys = () => {
      setTimeout(() => {
        this.selectKeys = []
      }, 1);
    }
  },
  methods: {
    handleClick(e) {
      // console.log('click', e);
      this.$router.push({name: e.key});
    }
  }
}
</script>