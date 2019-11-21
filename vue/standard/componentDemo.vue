<!--
 * @Author: 刘金宇
 * @Date: 2019-11-20
 * @Description: 组件模板格式
 -->
<template>
  <div class="model-cont">

  </div>
</template>

<script>
export default {
  name: 'component-demo',
  props: {
    value: {
      type: Object,
      default() {
        return {};
      },
    },
  },
  data() {
    return {
      modelData: {}, // 单流处理 用来接收父级的v-model
      emitDataLock: false, // 阻止mvvm过多的数据反馈
      
    };
  },
  computed: {
  },
  watch: {
    value: {
      deep: true,
      handler() {
        this.syncModelData();
      },
    },
  },
  // created 钩子
  created() {
    this.syncModelData();
  },
  methods: {
    // 单流接收 另存从父级v-model接过来的值
    syncModelData() {
      if (this.emitDataLock) {
        this.emitDataLock = false
        return;
      }
      this.modelData = JSON.parse(JSON.stringify(this.value||{}));
    },
    // 反馈数据
    emitModelData() {
      this.$emit('input', this.modelData);
      this.emitDataLock = true
    },
  },
  // 依赖的组件
  components:{}
}
</script>

<style scoped lang="scss">
.model-cont{

}
</style>