<template>
  <div class="home-wrap">
    <div class="news">RATS 공지사항</div>
    <Card v-for="(item, index) in list" :key="item.nid">
      <Row type="flex" align="middle">
        <Col :span="2">
          <Icon type="chatbox-working"></Icon>
        </Col>
        <Col :span="20">
          <router-link :to="{ name: 'newsInfo', params: { nid: item.nid } }">
            <span>{{ item.title }}</span>
          </router-link>
          <p>{{ item.create | timePretty }}</p>
        </Col>
        <Col :span="2">
          <Icon v-if="isAdmin && canRemove" type="close-circled" @click.native="del(item.nid)"></Icon>
        </Col>
      </Row>
    </Card>
    <Page :total="sum" @on-change="pageChange" :page-size="pageSize" :current.sync="page" show-elevator></Page>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import { purify } from '@/util/helper'
import only from 'only'

export default {
  data () {
    return {
      currentPage: 1,
      pageSize: 5
    }
  },
  computed: {
    ...mapGetters({
      list: 'news/list',
      sum: 'news/sum',
      isAdmin: 'session/isAdmin',
      canRemove: 'session/canRemove'
    }),
    query () {
      const opt = only(this.$route.query, 'page pageSize')
      return purify(opt)
    }
  },
  created () {
    this.fetch()
  },
  methods: {
    fetch () {
      this.$store.dispatch('news/find', this.query)
      const query = this.$route.query
      this.page = parseInt(query.page) || 1
      if (query.pageSize) this.pageSize = parseInt(query.pageSize)
    },
    reload (payload = {}) {
      const query = Object.assign(this.query, purify(payload))
      this.$router.push({
        name: 'home',
        query
      })
    },
    pageChange (val) {
      this.reload({ page: val })
    },
    del (nid) {
      this.$Modal.confirm({
        title: '알림',
        content: '<p>메시지를 삭제하시겠습니까?</p>',
        onOk: () => {
          this.$store.dispatch('news/delete', { nid }).then(() => {
            this.$Message.success(`삭제 완료 ${nid}！`)
            this.$router.push({
              name: 'home',
              query: { page: this.page }
            })
          })
        },
        onCancel: () => {
          this.$Message.info('삭제 취소！')
        }
      })
    }
  },
  watch: { // 浏览器后退时回退页面
    '$route' (to, from) {
      if (to !== from) this.fetch()
    }
  }
}
</script>
<style lang="stylus">
@import '../styles/common'

.home-wrap
  margin: 0 10%
  .news
    font-size: 40px
    padding-bottom: 10px
    // margin-bottom: 20px
    // border-bottom: 1px solid #dfd8d8
  .content
    padding-left: 20px
    padding-bottom: 20px
    margin-bottom: 20px
    border-bottom: 1px solid #dfd8d8
  .ivu-card
    margin-bottom: 20px
    .ivu-icon-chatbox-working
      font-size: 24px
      margin-left: 30%
      color: alpha($primary-color, 0.9)
    p
      margin-top: 10px
    .ivu-icon-close-circled
      line-height: 20px
      color: #c3c2c2
      cursor: pointer
      &:hover
        font-size: 20px
</style>
