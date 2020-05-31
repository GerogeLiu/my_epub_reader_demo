<template>
  <div class="about">
    <h1>This is an about page</h1>
    <span class="icon-back"></span>
    <div class="test">
      <input type="range" :value="num" ref="input1" class="input1" />
      <span>{{num}} %</span>
      <button @click="submit">提交</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      num: 0
      // input1: 0
    };
  },
  methods: {
    submit() {
      console.log("---test----");
      console.log("refs", this.$refs.input1);
    }
  }
};
</script>
<style lang="scss">
.input1 {
}
</style>
