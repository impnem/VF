<template>
  <div>
    <v-list-item>
      <v-list-item-content>
        <v-list-item-title class="text-h6">
          Menu
        </v-list-item-title>
        <v-list-item-subtitle>
          0.0.1
        </v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>

    <v-divider></v-divider>
    <v-list>
      <!-- 메인 아이템 불러오기 -->
      <v-list-group
        v-for="(item, i) in items"
        :key="i"
        v-model="item.active"
        :prepend-icon="item.icon"
        no-action
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>
              {{ item.title }}
              <v-btn @click="openDialogItem(i)" icon><v-icon>mdi-pencil</v-icon></v-btn>
            </v-list-item-title>
          </v-list-item-content>
        </template>

        <!-- 서브 아이템 불러오기 -->
        <v-list-item
          v-for="(subItem, j) in item.subItems"
          :key="j"
          :to="subItem.to"
        >
          <v-list-item-content>
            <v-list-item-title>
              {{ subItem.title }}
              <v-btn @click="openDialogSubItem(i, j)" icon><v-icon>mdi-pencil</v-icon></v-btn>
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <!-- 서브 아이템 추가하기 -->
        <v-list-item @click="openDialogSubItem(i, -1)">
          <v-list-item-icon>
            <v-icon>mdi-plus</v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title>추가하기</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-group>

      <!-- 메인 아이템 추가하기 -->
      <v-list-item @click="openDialogItem(-1)">
        <v-list-item-icon>
          <v-icon>mdi-plus</v-icon>
        </v-list-item-icon>
        <v-list-item-content>
          <v-list-item-title>추가하기</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>

    <!-- 메인 아이템 수정 dialog -->
    <v-dialog v-model="dialogItem" max-width="400">
      <v-card>
        <v-card-title>
          메인 아이템 수정
          <v-spacer></v-spacer>
          <v-btn @click="saveItem" icon color="success"><v-icon>mdi-content-save</v-icon></v-btn>
          <v-btn @click="dialogItem=false" icon><v-icon>mdi-close</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="2">
              <v-icon v-text="formItem.icon" required></v-icon>
            </v-col>
            <v-col cols="10">
              <v-text-field
                v-model="formItem.icon"
                label="mdi icon"
                outlined
                clearable
                required
              ></v-text-field>
            </v-col>
          </v-row>
          <v-text-field v-model="formItem.title" label="아이템 이름" outlined hide-details></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- 서브 아이템 수정 dialog -->
    <v-dialog v-model="dialogSubItem" max-width="400">
      <v-card>
        <v-card-title>
          서브 아이템 수정
          <v-spacer></v-spacer>
          <v-btn @click="saveSubItem" icon color=""><v-icon>mdi-content-save</v-icon></v-btn>
          <v-btn @click="dialogSubItem=false" icon><v-icon>mdi-close</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="formSubItem.title" label="메뉴 이름" outlined required></v-text-field>
          <v-text-field v-model="formSubItem.to" label="경로" outlined required></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  props: ['items'],
  data () {
    return {
      dialogItem: false,
      dialogSubItem: false,
      selectedItemIndex: 0, // 메인 아이템 제어용 변수
      selectedSubItemIndex: 0, // 서브 아이템 제어용 변수
      formItem: {
        icon: '',
        title: ''
      },
      formSubItem: {
        title: '',
        to: ''
      }
    }
  },
  methods: {
    async save () { // 전체 저장 함수
      try {
        const db = this.$firebaseDB.getDatabase()
        await this.$firebaseDB.set(this.$firebaseDB.ref(db, 'site/'), { // 통째로 하기 때문에 update대신 set 사용
          menu: this.items
        })
      } finally {
        this.dialogItem = false // 메인 아이템 저장 후 창 닫기
        this.dialogSubItem = false // 서브 아이템 저장 후 창 닫기
      }
    },
    openDialogItem (index) { // 메인 아이템 dialog 함수
      this.selectedItemIndex = index // 클릭한 dialog에 대한 index 기억하기
      console.log(index)
      if (index < 0) {
        this.formItem.icon = 'mdi-crosshairs-question' // 새로 메뉴를 만들다면 비워두기
        this.formItem.title = '' // 새로 메뉴를 만들다면 비워두기
      } else {
        this.formItem.icon = this.items[index].icon // 기존 메뉴라면 아이콘 이름 가져오기
        console.log(this.items[index].icon)
        this.formItem.title = this.items[index].title // 기존 메뉴라면 메뉴 이름 가져오기
      }
      this.dialogItem = true
    },
    async saveItem () { // 메인 아이템 저장 함수
      if (this.selectedItemIndex < 0) {
        this.items.push(this.formItem) // 현재 작성한 내용을 추가
      } else {
        this.items[this.selectedItemIndex].icon = this.formItem.icon // 현재 아이콘 가져오기
        this.items[this.selectedItemIndex].title = this.formItem.title // 현재 제목 가져오기
      }
      this.save()
    },
    openDialogSubItem (index, subIndex) { // 서브 아이템 dialog 함수
      this.selectedItemIndex = index // 클릭한 dialog에 대한 index 기억하기
      this.selectedSubItemIndex = subIndex // 클릭한 dialog에 대한 subIndex 기억하기
      if (subIndex < 0) {
        this.formSubItem.title = '' // 새로 서브 메뉴를 만들다면 비워두기
        this.formSubItem.to = '' // 새로 서브 메뉴를 만들다면 비워두기
      } else {
        this.formSubItem.title = this.items[index].subItems[subIndex].title // 기존 서브 메뉴라면 메뉴명 가져오기
        this.formSubItem.to = this.items[index].subItems[subIndex].to // 기존 서브 메뉴라면 경로 가져오기
      }
      this.dialogSubItem = true
    },
    async saveSubItem () { // 서브 아이템 저장 함수
      if (this.selectedSubItemIndex < 0) {
        if (!this.items[this.selectedItemIndex].subItems) {
          this.items[this.selectedItemIndex].subItems = []
        }
        this.items[this.selectedItemIndex].subItems.push({ title: this.formSubItem.title, to: this.formSubItem.to }) // 현재 작성한 내용을 추가
      } else {
        this.items[this.selectedItemIndex].subItems[this.selectedSubItemIndex].title = this.formSubItem.title // 현재 아이콘 가져오기
        this.items[this.selectedItemIndex].subItems[this.selectedSubItemIndex].to = this.formItem.to // 현재 경로 가져오기
      }
      this.save()
    }
  }
}
</script>

<style>

</style>
