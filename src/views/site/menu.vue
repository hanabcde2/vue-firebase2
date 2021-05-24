<template>
  <div>
    <v-list-item>
      <v-list-item-content>
        <v-list-item-title class="title">
          강희전용 사이트 만들기
        </v-list-item-title>
        <v-list-item-subtitle>
          강희 vue & firebase 공부
        </v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>
    <v-divider></v-divider>
    <v-list>
      <v-list-group
        v-for="(item, i) in items"
        :key="i"
        v-model="item.active"
        :prepend-icon="item.icon"
        no-action
      >
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title >{{item.title}}
              <v-btn @click="openDialogItem(i)" icon><v-icon>mdi-pencil</v-icon></v-btn>
            <v-btn @click="moveItem(items, i, -1)" v-if="i > 0" icon><v-icon>mdi-chevron-double-up</v-icon></v-btn>
            <v-btn @click="moveItem(items, i, 1)" v-if="i < items.length - 1" icon><v-icon>mdi-chevron-double-down</v-icon></v-btn>

            </v-list-item-title>
          </v-list-item-content>
          <v-list-item-action>

          </v-list-item-action>
        </template>

        <v-list-item
          v-for="(subItem, j) in item.subItems"
          :key="j"
          :to="subItem.to"
        >
          <v-list-item-content>
            <v-list-item-title>
              {{subItem.title}}
              <v-btn @click="openDialogSubItem(i, j)" icon><v-icon>mdi-pencil</v-icon></v-btn>
            <v-btn @click="moveItem(item.subItems, i, -1)" v-if="j > 0" icon><v-icon>mdi-chevron-double-up</v-icon></v-btn>
            <v-btn @click="moveItem(item.subItems, i, 1)" v-if="j < item.subItems.length - 1" icon><v-icon>mdi-chevron-double-down</v-icon></v-btn>

            </v-list-item-title>

          </v-list-item-content>
          <v-list-item-action>

          </v-list-item-action>
        </v-list-item>
        <v-list-item @click="openDialogSubItem(i,-1)">
          <v-list-item-icon>
            <v-icon>mdi-plus</v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title>서브추가하기</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-group>
      <v-list-item @click="openDialogItem(-1)">
        <v-list-item-icon>
          <v-icon>mdi-plus</v-icon>
        </v-list-item-icon>
        <v-list-item-content>
          <v-list-item-title>추가하기</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>
    <v-dialog v-model="dialogItem" max-width="400">
      <v-card>
        <v-card-title>
          수정하기
          <v-spacer/>
          <v-btn icon @click="saveItem" ><v-icon>mdi-content-save</v-icon></v-btn>
          <v-btn icon @click="dialogItem = false" @keypress.esc ="dialogItem = false" ><v-icon>mdi-close</v-icon></v-btn>
        </v-card-title>
        <v-card-text>

          <v-text-field v-model="formItem.title" label="타이틀" outlined required @keypress.enter="saveItem"></v-text-field>

          <v-text-field v-model="formItem.icon" label="아이콘" outlined required @keypress.enter="saveItem"></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogSubItem" max-width="400">
      <v-card>
        <v-card-title>
          수정하기
          <v-spacer/>
          <v-btn icon @click="saveSubItem()" ><v-icon>mdi-content-save</v-icon></v-btn>
          <v-btn icon @click="dialogSubItem = false" @keypress.esc="dialogSubItem = false"><v-icon>mdi-close</v-icon></v-btn>

        </v-card-title>
        <v-card-text>

          <v-text-field v-model="formSubItem.title" label="타이틀" outlined required></v-text-field>

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
      formItem: {
        icon: '',
        title: '',
        subItems: []

      },
      formSubItem: {
        to: '',
        title: ''
      },

      selectedItemIndex: -1,
      selectedSubItemIndex: -1

    }
  },
  methods: {
    openDialogItem (index) {
      this.selectedItemIndex = index
      this.dialogItem = true
      if (index < 0) {
        this.formItem.title = ''
        this.formItem.icon = ''
      } else {
        this.formItem.title = this.items[index].title
        if (this.items[index].icon) {
          this.formItem.icon = this.items[index].icon
        } else {
          this.formItem.icon = ''
        }
      }
    },
    openDialogSubItem (index, subIndex) {
      this.selectedItemIndex = index
      this.selectedSubItemIndex = subIndex
      this.dialogSubItem = true
      if (subIndex < 0) {
        this.formSubItem.title = ''
        this.formSubItem.to = ''
      } else {
        this.formSubItem.title = this.items[index].subItems[subIndex].title
        this.formSubItem.to = this.items[index].subItems[subIndex].to
      }
    },
    saveItem () {
      if (this.formItem.title) {
        if (this.selectedItemIndex < 0) {
          this.items.push(this.formItem)
        } else {
          if (this.items[this.selectedItemIndex].subItems) this.formItem.subItems = this.items[this.selectedItemIndex].subItems
          this.items[this.selectedItemIndex] = this.formItem
        }
        this.save()
      } else {
        alert('타이틀을 입력해주세요')
      }
    },
    saveSubItem () {
      if (this.formSubItem.title) {
        if (this.selectedSubItemIndex < 0) {
          if (!this.items[this.selectedItemIndex].subItems) this.items[this.selectedItemIndex].subItems = []
          this.items[this.selectedItemIndex].subItems.push({
            title: this.formSubItem.title,
            to: this.formSubItem.to
          })
        } else {
          this.items[this.selectedItemIndex].subItems[this.selectedSubItemIndex].title = this.formSubItem.title
          this.items[this.selectedItemIndex].subItems[this.selectedSubItemIndex].to = this.formSubItem.to
        }
        this.save()
      } else {
        alert('타이틀을 입력해주세요')
      }
    },
    async save () {
      try {
        await this.$firebase.database().ref().child('site').update({ menu: this.items })
      } finally {
        this.dialogItem = false
        this.dialogSubItem = false
      }
    },
    moveItem (items, i, arrow) {
      // const item = items.splice(i, 1)[0]
      // items.splice(i + arrow, 0, item)
      items.splice(i + arrow, 0, ...items.splice(i, 1))
      this.save()
    }

  }
}
</script>
