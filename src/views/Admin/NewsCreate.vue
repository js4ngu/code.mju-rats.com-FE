<template>
  <div>
    <h1>새로운 소식</h1>
    <oj-news-edit></oj-news-edit>
    <Button type="primary" size="large" @click="submit">Submit</Button>
  </div>
</template>
<script>
import NewsEdit from '@/components/NewsEdit'
import { mapGetters } from 'vuex'

export default {
  data: () => ({
    addNews: {
      title: '',
      content: ''
    }
  }),
  computed: {
    ...mapGetters('news', [
      'news'
    ])
  },
  created () {
    this.$store.commit('news/UPDATE_NEWS', this.addNews)
  },
  methods: {
    submit () {
      if (!this.news.title) {
        this.$Message.error('Title can not be empty')
      } else {
        this.$store.dispatch('news/create', this.news)
          .then((nid) => {
            this.$Message.success(`News "${this.news.title}" has been created!`)
            this.$router.push({ name: 'newsInfo', params: { nid } })
          })
      }
    }
  },
  components: {
    'oj-news-edit': NewsEdit
  }
}
</script>
<style lang="stylus" scoped>
h1
  margin-bottom: 20px
.ivu-btn
  margin-top: 20px
</style>
