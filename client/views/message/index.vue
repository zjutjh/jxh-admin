<template>
  <div>
    <div class="tile is-ancestor">
      <div class="tile is-parent">
        <article class="tile is-child box">
          <h4 class="title">Table</h4>
          <router-link :to="{path: '/edit'}" tag="div" replace>
            <button class="button is-success">添加模版消息</button>
          </router-link>
          <table class="table is-fullwidth">
            <thead>
            <tr>
              <th>id</th>
              <th>主题</th>
              <th>发送者</th>
              <th>文字缩略</th>
              <th>操作</th>
            </tr>
            </thead>
            <tfoot>
            <button class="button " @click="chagePage(true)">上一页</button>
            <button class="button " @click="chagePage(false)">下一页</button>
            </tfoot>
            <tbody>
            <tr v-for="(item, index) in messages" :key="item.id" >
            <td>{{ item.id}}</td>
            <td>{{ item.title}}</td>
            <td>{{ item.informer}}</td>
            <td>{{ item.content}}</td>
            <td class="is-icon">
            <button class="button is-primary" @click="sendMsg(index)">发送消息模版</button>
            <button class="button is-info" @click="edit(item.id)">修改</button>
            <button class="button is-success" @click="preCheck(item.id)">预览</button>
            </td>
            </tr>
            <!--<tr>-->
              <!--<td>dsf</td>-->
              <!--<td>sdf</td>-->
              <!--<td>sdf</td>-->
              <!--<td>sdf</td>-->
              <!--<td class="is-icon">-->
                <!--<button class="button is-primary" @click="sendMsg(1)">发送消息模版</button>-->

                <!--<button class="button is-info" @click="edit(1)">修改</button>-->
                <!--<button class="button is-success" @click="preCheck(1)">预览</button>-->
              <!--</td>-->
            <!--</tr>-->
            </tbody>
          </table>
        </article>
      </div>
      <card-modal :visible="enSure" :okText="'确认发送'" :cancelText="'取消发送'" translate="zoom" @ok="sureSend"
                  @cancel="enSure=!enSure">
        <label class="label">精弘通行证</label>
        <p class="control">
          <input v-model="username" class="input" type="text" placeholder="email@example.org">
        </p>
        <label class="label">密码</label>
        <p class="control">
          <input v-model="passwd" class="input" type="password" placeholder="password">
        </p>
      </card-modal>
    </div>

  </div>
</template>

<script>
  import Vue from 'vue'
  import Message from 'vue-bulma-message'
  import {CardModal} from 'vue-bulma-modal'

  const MessageComponent = Vue.extend(Message)

  const openMessage = (propsData = {
    title: '',
    message: '',
    type: '',
    direction: '',
    duration: 1500,
    container: '.messages',
    sendId: -1
  }) => {
    return new MessageComponent({
      el: document.createElement('div'),
      propsData
    })
  }
  export default {
    name: 'message',
    components: {
      CardModal
    },
    mounted () {
      this.getMessages()
    },
    data: () => ({
      lastPage: null,
      nextPage: null,
      messages: [],
      enSure: false,
      username: '',
      passwd: ''
    }),
    methods: {
      getMessages: function () {
        this.$http({
          method: 'get',
          url: 'http://jxh.jh.zjut.edu.cn/messages'
        }).then((res) => {
          console.log(res)
          const result = res.data
          if (result.code < 0) {
            openMessage({
              title: '提示',
              message: result.msg ? result.msg : '出现一些错误',
              type: 'warning'
            })
            return
          }
          const data = result.data
          this.nextPage = data.next_page_url
          this.lastPage = data.last_page_url
          this.messages = data.data
        })
      },
      chagePage: function (type) {
        let _this = this
        let changeUrl = ''
        if (type) {
          changeUrl = this.lastPage
        } else {
          changeUrl = this.nextPage
        }
        this.$http({
          method: 'get',
          url: changeUrl
        }).then((res) => {
          const result = res.data
          if (result.code < 0) {
            openMessage({
              title: '提示',
              message: result.msg ? result.msg : '出现一些错误',
              type: 'warning'
            })
            return
          }
          const data = result.data
          _this.nextPage = data.next_page_url
          _this.lastPage = data.last_page_url
          _this.messages = data.data
        })
      },
      sendMsg: function (id) {
        console.log(id)
        if (this.messages[id].is_send) {
          openMessage({
            title: '提示',
            message: '已经发送过',
            type: 'warning'
          })
          return
        }
        this.enSure = !this.enSure
        this.sendId = this.messages[id].id
      },
      edit: function (id) {
        this.$router.push({path: '/edit', query: {messageId: id}})
      },
      preCheck: function (id) {
        this.$http({
          url: 'http://jxh.jh.zjut.edu.cn/message/pre/' + id,
          method: 'GET'
        }).then((res) => {
          const data = res.data
          if (data.code < 0) {
            openMessage({
              title: '提示',
              message: data.msg ? data.msg : '出现一些错误',
              type: 'warning'
            })
            return
          }
          openMessage({
            title: '提示',
            message: data.msg ? data.msg : '预览已发送',
            type: 'success'
          })
        })
      },
      sureSend: function () {
        const params = {
          username: this.username,
          passwd: this.passwd
        }
        let _this = this
        this.$http({
          url: 'http://jxh.jh.zjut.edu.cn/message/send/' + this.sendId,
          method: 'POST',
          data: params
        }).then((res) => {
          const data = res.data
          if (data.code < 0) {
            openMessage({
              title: '提示',
              message: data.msg ? data.msg : '出现一些错误',
              type: 'warning'
            })
            return
          }
          openMessage({
            title: '提示',
            message: data.msg ? data.msg : '发送成功',
            type: 'success'
          })
          _this.sendId = -1
          _this.enSure = !this.enSure
          _this.getMessages()
        })
      }
    }
  }
</script>

<style scoped>

</style>
