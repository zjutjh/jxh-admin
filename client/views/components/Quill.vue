<template>
  <div class="box">
    <label class="label">主题</label>
    <p class="control">
      <input class="input" type="text" placeholder="请输入标题" v-model="title">
    </p>
    <label class="label">发送者</label>
    <p class="control">
      <input class="input" type="text" placeholder="请输入发送方" v-model="informer">
    </p>
    <div ref="editor">
    </div>
    <p class="control">
      <button class="button is-success" @click="saveHandle">保存</button>
    </p>
    <card-modal :visible="visable" translate="zoom"  @ok="ensureHandle" @cancel="visable=!visable">
      <p>确定保存?</p>
    </card-modal>
    <div class="modal" v-show="loading">
      <div class="is-loading"></div>
    </div>

  </div>
</template>

<script>

  import E from 'wangeditor'
  import Vue from 'vue'
  import Message from 'vue-bulma-message'

  const MessageComponent = Vue.extend(Message)

  const openMessage = (propsData = {
    title: '',
    message: '',
    type: '',
    direction: '',
    duration: 1500,
    container: '.messages'
  }) => {
    return new MessageComponent({
      el: document.createElement('div'),
      propsData
    })
  }

  import { CardModal } from 'vue-bulma-modal'
  export default {
    components: {
      CardModal
    },
    data: () => (
      {
        content: '',
        title: '',
        informer: '',
        messageId: null,
        visable: false,
        editor: null,
        loading: false
      }
    ),
    mounted () {
      this.messageId = this.$route.query.messageId
      if (this.messageId !== -1) {
        this.getMessage()
      }
      this.editor = new E(this.$refs.editor)
      this.editor.customConfig.uploadImgServer = 'http://127.0.0.1:8000/message/upload'
      this.editor.customConfig.uploadFileName = 'file'
      this.editor.customConfig.onchange = (html) => {
        this.content = html
      }
      this.editor.create()
    },
    watch: {
      'content': function (newValue, oldValue) {
        console.log(this.content)
      }
    },
    methods: {
      saveHandle: function () {
        if (!this.content || !this.title || !this.informer) {
          openMessage({
            title: '',
            message: '不能为空',
            type: 'warning'
          })
          return
        }
        this.visable = !this.visable
      },
      ensureHandle: async function () {
        this.visable = !this.visable
        this.loading = !this.loading
        if (this.messageId !== -1) {
          this.update()
          return
        }
        this.save()
      },
      getMessage: async function () {
        await this.$http({
          url: '/message/' + this.messageId,
          method: 'POST'
        }).then((res) => {
          const data = res.body
          if (data.code < 0) {
            openMessage({
              title: '提示',
              message: data.msg ? data.msg : '出现一些错误',
              type: 'warning'
            })
            this.$route.back()
            return
          }
          this.editor.txt.html(data.data.message.content)
          this.content = data.data.message.content
          this.title = data.data.message.title
          this.informer = data.data.message.informer
        })
      },
      save: async function () {
        const params = {
          content: this.content,
          informer: this.informer,
          title: this.title
        }
        await this.$http({
          url: '/message/create',
          data: params,
          method: 'POST'
        }).then((res) => {
          const data = res.body
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
            message: data.msg ? data.msg : '保存成功',
            type: 'success'
          })
          this.$route.push('/message')
        }).catch((res) => {
          openMessage({
            title: '提示',
            message: '出现一些错误',
            type: 'warning'
          })
        })
      },
      update: async function () {
        const params = {
          content: this.content,
          informer: this.informer,
          title: this.title,
          id: this.messageId
        }
        await this.$http({
          url: '/message/upate',
          data: params,
          method: 'POST'
        }).then((res) => {
          const data = res.body
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
            message: data.msg ? data.msg : '保存成功',
            type: 'success'
          })
          this.$route.push('/message')
        }).catch((res) => {
          openMessage({
            title: '提示',
            message: '出现一些错误',
            type: 'warning'
          })
        })
      }
    }
  }
</script>

<style lang="styl">
  @import "~quill/assets/snow"
</style>
