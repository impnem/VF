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
      <v-btn icon @click="save"><v-icon>mdi-check</v-icon></v-btn>
      <v-btn icon @click="read"><v-icon>mdi-numeric</v-icon></v-btn>
      <v-btn icon @click="readOne"><v-icon>mdi-account</v-icon></v-btn>
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
        menu: [],
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
        }
        this.site = v
      }, (e) => {
        console.log(e.message)
      })
    },
    save () { // 쓰기 테스트
      console.log('save@@@')
      const db = this.$firebaseDB.getDatabase()
      this.$firebaseDB.set(this.$firebaseDB.ref(db, 'abcd/'), {
        title: 'abcd',
        text: 'tttttt'
      })
    },
    read () { // 실시간 읽기 테스트
      const db = this.$firebaseDB.getDatabase()
      const starCountRef = this.$firebaseDB.ref(db, 'abcd/')
      this.$firebaseDB.onValue(starCountRef, (snapshot) => {
        console.log(snapshot)
        console.log(snapshot.val())
      })
    },
    // readOne () { // 한번만 읽기 테스트(관찰자 사용)
    //   const db = this.$firebaseDB.getDatabase()
    //   return this.$firebaseDB.onValue(this.$firebaseDB.ref(db, 'abcd/'), (snapshot) => {
    //     console.log(snapshot)
    //     console.log(snapshot.val())
    //   })
    // }
    async readOne () { // 한번만 읽기 테스트(get() 사용)
      const dbRef = this.$firebaseDB.ref(this.$firebaseDB.getDatabase())
      const sn = await this.$firebaseDB.get(this.$firebaseDB.child(dbRef, 'abcd/'))
      console.log(sn.val())
    }
  }
}
</script>
