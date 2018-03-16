<template>
  <div class="container">

    <!-- <div class="userinfo">
      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div> -->

    <div class="usermotto">
      <div class="user-motto">
        Todo-List(待办事项)
      </div>
      <div class="todo-form">
        <input type="text" v-model="motto">
        <button class="todo-add" @click="addItem()">添加</button>
      </div>
    </div>

    <div>
      <ul>
        <li class="list-item" v-for="item in items" :key="item.index">
          <span>{{item.value}}</span>丨
          <span @click="delItem(item.index)">完成</span>
        </li>
      </ul>
    </div>
    <div>
      <button 
        class="todo-del-all" 
        :disabled="!items.length"
        @click = delItem()
      >删除全部
      </button>
    </div>
      
    <!-- <form class="form-container">
      <input type="text" class="form-control" v-model="motto" placeholder="v-model" />
      <input type="text" class="form-control" v-model.lazy="motto" placeholder="v-model.lazy" />
    </form> -->

  </div>
</template>

<script>
import card from '@/components/card'

export default {
  data () {
    return {
      motto: '',
      userInfo: {},
      items: []
    }
  },

  components: {
    card
  },

  methods: {
    getUserInfo () {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: (res) => {
              this.userInfo = res.userInfo
            }
          })
        }
      })
    },
    addItem () {
      if (this.motto.trim()) {
        this.items.push({
          index: this.items.length + 1,
          value: this.motto
        })
      }
      this.motto = ''
      this.updateStorage()
    },
    delItem (key) {
      if (!key) {
        this.items = []
        this.updateStorage()
        return
      }
      this.items = this.items.filter(item => {
        return item.index !== key
      })
      this.updateStorage()
    },
    updateStorage () {
      wx.setStorage({
        key: 'todo-item',
        data: this.items
      })
    }
  },

  created () {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo()
    wx.getStorage({
      key: 'todo-item',
      success: res => {
        this.items = res.data
      }
    })
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
}
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

.usermotto {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.todo-form {
  display: flex;
  margin: .8rem auto;
}
.todo-form input {
  width: 3rem;
  margin: 2px .2rem 0 0;
  border: 1px solid #aaa;
  border-radius: .1rem;
}
.todo-add,
.todo-del-all {
  height: .7rem;
  width: 1.5rem;
  font-size: .3rem;
}
.list-item {
  display: flex;
}
.todo-del-all {
  margin-top: 1rem;
  width: 2rem;
}

</style>
