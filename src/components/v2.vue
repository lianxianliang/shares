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
      <el-input-number
        v-model="domain.price"
        :precision="2"
        :step="0.1"
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
        :step="0.1"
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

  </el-form>
</template>

<script>
export default {
  data() {
    return {
      dynamicValidateForm: {
        domains: [{
          price: 10,
          shares: 100
        }],
        email: ''
      },
      all: {
        price: 10,
        shares: 100,
        money: 1000
      }
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
        shares: 100,
        key: Date.now()
      });
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
  }
}
</style>

