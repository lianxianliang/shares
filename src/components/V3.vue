<template>
  <el-form
    :model="dynamicValidateForm"
    ref="dynamicValidateForm"
    label-width="100px"
    class="demo-dynamic"
  >
    <el-form-item
      v-for="(domain, index) in dynamicValidateForm.domains"
      :label="'域名' + index"
      :key="domain.key"
    >
      <span
        class="percent"
        v-if="index>0"
        :class="(domain.price/dynamicValidateForm.domains[index-1].price-1)>0?'red':'green'"
      >{{((domain.price/dynamicValidateForm.domains[index-1].price-1)*100).toFixed(2)}}%</span>
      <el-input-number
        v-model="domain.price"
        :precision="2"
        :step="0.01"
        @change="submitForm()"
      ></el-input-number>
      <el-input-number
        v-model="domain.shares"
        :precision="0"
        :step="100"
        @change="submitForm()"
      ></el-input-number>
      <el-input-number
        :value="domain.price*domain.shares"
        disabled
      ></el-input-number>
      <el-button
        @click.prevent="removeDomain(domain)"
        class="delete"
        v-if="index>0"
      >删除</el-button>
    </el-form-item>

    <el-form-item>
      <el-button @click="addDomain">新增</el-button>
    </el-form-item>

    <el-form-item label="总结">
      <el-input-number
        v-model="all.price"
        :precision="2"
        :step="0.01"
        disabled
      ></el-input-number>
      <el-input-number
        v-model="all.shares"
        :precision="0"
        :step="100"
        disabled
      ></el-input-number>
      <el-input-number
        :value="all.money"
        disabled
      ></el-input-number>
    </el-form-item>

    <el-form-item label="">
      <span
        class="percent"
        :class="(end_price/all.price-1)>0?'red':'green'"
      >{{((end_price/all.price-1)*100).toFixed(2)}}%</span>
      <el-input-number
        v-model="end_price"
        :precision="2"
        :step="0.01"
      ></el-input-number>
      <el-input-number
        :value="(end_price-all.price)*all.shares"
        :precision="0"
        disabled
      ></el-input-number>
      <el-input-number disabled></el-input-number>
    </el-form-item>

    <el-form-item label="持有时间">
      <el-date-picker
        v-model="date"
        type="daterange"
        range-separator="至"
        start-placeholder="开始日期"
        end-placeholder="结束日期"
        @change="onChangeDate"
      >
      </el-date-picker>
    </el-form-item>

    <el-form-item label="实际投资周期">
      <div>{{cycle}}天</div>
    </el-form-item>

    <el-form-item label="实际日均">
      <div>{{cycle===0?'0':((end_price-all.price)*all.shares / cycle).toFixed(2)}}</div>
    </el-form-item>

  </el-form>
</template>

<script>
export default {
  data() {
    return {
      dynamicValidateForm: {
        domains: [{
          price: 10,
          shares: 5000
        }],
        email: ''
      },
      all: {
        price: 10,
        shares: 5000,
        money: 50000,
      },
      end_price: 10.3,
      date: [new Date(), new Date(+new Date() + 6 * 24 * 60 * 60 * 1000)],
      cycle: (+new Date(+new Date() + 6 * 24 * 60 * 60 * 1000) - (+new Date())) / (1 * 24 * 60 * 60 * 1000) + 1
    };
  },
  methods: {
    submitForm() {
      let price = 0;
      let shares = 0;
      let money = 0;
      this.dynamicValidateForm.domains.forEach(element => {
        shares += element.shares;
        money += element.price * element.shares;
      });
      price = (money / shares).toFixed(2);

      this.all = {
        price,
        shares,
        money
      }
    },
    removeDomain(item) {
      var index = this.dynamicValidateForm.domains.indexOf(item)
      if (index !== -1) {
        this.dynamicValidateForm.domains.splice(index, 1)
      }
      this.submitForm();
    },
    addDomain() {
      this.dynamicValidateForm.domains.push({
        price: 10,
        shares: 5000,
        key: Date.now()
      });
    },
    onChangeDate() {
      // 开始买入时间
      let startDate = this.date[0];

      // 终止买入时间
      let endDate = this.date[1];

      // 投资周期
      this.cycle = (+new Date(endDate) - (+new Date(startDate))) / (1 * 24 * 60 * 60 * 1000) + 1;

    }
  }
}
</script>

<style lang="scss" scoped>
.demo-dynamic {
  width: 75%;
  margin: 0 auto;
  margin-top: 20px;
  .el-form-item__content {
    .el-button.delete {
      position: absolute;
      right: -69px;
      top: 0;
    }
    .percent {
      position: absolute;
      left: 50%;
      top: 0;
      margin-left: -330px;
      &.green {
        color: #109e10;
      }
      &.red {
        color: #dc0f0f;
      }
    }
  }
}
</style>

