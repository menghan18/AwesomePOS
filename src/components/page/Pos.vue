<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  <el-button type="text" size="small" @click="deleteSingleGoods(scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>
              {{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
              <small>金额：</small>
              {{totalMoney}}元
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="deleteAllGoods()">删除</el-button>
              <el-button type="success" @click="checkout()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" :key="goods.goodsid" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type0Goods" :key="goods.goodsid" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                <li v-for="goods in type1Goods" :key="goods.goodsid" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class="cookList">
                <li v-for="goods in type2Goods" :key="goods.goodsid" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class="cookList">
                <li v-for="goods in type3Goods" :key="goods.goodsid" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  created: function() {
    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods"
      )
      .then(res => {
        console.log("获取常用商品成功", res);
        this.oftenGoods = res.data;
      })
      .catch(err => {
        console.log("获取常用商品失败", err);
      });
    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods"
      )
      .then(res => {
        console.log("获取分类商品成功", res);
        this.type0Goods = res.data[0];
        console.log(res.data[0]);
        this.type1Goods = res.data[1];
        this.type2Goods = res.data[2];
        this.type3Goods = res.data[3];
        // console.log(this);
      })
      .catch(err => {
        console.log("获取分类商品失败", err);
      });
  },
  mounted: function() {
    setTimeout(() => {
      let orderHeight = document.body.clientHeight;
      document.getElementById("order-list").style.height = orderHeight + "px";
    }, 0);
  },
  methods: {
    addOrderList(goods) {
      //添加前先把数量和金额清零
      this.totalMoney = 0;
      this.totalCount = 0;
      //商品是否已存在订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据判断的值编写业务逻辑
      if (isHave) {
        let arr = this.tableData.filter(a => a.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      //计算数量和金额
      this.tableData.forEach(Element => {
        this.totalCount += Element.count;
        this.totalMoney = this.totalMoney + Element.price * Element.count;
      });
    },
    //删除单个商品
    deleteSingleGoods(goods) {
      this.tableData = this.tableData.filter(a => a.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //删除所有商品
    deleteAllGoods() {
      this.tableData = [];
      this.totalMoney = 0;
      this.totalCount = 0;
    },
    //结账
    checkout() {
      if (this.totalCount != 0) {
        this.tableData = [];
        this.totalMoney = 0;
        this.totalCount = 0;
        this.$message({
          message:'结账成功！',
          type:'success'
        })
      }else{
        this.$message.error('没有商品不能结账！');
      }
    },

    //汇总数量和金额
    getAllMoney() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        this.tableData.forEach(Element => {
          this.totalCount += Element.count;
          this.totalMoney = this.totalMoney + Element.price * Element.count;
        });
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #cbccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px soild #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 10px;
  background-color: #ffffff;
  cursor: pointer;
}
.o-price {
  color: #587bff;
}
.goods-type {
  clear: both;
}

.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auto;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d3dce6;
}
</style>
<style>
/* 修改点餐按钮padding-left样式 */
.el-tabs__nav-scroll {
  padding-left: 20px;
}
</style>