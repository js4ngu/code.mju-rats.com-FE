<template>
  <div>
    <table>
      <tr>
        <th>Did</th>
        <th>Title</th>
        <th>Author</th>
        <th>Updated</th>
        <th v-if="isAdmin && canRemove">Action</th>
      </tr>
      <template v-for="item in list">
        <tr>
          <td>
            {{ item.did }}
          </td>
          <td> <router-link :to="{ name: 'discussInfo', params: { did: item.did } }">
            <Button type="text">{{ item.title }}</Button>
          </router-link></td>
          <td>
            <router-link :to="{ name: 'userInfo', params: { uid: item.uid } }">
              <Button type="text"> {{ item.uid }} </Button>
            </router-link>
          <td>
            {{ item.update | timeagoPretty }}
          </td>
          <td v-if="isAdmin && canRemove">
            <Button type="text" @click="del(item.did)"> Delete </Button>
          </td>
        </tr>
      </template>
    </table>
    <h3>Create New Thread</h3>
    <Form :model="form" label-position="right" :label-width="100" class="form">
      <FormItem label="Title">
          <Input v-model="form.title"></Input>
      </FormItem>
      <FormItem label="Content">
          <Input v-model="form.content" type="textarea" :autosize="{minRows: 2,maxRows: 20}"></Input>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="createNew" :loading="loading" :disabled="!isLogined">Submit</Button>
        <span v-if="!isLogined">Login to reply</span>
    </FormItem>
  </Form>
  </div>
</template>
<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  data () {
    return {
      form: {
        title: '',
        content: ''
      },
      loading: false
    }
  },
  created () {
    this.fetch()
  },
  computed: {
    ...mapGetters('discuss', [ 'list' ]),
    ...mapGetters('session', [ 'isLogined' ]),
    ...mapGetters({
      isAdmin: 'session/isAdmin',
      canRemove: 'session/canRemove'
    })
  },
  methods: {
    ...mapActions('discuss', [ 'find', 'create' ]),
    fetch () {
      this.find()
    },
    createNew () {
      if (!this.form.title || !this.form.content) {
        this.$Message.warning('Title or Content can not be empty')
        return
      }
      this.loading = true
      this.create(this.form).then((did) => {
        this.$router.push({
          name: 'discussInfo',
          params: {
            did
          }
        })
      }).catch(() => {
        this.loading = false
      })
    },
    del (did) {
      this.$Modal.confirm({
        title: '알림',
        content: '<p>파일을 삭제하시겠습니까?</p>',
        onOk: () => {
          this.$store.dispatch('discuss/delete', { did }).then(() => {
            this.$Message.success(`삭제 완료, ${did}！`)
          })
        },
        onCancel: () => {
          this.$Message.info('삭제 취소 !')
        }
      })
    }
  }
}
</script>
<style lang="stylus" scoped>
@import '../../styles/common'
h3
  margin-top: 20px
.form
  margin-top: 2em
</style>
