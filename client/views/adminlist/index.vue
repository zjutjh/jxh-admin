<template>
  <div>
    <div class="tile is-ancestor">
      <div class="tile is-parent">
        <article class="tile is-child box">
          <h4 class="title">Table</h4>
          <router-link :to="{path: '/edit'}" tag="div" replace>
            <button class="button is-success">添加管理员</button>
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
    name: 'admins',
    components: {
      CardModal
    },
    mounted () {
      this.getAdmins()
    },
    data: () => ({
      admins: []
    }),
    methods: {
      getAdmins: function () {

      }
    }
  }
</script>

<style scoped>

</style>
