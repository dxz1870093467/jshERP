<template>
  <div class="page-header-index-wide">
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="今日累计销售">
          <a-tooltip title="统计今日零售和销售单据数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <head-info :content="statistics.todaySale"></head-info>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="本月累计销售">
          <a-tooltip title="统计本月零售和销售单据数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <head-info :content="statistics.thisMonthSale"></head-info>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="今日累计采购">
          <a-tooltip title="统计今日采购单据数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <head-info :content="statistics.todayBuy"></head-info>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="本月累计采购">
          <a-tooltip title="统计本月采购单据数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <head-info :content="statistics.thisMonthBuy"></head-info>
        </chart-card>
      </a-col>
    </a-row>
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="12" :style="{ marginBottom: '24px' }">
        <a-card :loading="loading" :bordered="false" :body-style="{paddingRight: '5'}">
          <bar title="销售统计" :yaxisText="yaxisText" :dataSource="salePriceData"/>
        </a-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="12" :style="{ marginBottom: '24px' }">
        <a-card :loading="loading" :bordered="false" :body-style="{paddingRight: '5'}">
          <bar title="采购统计" :yaxisText="yaxisText" :dataSource="buyPriceData"/>
        </a-card>
      </a-col>
    </a-row>
    <a-row :gutter="24">
      <a-col :sm="24" :md="24" :xl="24" :style="{ marginBottom: '5px' }">
        <a-card :bordered="false" :body-style="{padding: '5'}">
          <div class="hidden-xs" style="float:right;">&copy; 2015-2030 {{systemTitle}} V3.0</div>
          <a-tag v-if="tenant.type==0" color="blue">试用到期：{{tenant.expireTime}}</a-tag>
          <a-tag v-if="tenant.type==0" color="blue">试用用户：{{tenant.userCurrentNum}}/{{tenant.userNumLimit}}</a-tag>
          <a-tag v-if="tenant.type==1" color="blue">服务到期：{{tenant.expireTime}}</a-tag>
          <a-tag v-if="tenant.type==1" color="blue">授权用户：{{tenant.userCurrentNum}}/{{tenant.userNumLimit}}</a-tag>
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>
<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import MiniArea from '@/components/chart/MiniArea'
  import MiniBar from '@/components/chart/MiniBar'
  import MiniProgress from '@/components/chart/MiniProgress'
  import Bar from '@/components/chart/Bar'
  import LineChartMultid from '@/components/chart/LineChartMultid'
  import HeadInfo from '@/components/tools/HeadInfo.vue'
  import Trend from '@/components/Trend'
  import { getBuyAndSaleStatistics, buyOrSalePrice } from '@/api/api'
  import { getAction } from '../../api/manage'

  export default {
    name: "IndexChart",
    components: {
      ATooltip,
      ACol,
      ChartCard,
      MiniArea,
      MiniBar,
      MiniProgress,
      Bar,
      Trend,
      LineChartMultid,
      HeadInfo
    },
    data() {
      return {
        systemTitle: window.SYS_TITLE,
        systemUrl: window.SYS_URL,
        loading: true,
        center: null,
        statistics: {},
        yaxisText: '金额',
        buyPriceData: [],
        salePriceData: [],
        visitFields:['ip','visit'],
        visitInfo:[],
        tenant: {
          type: '',
          expireTime: '',
          userCurrentNum: '',
          userNumLimit: ''
        }
      }
    },
    created() {
      setTimeout(() => {
        this.loading = !this.loading
      }, 1000)
      this.initInfo()
      this.initWithTenant()
    },
    methods: {
      initInfo () {
        getBuyAndSaleStatistics(null).then((res)=>{
          if(res.code === 200){
            this.statistics = res.data;
          }
        })
        buyOrSalePrice(null).then(res=>{
          if(res.code === 200){
            this.buyPriceData = res.data.buyPriceList;
            this.salePriceData = res.data.salePriceList;
          }
        })
      },
      initWithTenant() {
        getAction("/user/infoWithTenant",{}).then(res=>{
          if(res && res.code === 200) {
            this.tenant = res.data
          }
        })
      }
    }
  }
</script>
<style lang="less" scoped>
  .circle-cust{
    position: relative;
    top: 28px;
    left: -100%;
  }
  .extra-wrapper {
    line-height: 55px;
    padding-right: 24px;

    .extra-item {
      display: inline-block;
      margin-right: 24px;

      a {
        margin-left: 24px;
      }
    }
  }
  /* 首页访问量统计 */
  .head-info {
    position: relative;
    text-align: left;
    padding: 0 32px 0 0;
    min-width: 125px;
    &.center {
      text-align: center;
      padding: 0 32px;
    }
    span {
      color: rgba(0, 0, 0, .45);
      display: inline-block;
      font-size: .95rem;
      line-height: 42px;
      margin-bottom: 4px;
    }
    p {
      line-height: 42px;
      margin: 0;
      a {
        font-weight: 600;
        font-size: 1rem;
      }
    }
  }
</style>