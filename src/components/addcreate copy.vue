<template>
  <div class="create_daohang">
    <span class="create_daohang_a" v-show="!drop" @click="changeDrop">
      <img class="create_daohang_a_icon" :src="xjcreate" alt />
    </span>
    <div class="create_ks_fixed" v-show="drop" @click="dropcancel">
      <div class="create_ks_box clearfix">
        <div class="create_ks_cont clearfix">
          <div
            v-for="(item,index) in dropList"
            :key="'ks'+index"
            class="create_ks_list clearfix fl"
            @click="goCreate(item.url)"
          >
              <img :src="item.img" alt />
              <span>{{item.name}}</span>
          </div>
        </div>
        <i class="el-icon-close create_ks_close"></i>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      drop: false,
      dropList: [
        {
          name: "BP&周报",
          url: "/rbcreate",
          img: require("../assets/img/normal/xjzb.png")
        },
        {
          name: "新建拜访",
          url: "/createData",
          img: require("../assets/img/normal/xjbf.png")
        },
        {
          name: "新建派单",
          url: "/create",
          img: require("../assets/img/normal/xjpd.png")
        },
        {
          name: "新建日报",
          url: "/xsCreateData",
          img: require("../assets/img/normal/xjrb.png")
        },
        {
          name: "新建客户",
          url: "/addCustomer",
          img: require("../assets/img/normal/xjkh.png")
        }
      ],
      xjcreate: require("../assets/img/normal/xjcreate.png")
      //   xjrb: require("../assets/img/normal/xjrb.png"),
      //   xjbf: require("../assets/img/normal/xjbf.png"),
      //   xjkh: require("../assets/img/normal/xjkh.png"),
      //   xjpd: require("../assets/img/normal/xjpd.png"),
      //   xjzb: require("../assets/img/normal/xjzb.png")
    };
  },

  methods: {
    goCreate(url) {
      this.drop = false;
      this.$router.push(url);
    },
    changeDrop() {
      this.drop = true;
    },
    dropcancel() {
      this.drop = false;
    }
  }
};
</script>
<style scoped>
.create_daohang_a_icon {
  width: 50px;
  display: block;
  height: 50px;
}
.create_daohang {
  position: fixed;
  right: 10px;
  top: 80%;
  z-index: 1002;
  user-select: none;
  -webkit-user-select: none;
  outline: none;
}
.create_ks_close {
  font-size: 22px;
  border-top: 1px solid #f0f0f0;
  padding: 10px 0;
  display: block;
  color: #666;
}
.create_ks_fixed {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  font-size: 14px;
  z-index: 1001;
  background: rgba(0, 0, 0, 0.5);
}
.create_ks_box {
  position: absolute;
  bottom: 10px;
  right: 10px;
  padding: 10px 10px 0;
  background: #fff;
  border-radius: 5px;
  left: 10px;
 
}

.create_ks_list {
  color: #333;
    width: 25%;
    margin: 4px 0 10px;
}
.create_ks_list img {
      width: 36px;
    text-align: center;
}
.create_ks_list span {
    display: block;
  line-height: 24px;
  color: #333;
  font-size: 12px;
}
@media screen and (min-width: 850px) {
   .create_ks_list{
       width:20%
   }
}
</style>