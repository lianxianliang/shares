<template>
  <div class="content">

    <el-row>
      <el-col :span="8">
        <el-form
          ref="form"
          :model="form"
          label-width="180px"
        >
          <el-form-item label="本金">
            <el-input-number
              v-model="money"
              :precision="2"
              :step="0.01"
              :min="0"
            ></el-input-number>
          </el-form-item>

          <el-form-item label="定投次数">
            <el-input-number
              v-model="frequency"
              :precision="0"
              :step="1"
              :min="0"
              @change="handleChange"
            ></el-input-number>
          </el-form-item>

          <el-form-item label="开始买入价格">
            <el-input-number
              v-model="form.support_price"
              :precision="2"
              :step="0.01"
              :min="0"
              @change="handleChange"
            ></el-input-number>
          </el-form-item>

          <div class="forcast item1">
            <el-form-item
              label="预计终止价格"
              class="rateBox"
            >
              <el-input-number
                v-model="form.end_price_pre"
                :precision="2"
                :step="0.01"
                :min="0"
                @change="handleChange"
              ></el-input-number>
              <div class="rate price">( {{(((form.end_price_pre / form.support_price - 1) * 100)).toFixed(2) + "%"}} )</div>
            </el-form-item>

            <el-form-item
              label="预计卖出价格"
              class="rateBox"
            >
              <el-input-number
                v-model="form.sell_price_pre"
                :precision="2"
                :step="0.01"
                :min="0"
              ></el-input-number>
              <el-button
                class="rate"
                type="primary"
                icon="el-icon-refresh"
                circle
                @click="handleClickSellPrice"
                title="重置为系统默认预计卖出价格"
              ></el-button>
            </el-form-item>

            <el-form-item
              label="定投间隔"
              class="rateBox"
            >
              <el-input-number
                v-model="form.diff"
                :precision="2"
                :step="0.01"
                :min="0"
              ></el-input-number>
              <el-button
                class="rate"
                type="primary"
                icon="el-icon-refresh"
                circle
                @click="handleClickDiff"
                title="重置为系统默认定投间隔"
              ></el-button>
            </el-form-item>
          </div>

          <div class="real item1">
            <el-form-item
              label="实际终止价格"
              class="rateBox"
            >
              <el-input-number
                v-model="form.end_price_real"
                :precision="2"
                :step="0.01"
                :min="0"
                @change="handleChange"
              ></el-input-number>
              <div class="rate price">( {{(((form.end_price_real / form.support_price - 1) * 100)).toFixed(2) + "%"}} )</div>
            </el-form-item>

            <el-form-item
              label="实际卖出价格"
              class="rateBox"
            >
              <el-input-number
                v-model="form.sell_price_real"
                :precision="2"
                :step="0.01"
                :min="0"
              ></el-input-number>
              <el-button
                class="rate"
                type="primary"
                icon="el-icon-refresh"
                circle
                @click="handleClickSellPriceReal"
                title="重置为系统默认实际卖出价格"
              ></el-button>
            </el-form-item>
          </div>

          <el-form-item label="持有时间">
            <el-date-picker
              v-model="form.date"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            >
            </el-date-picker>
          </el-form-item>

          <el-form-item>
            <el-button
              type="primary"
              @click="onSubmit"
            >预测</el-button>
          </el-form-item>
        </el-form>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form
              label-width="180px"
              class="info forcast"
            >
              <el-form-item label="预计交易次数">
                <div>{{this.count_pre}}</div>
              </el-form-item>

              <el-form-item label="预计投入">
                <div>{{this.Principal_pre}}</div>
              </el-form-item>

              <el-form-item label="预计收益">
                <div>{{this.profit_pre.toFixed(0)}}</div>
              </el-form-item>

              <el-form-item label="预计收益率">
                <div>{{this.Principal_pre===0?'0':((this.profit_pre / this.Principal_pre) * 100).toFixed(2)}}%</div>
              </el-form-item>

              <el-form-item label="预计投资周期">
                <div>{{this.cycle_pre}}天</div>
              </el-form-item>

              <el-form-item label="预计日均">
                <div>{{this.cycle_pre===0?'0':(this.profit_pre / this.cycle_pre).toFixed(2)}}</div>
              </el-form-item>
            </el-form>
          </el-col>
          <el-col :span="12">
            <el-form
              label-width="180px"
              class="info real"
            >
              <el-form-item label="实际交易次数">
                <div>{{this.count_real}}</div>
              </el-form-item>

              <el-form-item label="实际投入">
                <div>{{this.Principal_real}}</div>
              </el-form-item>

              <el-form-item label="实际收益">
                <div>{{this.profit_real.toFixed(0)}}</div>
              </el-form-item>

              <el-form-item label="实际收益率">
                <div>{{this.Principal_real===0?'0':((this.profit_real / this.Principal_real) * 100).toFixed(2)}}%</div>
              </el-form-item>

              <el-form-item label="实际投资周期">
                <div>{{this.cycle_real}}天</div>
              </el-form-item>

              <el-form-item label="实际日均">
                <div>{{this.cycle_real===0?'0':(this.profit_real / this.cycle_real).toFixed(2)}}</div>
              </el-form-item>
            </el-form>
          </el-col>
        </el-row>
      </el-col>
      <el-col :span="16">
        <el-table
          :data="tableData"
          style="width: 100%"
          height="900"
        >
          <el-table-column
            prop="cur_shares"
            label="持有股票"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="Principal"
            label="投入"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="cost_price"
            label="成本价"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="buy_price"
            label="买入价"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="buy_shares"
            label="买入股票"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="rate_1"
            label="持仓盈亏"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="rate_2"
            label="自支撑位下跌"
            align="center"
          >
          </el-table-column>
          <!-- <el-table-column
            prop="rate_3"
            label="相差"
            align="center"
          >
          </el-table-column> -->
        </el-table>
      </el-col>
    </el-row>

    <div
      id="myChart"
      :style="{width: '100%', height: '420px'}"
    ></div>
  </div>

</template>

<script>
// 引入基本模板
let echarts = require('echarts/lib/echarts')
// 引入柱状图组件
require('echarts/lib/chart/line')
// 引入提示框和title组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')

export default {
  data() {
    return {
      xAxis: [],
      dataReal: [],
      dataPre: [],
      // 可动用资产
      money: 500000,
      // 定投次数
      frequency: 20,

      // 是否已到达预计终止买入价格
      isEndPre: false,
      // 是否已到达实际终止买入价格
      isEndReal: false,

      // 交易次数
      count: 0,
      // 成本价
      cost_price: 0,
      // 投入
      Principal: 0,
      // 利润
      profit: 0,
      // 持股
      cur_shares: 0,
      // 投资周期
      cycle: 0,

      // 预计交易次数
      count_pre: 0,
      // 预计成本价
      cost_price_pre: 0,
      // 预计投入
      Principal_pre: 0,
      // 预计利润
      profit_pre: 0,
      // 预计持股
      cur_shares_pre: 0,
      // 预计投资周期
      cycle_pre: 0,

      // 实际交易次数
      count_real: 0,
      // 实际成本价
      cost_price_real: 0,
      // 实际投入
      Principal_real: 0,
      // 实际利润
      profit_real: 0,
      // 实际持股
      cur_shares_real: 0,
      // 实际投资周期
      cycle_real: 0,

      form: {
        // 开始买入价格(支撑位)
        support_price: 10,
        // 预计终止价格
        end_price_pre: 7,
        // 实际终止价格
        end_price_real: 8,
        // 定投间隔
        diff: 0,
        // 预计卖出价格
        sell_price_pre: 0,
        // 实际卖出价格
        sell_price_real: 0,
        // 持有日期
        date: [new Date(), new Date(+new Date() + 6 * 24 * 60 * 60 * 1000)],
      },
      tableData: []
    }
  },
  mounted() {
    this.handleChange();
  },
  methods: {
    handleChange() {
      this.handleClickSellPrice();
      this.handleClickSellPriceReal();
      this.handleClickDiff();
    },
    // 重置为系统默认预计卖出价格
    handleClickSellPrice() {
      this.form.sell_price_pre = (this.form.end_price_pre + (this.form.support_price - this.form.end_price_pre) * 2 / 3).toFixed(2);
    },
    handleClickSellPriceReal() {
      this.form.sell_price_real = (this.form.end_price_real + (this.form.support_price - this.form.end_price_real) * 2 / 3).toFixed(2);
    },
    // 重置为系统默认定投间隔
    handleClickDiff() {
      this.form.diff = ((this.form.support_price - this.form.end_price_pre) / (this.frequency - 1)).toFixed(2) - 0;
    },
    onSubmit() {

      // 开始买入价格(支撑位)
      let support_price = this.form.support_price;
      // 开始买入时间
      let startDate = this.form.date[0];

      // 终止买入时间
      let endDate = this.form.date[1];

      // 投资周期
      this.cycle = (+new Date(endDate) - (+new Date(startDate))) / (1 * 24 * 60 * 60 * 1000) + 1;

      // 交易次数
      this.count = 0;
      // 成本价
      this.cost_price = 0;
      // 投入
      this.Principal = 0;
      // 持股
      this.cur_shares = 0;

      this.isEndPre = false;
      this.isEndReal = false;
      this.tableData = [];
      this.handleClickDiff();
      this.xAxis = [];
      this.dataReal = [];
      this.dataPre = [];
      this.cal(support_price + this.form.diff);

    },

    cal(cur_price) {
      // 交易次数
      this.count++;
      // 买入价
      let buy_price = (cur_price - this.form.diff).toFixed(2);

      let min = Math.min(this.form.end_price_pre, this.form.end_price_real);

      // 预计
      if (!this.isEndPre && buy_price < this.form.end_price_pre) {
        this.count_pre = this.count;
        this.cost_price_pre = this.cost_price;
        this.Principal_pre = this.Principal;
        // 市值
        let value = this.cur_shares * (this.form.sell_price_pre - this.cost_price);
        // 收益
        this.profit_pre = value - Math.ceil(value / 10000) * 5;
        this.cur_shares_pre = this.cur_shares;
        this.cycle_pre = this.cycle;

        this.isEndPre = true;

      }

      // 实际
      if (!this.isEndReal && buy_price < this.form.end_price_real) {
        this.count_real = this.count;
        this.cost_price_real = this.cost_price;
        this.Principal_real = this.Principal;
        // 市值
        let value = this.cur_shares * (this.form.sell_price_real - this.cost_price);
        // 收益
        this.profit_real = value - Math.ceil(value / 10000) * 5;
        this.cur_shares_real = this.cur_shares;
        this.cycle_real = this.cycle;

        this.isEndReal = true;
      }

      if (buy_price < min) {
        this.drawLine();
        return;
      }

      // 买入股票
      let buy_shares = Math.floor(10000 / buy_price / 100) * 100;
      if (buy_shares * buy_price < 10000) {
        buy_shares += 100;
      }

      // 当次投入
      let curPrincipal = buy_shares * buy_price;
      // 投入
      this.Principal = this.Principal + curPrincipal + Math.ceil(curPrincipal / 10000) * 5;
      if (buy_price <= 0 || this.Principal >= this.money) {
        this.drawLine();
        return;
      }

      // 持股
      this.cur_shares = this.cur_shares + buy_shares;
      // 成本价
      this.cost_price = this.Principal / this.cur_shares;
      // 持仓盈亏
      let rate_1 = ((buy_price / this.cost_price - 1) * 100).toFixed(2) + "%";
      // 自支撑位下跌
      let rate_2 = ((buy_price / this.form.support_price - 1) * 100).toFixed(2) + "%";

      let rate_3 = (((buy_price / this.cost_price - 1) * 100) - ((buy_price / this.form.support_price - 1) * 100)).toFixed(2) + "%";

      let obj = {
        buy_shares,
        cur_shares: this.cur_shares,
        Principal: this.Principal.toFixed(2),
        cost_price: this.cost_price.toFixed(3),
        rate: (buy_price - this.cost_price).toFixed(4),
        buy_price,
        rate_1,
        rate_2,
        rate_3,
      }

      this.xAxis.push(this.count);
      this.dataReal.push(obj.cost_price);
      this.dataPre.push(obj.buy_price);

      if (!this.isEndReal && buy_price - this.diff < 0) {
        obj.isEndReal = true;
      }

      this.tableData.push(obj);

      // this.form.diff = this.form.diff * 1.05;
      this.cal(buy_price);
    },

    drawLine() {
      // 基于准备好的dom，初始化echarts实例
      let myChart = echarts.init(document.getElementById('myChart'))
      // 绘制图表
      let option = {
        title: {
          text: ''
        },
        tooltip: {
          trigger: 'axis'
        },
        legend: {
          data: ['成本价', '买入价']
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        xAxis: {
          type: 'category',
          boundaryGap: false,
          data: this.xAxis
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            name: '成本价',
            type: 'line',
            data: this.dataReal
          },
          {
            name: '买入价',
            type: 'line',
            data: this.dataPre
          }
        ]
      };
      myChart.setOption(option);
    }

  }
}
</script>

<style lang="scss">
::-webkit-scrollbar {
  width: 3px;
  height: 3px;
}

::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: #1890ff;
  background-image: -webkit-linear-gradient(
    45deg,
    rgba(255, 255, 255, 0.2) 25%,
    transparent 25%,
    transparent 50%,
    rgba(255, 255, 255, 0.2) 50%,
    rgba(255, 255, 255, 0.2) 75%,
    transparent 75%,
    transparent
  );
}

::-webkit-scrollbar-track {
  border-radius: 10px;
}

.el-form-item {
  margin-bottom: 12px;
}
.content {
  padding: 12px;
}
.info {
  border-radius: 5px;
}
.rateBox {
  position: relative;
  .rate {
    position: absolute;
    right: 17%;
    top: 0;
    &.price {
      right: 13%;
      color: #20c120;
    }
  }
}
.item1 {
  overflow: auto;
  padding-top: 12px;
  border-radius: 5px;
  margin-bottom: 12px;
}
.forcast {
  background: #ededed;
}
.real {
  background: #f6b24d;
  color: #fff;
  .el-form-item__label {
    color: #fff;
  }
}
</style>
