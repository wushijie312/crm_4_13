<template>
  <div class="wrapper">
    <Head :act.sync="act" :ty.sync="act1"></Head>
    <div class="content csgogogo" ref="wrapper">
      <div class="menu-head-top50"></div>
      <div class="filter" @click="drawer = true"></div>
      <el-drawer
        size="80%"
        :visible.sync="drawer"
        :with-header="false"
        :show-close="false"
        @close="closeDrawer"
      >
        <div style="width:98%;margin-left:1%;padding: 0px 10px;">
          <el-form ref="form" label-width="8em" style="padding:0.3rem;">
            <el-form-item label="供应商名称">
              <el-input placeholder="请输入供应商名称" v-model="searchData.providerName"></el-input>
            </el-form-item>
            <el-form-item label="省市">
              <el-cascader
                ref="cascaderAddr"
                placeholder="请选择省市"
                :options="options"
                clearable
                v-model="searchData.selected"
                :props="{value:'label'}"
                style="width: 100%;display: inline-block;"
              ></el-cascader>
            </el-form-item>
            <el-form-item label="主营业务">
              <el-input placeholder="请输入主营业务" v-model="searchData.major"></el-input>
            </el-form-item>
            <el-form-item label="付款方式">
              <el-select v-model="searchData.resultSectionBy" placeholder="请选择付款方式">
                <el-option label="半年结" value="4"></el-option>
                <el-option label="季结" value="3"></el-option>
                <el-option label="2.5月结" value="10"></el-option>
                <el-option label="2月结" value="8"></el-option>
                <el-option label="1.5月结" value="9"></el-option>
                <el-option label="4月结" value="12"></el-option>
                <el-option label="月结" value="2"></el-option>
                <el-option label="现结" value="11"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="接单金额(万元)">
              <el-select v-model="searchData.orderMoney" placeholder="请选择接单金额">
                <el-option label="<50" value="1"></el-option>
                <el-option label="50-100" value="2"></el-option>
                <el-option label="100-300" value="3"></el-option>
                <el-option label="300-700" value="4"></el-option>
                <el-option label="700-1000" value="5"></el-option>
                <el-option label=">1000" value="6"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="接单数量">
              <el-input placeholder="请输入接单数量" v-model="searchData.orderCount"></el-input>
            </el-form-item>
            <div style="text-align: center;">
              <el-button type="info" @click="zhongjian(false)">重置</el-button>
              <el-button type="primary" @click="zhongjian(true)">搜索</el-button>
            </div>
          </el-form>
        </div>
      </el-drawer>
      <el-table
        :data="data1"
        :row-class-name="tableRowClassName"
        cell-class-name="selfCell"
        :cell-style="cellClass"
        :header-cell-style="cellClass"
        border
        show-overflow-tooltip
        style="width: 100%"
        @row-click="goxq"
      >
        <el-table-column prop="providerName" label="供应商名称" fixed width="150">
          <template v-slot="scope">
            <!--<router-link :to='{path:"/supplyd",query:{providerCode:scope.row.providerCode}}' style="text-align:center;"></router-link>-->
            {{scope.row.providerName}}
          </template>
        </el-table-column>
        <el-table-column prop="major" label="主营业务" class-name="selfColumn" width="137">
          <template v-slot="scope">{{scope.row.major?scope.row.major:'/'}}</template>
        </el-table-column>
        <el-table-column prop="position" label="付款方式" class-name="selfColumn" width="90">
          <template v-slot="scope">{{scope.row.resultSectionByStr?scope.row.resultSectionByStr:'/'}}</template>
        </el-table-column>
        <el-table-column
          prop="correlation_person"
          label="接单金额(万元)"
          class-name="selfColumn"
          width="140"
        >
          <template v-slot="scope">{{scope.row.orderPriceStr?scope.row.orderPriceStr:'/'}}</template>
        </el-table-column>
        <el-table-column prop="relation" label="接单数量" class-name="selfColumn" width="90">
          <template v-slot="scope">{{scope.row.orderCount?scope.row.orderCount:'/'}}</template>
        </el-table-column>
        <el-table-column prop="relation" label="省" class-name="selfColumn" width="90">
          <template v-slot="scope">{{scope.row.provinceName?scope.row.provinceName:'/'}}</template>
        </el-table-column>
        <el-table-column prop="relation" label="市" class-name="selfColumn" width="100">
          <template
            v-slot="scope"
          >{{scope.row.cityName==scope.row.provinceName?scope.row.areaName:scope.row.cityName}}</template>
        </el-table-column>
      </el-table>
    </div>

    <!-- <ShowbackTop/> -->
    <Addcreate v-if="!act1"></Addcreate>
  </div>
</template>
<script>
import { providerList } from "@/api/configWu";
import BScroll from "better-scroll";
import Head from "@/view/common/head";
import { pca } from "@/assets/data/pca.js";
import Addcreate from "@/components/addcreate";
// import ShowbackTop from "@/components/showbackTop";
export default {
  name: "index",
  components: {
    Head,
    Addcreate,
    // ShowbackTop
  },
  data() {
    return {
      act: 1,
      act1: this.$route.query.id == 2 ? false : true,
      drawer: false,
      isscroll: false,
      loading: true,
      dataNumber:0,
      pageSize:50,
      pulldownTip: {
        text: "", // 松开立即刷新
        textup: "", // 松开立即刷新
        rotate: "" // icon-rotate
      },
      pagenum: 1,
      data1: [],
      defaultProps: {
        children: "children",
        label: "label"
      },
      options: [],
      searchData: {
        selected: "",
        providerName: "",
        major: "",
        resultSectionBy: ""
      }
    };
  },
  mounted() {
    this.getdata(this.pagenum).then(res => {
      if (res.code == 200) {
         this.dataNumber=res.data;
        this.data1 = res.dataList;
      }
    });
    this.getact();
    //
    window.addEventListener("scroll", this.scrollSuppy, true);
    // document.addEventListener("scroll", this.BS);
    //  this.gethong();
    this.areaData();
  },
  methods: {
    getact() {
      var lodata =
        this.$route.query.id == 1
          ? JSON.parse(sessionStorage.getItem("leaderMenus"))
          : JSON.parse(sessionStorage.getItem("userMenus"));
      console.log(lodata);
      lodata.forEach((e, index) => {
        if (e.path == "/supply?id=1" || e.path == "/supply?id=2") {
          this.act = index + 1;
        }
      });
    },
    areaData() {
      var aread = pca;
      for (var i in aread["86"]) {
        var _arr = {
          value: i,
          label: aread["86"][i],
          children: []
        };
        this.options.push(_arr);
      }
      for (var ii in aread) {
        this.options.forEach((item, index) => {
          if (item.value == ii) {
            for (var iic in aread[ii]) {
              var _arrc = {
                value: iic,
                label: aread[ii][iic]
              };
              this.options[index].children.push(_arrc);
            }
          }
        });
      }
    },
    tableRowClassName({ row, rowIndex }) {
      if (rowIndex % 2 == 0) {
        return "success-row";
      }
      return "";
    },
    cellClass() {
      //  	return 'text-align: center;white-space: nowrap;overflow: hidden;text-overflow: ellipsis;display: table-cell!important;'
    },
    goxq(row) {
      this.$router.push({
        path: "/supplyd",
        query: { id: row.id }
      });
    },

    scrollSuppy() {
      // 滚动到页面底部时
      // const el = document.getElementById("customlist");
      let scrollTop =
        document.documentElement.scrollTop || document.body.scrollTop;

      let clientHeight = document.documentElement.clientHeight;
      let scrollHeight = document.documentElement.scrollHeight;
      const toBottom = scrollHeight - scrollTop - clientHeight;

     if (
        toBottom <= 30 &&
        this.loading &&
        this.dataNumber > this.data1.length
      ) {
        this.loading = false;
        let scrollTop =
          document.documentElement.scrollTop || document.body.scrollTop;
        this.getdata(++this.pagenum).then(res => {
          if (res.code == 200) {
            this.dataNumber=res.data;
            this.data1 = this.data1.concat(res.dataList);
            if (this.data1.length == this.pagenum * this.pageSize) {
              document.documentElement.scrollTop = scrollTop - 10;
            }
            if(this.dataNumber<this.pagenum * this.pageSize){
              this.$message.success("已经到底部了");
            }
            this.loading = true;
          }
        });
      }
    
    },
 

    zhongjian(status) {
      if (!status) {
        this.searchData = {
          selected: ""
        };
      } else {
        this.drawer = false;
      }
    },
    closeDrawer() {
      this.zhongjian(true);
      this.pagenum = 1;
      this.getdata(this.pagenum);
    },
    getdata(page) {
      return providerList({
        page: page,
        pageSize: this.pageSize,
        role: localStorage.getItem("role"),
        userId: localStorage.getItem("userid"),
        level: localStorage.getItem("level"),
        providerName: this.searchData.providerName,
        major: this.searchData.major,
        resultSectionBy: this.searchData.resultSectionBy,
        provinceName: this.searchData.selected
          ? this.searchData.selected[0]
          : "",
        cityName: this.searchData.selected ? this.searchData.selected[1] : "",
        orderMoney: this.searchData.orderMoney,
        orderCount: this.searchData.orderCount
          ? this.searchData.orderCount
          : null
      });
    }
  }
};
</script>
<style type="text/css">
</style>
<style scoped>
.filter {
  height: 30px;
  padding: 10px;
}
.filter::after {
  content: "";
  display: block;
  float: right;
  width: 30px;
  height: 100%;
  background: url(../../assets/img/normal/filter.png) no-repeat center;
  background-size: 100%;
}
.content {
  background: #f2f2f5;
}
.el-card__header {
  background: #21aefb;
}
.item > div {
  width: 50%;
  float: left;
  text-align: left;
  box-sizing: border-box;
  padding: 5px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.head {
  height: 1rem;
  font-size: 0.3rem;
  line-height: 1rem;
  background: #21aefb;
  color: #fff;
}
.tap {
  font-size: 0.3rem;
  background: #21aefb;
  height: 0.7rem;
  line-height: 0.7rem;
}
.tap .act {
  background: #fff;
  color: #333;
}
.tap .act a {
  color: #333;
}
.tap a {
  color: #fff;
  text-decoration: none;
}
.maincontent {
  font-size: 0.3rem;
  height: 2.5rem;
  background: #fff;
  padding: 0.2rem;
  box-sizing: border-box;
}
.nianBt {
  height: 1.3rem;
  width: 20%;
  background: red;
  padding-top: 0.6rem;
  color: #fff;
}
table,
tbody,
thead {
  width: 100% !important;
}
.el-table .cell,
.el-table th div,
.el-table--border td:first-child .cell,
.el-table--border th:first-child .cell {
  padding: 0;
  text-align: center;
}
.el-table .cell,
.el-table th div,
.el-table--border td:first-child .cell,
.el-table--border th:first-child .cell {
  padding-left: 0px;
}
</style>