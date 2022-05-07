<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >

      <v-app-bar-nav-icon v-on:click="drawer = !drawer"></v-app-bar-nav-icon>

      <site-title :title="title"></site-title>

      <v-spacer></v-spacer>

    </v-app-bar>

    <v-navigation-drawer app v-model="drawer">
      <site-menu></site-menu>
    </v-navigation-drawer>

    <v-content>
      <router-view></router-view>
    </v-content>

    <site-footer :footer="footer"></site-footer>

  </v-app>
</template>

<script>
import SiteTitle from '@/views/site/titleView.vue'
import SiteFooter from '@/views/site/footerView.vue'
import SiteMenu from '@/views/site/menuView.vue'

export default {
  components: {
    SiteTitle,
    SiteFooter,
    SiteMenu
  },
  name: 'App',
  data () {
    return {
      drawer: false,
      title: '나의 타이틀입니다',
      footer: '푸터입니다'
    }
  }
}
</script>
