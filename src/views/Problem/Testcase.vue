<template>
  <div>
    <h1>Test Data</h1>
    <table>
      <tr>
        <th>#</th>
        <th>Test in</th>
        <th>Test out</th>
        <th>Delete</th>
      </tr>
      <tr v-for="item in list.testcases">
        <td>{{ item.uuid.slice(0, 8) }}</td>
        <td><a :href="testcaseUrl(item, 'in')" target="_blank" @click="search(item)">test.in</a></td>
        <td><a :href="testcaseUrl(item, 'out')" target="_blank">test.out</a></td>
        <td>
          <Button type="text" @click="del(item)">Delete</Button>
        </td>
      </tr>
    </table>
    <h1>Create New</h1>
    <p>In</p>
    <Input v-model="test.in" type="textarea" :autosize="{minRows: 5,maxRows: 25}"></Input>
    <p>Out</p>
    <Input v-model="test.out" type="textarea" :autosize="{minRows: 5,maxRows: 25}"></Input>
    <br>
    <br>
    <Button type="primary" @click="create"> Submit </Button>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import { testcaseUrl } from '@/util/helper'

export default {
  data: () => ({
    test: {
      pid: '',
      in: '',
      out: ''
    }
  }),
  computed: {
    ...mapGetters('testcase', ['list', 'testcase'])
  },
  created () {
    this.fetch()
    this.test.pid = this.$route.params.pid
  },
  methods: {
    fetch () {
      this.$store.dispatch('testcase/find', this.$route.params)
    },
    search (item) {
      const testcase = {
        pid: this.$route.params.pid,
        uuid: item.uuid,
        type: 'in'
      }
      this.$store.dispatch('testcase/findOne', testcase)
    },
    del (item) {
      this.$Modal.confirm({
        title: '알림',
        content: '<p>파일을 삭제하사겠습니까?</p>',
        onOk: () => {
          const testcase = {
            pid: this.$route.params.pid,
            uuid: item.uuid
          }
          this.$store.dispatch('testcase/delete', testcase).then(() => {
            this.$Message.success(`삭제 완료, ${item.uuid}！`)
          })
        },
        onCancel: () => {
          this.$Message.info('삭제 취소 !')
        }
      })
    },
    create () {
      this.$store.dispatch('testcase/create', this.test).then(() => {
        this.$Message.success(`생성 완료 !`)
        this.fetch()
        this.test.in = ''
        this.test.out = ''
      })
    },
    testcaseUrl ({ uuid }, type) {
      return testcaseUrl(this.$route.params.pid, uuid, type)
    }
  }
}
</script>
<style lang="stylus" scoped>
@import '../../styles/common'
</style>
