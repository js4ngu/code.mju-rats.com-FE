<template>
  <div class="proinfo-wrap">
    <slot name="title"><h1>{{ problem.pid }}:  {{ problem.title }}</h1></slot>
    <h5>Time Limit: {{ problem.time }}MS&nbsp;&nbsp;&nbsp;Memory Limit: {{ problem.memory }}KB</h5>
    <h2 class="text-primary">Description</h2>
    <div class="cont" v-html="problem.description"></div>
    <h2>Input</h2>
    <div class="cont" v-html="problem.input"></div>
    <h2>Output</h2>
    <div class="cont" v-html="problem.output"></div>
    <h2>Sample Input
      <Tooltip content="Click to copy" placement="top">
        <Icon type="document" v-clipboard:copy="problem.in" v-clipboard:success="onCopy" style="cursor: pointer"></Icon>
      </Tooltip>
    </h2>
    <pre><code>{{ problem.in }}</code></pre>
    <h2>Sample Output
      <Tooltip content="Click to copy" placement="top">
        <Icon type="document" v-clipboard:copy="problem.out" v-clipboard:success="onCopy" style="cursor: pointer"></Icon>
      </Tooltip>
    </h2>
    <pre><code>{{ problem.out }}</code></pre>
    <div v-if="problem.hint">
      <h2>Hint</h2>
      <div class="cont" v-html="problem.hint"></div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['problem'],
  methods: {
    onCopy () {
      this.$Message.success('Copied!')
    }
  }
}
</script>
<style lang="stylus" scoped>
h1
  color: #757575
  margin-top: 10px
  margin-bottom: 8px
  text-align: center
h5
  margin-bottom: 10px
  text-align:center
h2
  border-bottom: 1px solid #e8e8e8
  padding: 10px 0
  color: #757575
.cont
  margin-top: 10px
  margin-bottom: 20px
pre
  padding: 10px
  border-radius: 5px
  background-color: #ECEFF1
</style>
