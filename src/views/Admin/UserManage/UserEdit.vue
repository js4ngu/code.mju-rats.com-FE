<template>
  <div>
    <h1>사용자 정보 수정</h1>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">Username</Col>
      <Col :span="4">
        <Input v-model="uid"></Input>
      </Col>
      <Col :offset="1" :span="2">
        <Button type="primary" @click="findUser">Search</Button>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">Nick</Col>
      <Col :span="21">
        <Input v-model="user.nick"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">Motto</Col>
      <Col :span="21">
        <Input v-model="user.motto"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">School</Col>
      <Col :span="21">
        <Input v-model="user.school"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">Password</Col>
      <Col :span="21">
        <Input v-model="newPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">CheckPwd</Col>
      <Col :span="21">
        <Input v-model="checkPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
      </Col>
    </Row>
    <Button type="primary" @click="saveUser" class="submit">Submit</Button>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { purify } from '@/util/helper'

export default {
  data: () => ({
    uid: '',
    newPwd: '',
    checkPwd: ''
  }),
  computed: {
    ...mapGetters({
      user: 'user/user'
    })
  },
  created () {
  },
  methods: {
    findUser () {
      this.$store.dispatch('user/findOne', { uid: this.uid })
    },
    saveUser () {
      if (this.newPwd === this.checkPwd) {
        const user = purify(Object.assign(
          this.user,
          { newPwd: this.newPwd }
        ))
        this.$store.dispatch('user/update', user).then(() => {
          this.$Message.success('업데이트 완료 !')
        })
      } else {
        this.$Message.info('비밀번호가 서로 일치하지 않습니다 ！')
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
.ivu-col-offset-1
  margin-left: 1%
</style>
