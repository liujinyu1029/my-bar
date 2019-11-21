<!--
 * @Author: 刘金宇
 * @Date: 2019-11-20
 * @Description: 转化周期下拉框 小组件
 -->
<template>
  <div class="circle-select">
    <el-popover
      placement="bottom-start"
      width="316"
      @after-leave="onHidePopView"
      v-model="isShow"
      trigger="click"
    >
      <!-- pop view -->
      <div>
        <!-- input -->
        <div style="display:flex;padding-bottom:7px;">
          <el-input
            style="flex:1;margin-right:10px;"
            size="medium"
            v-model="modelData.timeNum"
            placeholder="15"
          />
          <el-select v-model="modelData.timeUnitStr" placeholder="请选择" size="medium" style="flex:1">
            <el-option
              v-for="(item, index) in timeUnitList"
              :key="index"
              :label="item"
              :value="item">
            </el-option>
          </el-select>
        </div>
        <!-- 错误提示 -->
        <div v-if="errText" style="color:#D0021B;line-height:20px;">
          {{errText}}
        </div>
        <!-- 确定按钮 -->
        <el-button
          type="primary"
          style="width:100%;margin-top:8px;"
          @click="onClickOkBtn"
          :disabled="!!errText"
        >
          确认
        </el-button>
      </div>
      <!-- pop btn -->
      <div slot="reference" class="pop-btn">
        转化周期 {{modelData.timeNum}}{{modelData.timeUnitStr}}
      </div>
    </el-popover>
  </div>
</template>

<script>

export default {
  name: 'cilcle-select',
  props: {
    value: {
      type: Object,
      default() {
        return {
          timeNum: 15,
          timeUnitStr: '分钟',
        };
      },
    },
  },
  data() {
    return {
      isShow: false,
      modelData: {},
      emitDataLock: false, // 阻止mvvm过多的数据反馈
      timeUnitList: ['天', '小时', '分钟'],
      errText: '',
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
    modelData: {
      deep: true,
      handler() {
        this.verifiedTimeNum();
      },
    },
  },
  created() {
    this.syncModelData();
  },
  methods: {
    // 同步父级通过v-model传来的value
    syncModelData() {
      if (this.emitDataLock) {
        this.emitDataLock = false;
        return;
      }
      this.modelData = JSON.parse(JSON.stringify(this.value || {}));
    },
    // 当隐藏pop弹窗时
    onHidePopView() {
      this.syncModelData();
    },
    // 转换时间为分钟单位
    transTimeNum() {
      const { timeNum, timeUnitStr } = this.modelData;
      if (timeUnitStr === '天') {
        return timeNum * 60 * 24;
      } else if (timeUnitStr === '小时') {
        return timeNum * 60;
      }
      return +timeNum;
    },
    // 输入框内容校验
    verifiedTimeNum() {
      const timeNum = +this.modelData.timeNum;
      if (!timeNum || timeNum < 0 || timeNum % 1) {
        this.errText = '请输入一个正整数';
      } else if (this.transTimeNum() > 60 * 24 * 30) {
        this.errText = '不能超过30天';
      } else {
        this.errText = '';
      }
    },
    onClickOkBtn() {
      const { modelData } = this;
      modelData.timeNum = +modelData.timeNum;
      this.$emit('input', modelData);
      this.emitDataLock = true;
      this.isShow = false;
    },
  },
}
</script>

<style scoped lang="scss">
.circle-select{
  .pop-btn{
    border: 1px solid #DCDFE6;
    border-radius: 4px;
    width: 160px;
    height: 36px;
    line-height: 36px;
    text-align: center;
    color:rgba(0,0,0,0.65);
    cursor: pointer;
  }
}
</style>