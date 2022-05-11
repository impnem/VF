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
      <v-btn icon v-on:click="save"><v-icon>mdi-check</v-icon></v-btn>
      <v-btn icon v-on:click="read"><v-icon>mdi-numeric</v-icon></v-btn>
      <v-btn icon v-on:click="readOne"><v-icon>mdi-account</v-icon></v-btn>
    </v-app-bar>
    <v-navigation-drawer app v-model="drawer">
      <site-menu></site-menu>
    </v-navigation-drawer>
    <v-main>
      <router-view></router-view>
    </v-main>
    <site-footer :footer="footer"></site-footer>
  </v-app>
</template>

<script>
import SiteTitle from '@/views/site/titleView.vue'
import SiteFooter from '@/views/site/footerView.vue'
import SiteMenu from '@/views/site/menuView.vue'
import { getDatabase, ref, set, onValue } from 'firebase/database'

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
  },
  mounted () {
    console.log(this.$firebase)
  },
  methods: {
    save () { // 쓰기 테스트
      console.log('save@@@')
      const db = getDatabase()
      set(ref(db, 'abcd/'), {
        title: 'abcd',
        text: 'tttttt'
      })
    },
    read () { // 실시간 읽기 테스트
      const db = getDatabase()
      const starCountRef = ref(db, 'abcd/')
      onValue(starCountRef, (snapshot) => {
        console.log(snapshot)
        console.log(snapshot.val())
      })
    },
    readOne () { // 한번만 읽기 테스트(관찰자 사용)
      const db = getDatabase()
      return onValue(ref(db, 'abcd/'), (snapshot) => {
        console.log(snapshot)
        console.log(snapshot.val())
      })
    }
  }
}
</script>
