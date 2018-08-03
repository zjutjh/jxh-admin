<template>
  <card-modal :visible="visible" @close="close" :title="title" transition="zoom" @ok="ok">
    <div class="content has-text-centered">{{ content}}</div>

  </card-modal>
</template>

<script>
import { CardModal } from 'vue-bulma-modal'

export default {
  components: {
    CardModal
  },

  props: {
    visible: Boolean,
    title: String,
    content: String,
    ok: Function
  },

  data () {
    return {
    }
  },

  methods: {

    close () {
      this.$emit('close')
    }
  }
}
</script>
