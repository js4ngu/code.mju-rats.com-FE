<template>
  <div class="status-wrap">
    <Row class="filter">
      <Col :offset="1" :span="5">
        <Col :span="6"><label>User</label></Col>
        <Col :span="15"><Input v-model="uid" placeholder="username"></Input></Col>
      </Col>
      <Col :span="4">
        <Col :span="6"><label>Pid</label></Col>
        <Col :span="15"><Input v-model="pid" placeholder="pid"></Input></Col>
      </Col>
      <Col :span="6">
        <Col :span="6"><label>Judge</label></Col>
        <Col :span="16">
          <Select v-model="judge" placeholder="请选择">
            <Option
              v-for="item in judgeList"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </Option>
          </Select>
        </Col>
      </Col>
      <Col :span="4">
        <Col :span="12"><label>Language</label></Col>
        <Col :span="12">
          <Select v-model="language" placeholder="请选择">
            <Option
              v-for="item in languageList"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </Option>
          </Select>
        </Col>
      </Col>
      <Col :span="3">
        <Button type="primary" @click="search" icon="search">Search</Button>
      </Col>
    </Row>
    <Row class="pagination">
      <Col :span="16">
        <Page :total="sum" @on-change="pageChange" :page-size="pageSize" :current.sync="page" show-elevator></Page>
      </Col>
    </Row>
    <table>
      <tr>
        <th>SID</th>
        <th>PID</th>
        <th>Username</th>
        <th>Judge</th>
        <th>Time/ms</th>
        <th>Memory/kb</th>
        <th>Language</th>
        <th>Submit Time</th>
      </tr>
      <tr v-for="(item, index) in list">
        <td>{{ item.sid }}</td>
        <td>
          <router-link :to="{ name: 'problemInfo', params: { pid: item.pid } }">
            {{ item.pid }}
          </router-link>
        </td>
        <td>
          <router-link :to="{ name: 'userInfo', params: { uid: item.uid } }">
            <Button type="text">{{ item.uid }}</Button>
          </router-link>
        </td>
        <td :class="color[item.judge]">
          {{ result[item.judge] }}
          <Tag color="yellow" v-if="item.sim">[{{ item.sim }}%]{{ item.sim_s_id }}</Tag>
        </td>
        <td>{{ item.time }}</td>
        <td>{{ item.memory }}</td>
        <td v-if="isAdmin || (profile && profile.uid === item.uid)">
          <router-link :to="{ name: 'solution', params: { sid: item.sid } }">
            {{ lang[item.language] }}
          </router-link>
        </td>
        <td v-else>{{ lang[item.language] }}</td>
        <td>{{ item.create | timePretty }}</td>
      </tr>
    </table>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import only from 'only'
import constant from '@/util/constant'
import { purify } from '@/util/helper'

export default {
  data () {
    return {
      uid: this.$route.query.uid || '',
      pid: this.$route.query.pid || '',
      judge: this.$route.query.judge || '',
      language: this.$route.query.language || '',
      page: parseInt(this.$route.query.page) || 1,
      pageSize: parseInt(this.$route.query.pageSize) || 30,
      judgeList: constant.judgeList,
      languageList: constant.languageList,
      result: constant.result,
      lang: constant.language,
      color: constant.color
    }
  },
  created () {
    this.fetch()
  },
  computed: {
    ...mapGetters({
      list: 'solution/list',
      sum: 'solution/sum',
      profile: 'session/profile',
      isAdmin: 'session/isAdmin'
    }),
    query () {
      const opt = only(this.$route.query, 'page pageSize uid pid language judge')
      return purify(opt)
    }
  },
  methods: {
    fetch () {
      this.$store.dispatch('solution/find', this.query)
      const query = this.$route.query
      this.page = parseInt(query.page) || 1
      this.pageSize = parseInt(query.pageSize) || 30
      this.uid = query.uid
      this.pid = query.pid || ''
      this.judge = query.judge || ''
      this.language = query.language || ''
    },
    reload (payload = {}) {
      this.$router.push({
        name: 'status',
        query: purify(Object.assign(this.query, payload))
      })
    },
    search (val) {
      this.reload({
        page: 1,
        uid: this.uid,
        pid: this.pid,
        language: this.language,
        judge: this.judge
      })
    },
    sizeChange (val) {
      this.reload({ pageSize: val })
    },
    pageChange (val) {
      this.reload({ page: val })
    }
  },
  watch: {
    '$route' (to, from) {
      if (to !== from) {
        this.fetch()
      }
    }
  }
}
</script>
<style lang="stylus" scoped>
@import '../styles/common'

.filter
  margin-bottom: 20px
  label
    height: 32px
    line-height: 32px
  .ivu-col
    text-align: center
  .ivu-select-item
    text-align: left
.pagination
  margin-bottom: 10px
table
  th:nth-child(1)
    width: 8%
  th:nth-child(2)
    width: 8%
  th:nth-child(3)
    width: 10%
  th:nth-child(4)
    width: 15%
  th:nth-child(5)
    width: 8%
  th:nth-child(6)
    width: 8%
  th:nth-child(7)
    width: 8%
  th:nth-child(8)
    width: 15%
</style>
