<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <site-title :title="site.title"></site-title>
      <v-spacer></v-spacer>
    </v-app-bar>
    <v-navigation-drawer app v-model="drawer">
      <site-menu :items="site.menu"></site-menu>
    </v-navigation-drawer>
    <v-main>
      <router-view></router-view>
    </v-main>
    <site-footer :footer="site.footer"></site-footer>
  </v-app>
</template>

<script>
import SiteTitle from '@/views/site/titleView.vue'
import SiteFooter from '@/views/site/footerView.vue'
import SiteMenu from '@/views/site/menuView.vue'

export default {

  components: { SiteTitle, SiteFooter, SiteMenu },
  name: 'App',
  data () {
    return {
      drawer: false,
      site: {
        menu: [
          {
            title: 'home',
            icon: 'mdi-home',
            subItems: [
              {
                title: 'Dashboard',
                to: '/'
              },
              {
                title: 'About',
                to: '/about'
              }
            ],

            to: '/'
          },
          {
            title: 'about',
            active: true,
            icon: 'mdi-account',
            subItems: [
              {
                title: 'xxx',
                to: '/xxx'
              }
            ],
            to: '/about'
          }
        ],
        title: '나의 타이틀입니다',
        footer: '푸터입니다'
      }
    }
  },
  created () {
    this.subscribe()
  },
  methods: {
    subscribe () {
      const db = this.$firebaseDB.getDatabase()
      const starCountRef = this.$firebaseDB.ref(db, 'site/')
      this.$firebaseDB.onValue(starCountRef, (snapshot) => {
        const v = snapshot.val()
        if (!v) { // DB에 site값이 없을 경우 현재의 site 객체를 넣는다.
          this.$firebaseDB.set(this.$firebaseDB.ref(db, 'site/'), this.site)
          return
        }
        this.site = v
      }, (e) => {
        console.log(e.message)
      })
    }
  }
}
</script>
