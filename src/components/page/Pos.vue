<template>
    <div class="pos">
      <el-row>
        <el-col :span="7" class="pos-order" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
               <el-table border style="width:100%" :data="tableData">
                 <el-table-column prop="goodsName" label="商品名称" width="120"></el-table-column>
                 <el-table-column prop="count" label="数量" width="60"></el-table-column>
                 <el-table-column prop="price" label="金额" width="60"></el-table-column>
                 <el-table-column label="操作" fixed="right" width="100">
                    <template slot-scope="scope">
                      <el-button type="text" size="samll" @click="delSingelGoods(scope.row)">删除</el-button>
                      <el-button type="text" size="samll" @click="addOrderlist(scope.row)">增加</el-button>
                    </template>
                 </el-table-column>
               </el-table>

               <div class="totalDiv">
                 <small>数量：</small>{{totalCount}}
                 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                 <small>金额：</small>{{totalMoney}}
               </div>

               <div class="div-btn">
                 <el-button type="warning">挂单</el-button>
                 <el-button type="danger" @click="delAllGoods">删除</el-button>
                 <el-button type="success" @click="checkOut">结账</el-button>
               </div>
            </el-tab-pane>

            <el-tab-pane label="挂单">

            </el-tab-pane>

            <el-tab-pane label="外卖">

            </el-tab-pane>
          </el-tabs>
        </el-col>

        <el-col :span="15">
          <div class="hotSall">
            <div class="title">热销</div>
            <div class="hotSall-list">
              <ul>
                <li v-for="goods in oftenGoods" @click="addOrderlist(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type0Goods" @click="addOrderlist(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.goodsPrice}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>

              <el-tab-pane label="小食">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type1Goods" @click="addOrderlist(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.goodsPrice}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>

              <el-tab-pane label="饮料">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type2Goods" @click="addOrderlist(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.goodsPrice}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>

              <el-tab-pane label="套餐">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type3Goods" @click="addOrderlist(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.goodsPrice}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
      </el-row>
    </div>
</template>
<script>
  import axios from 'axios';
    export default {
       name:'pos',
       data(){
         return {
            tableData:[],
            oftenGoods:[],
            type0Goods:[],
            type1Goods:[],
            type2Goods:[],
            type3Goods:[],
            totalMoney:0,
            totalCount:0
         }
       },
       created(){
         //热销
         axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
         .then(response=>{
           //console.log(response);
           this.oftenGoods=response.data;
         })
        .catch(error=>{
           //console.log(error);
           alert('网络错误，不能访问');
         })

        //汉堡
         axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
         .then(response=>{
           //console.log(response);
           this.type0Goods=response.data[0];
           this.type1Goods=response.data[1];
           this.type2Goods=response.data[2];
           this.type3Goods=response.data[3];
         })
         .catch(error=>{
           //console.log(error);
           alert('网络错误，不能访问');
         })
       },
       mounted:function(){
          var orderHeight=document.body.clientHeight;
          document.getElementById('order-list').style.height=orderHeight+"px";
       },
       methods:{
         addOrderlist(goods){
            this.totalMoney=0;
            this.totalCount=0;

           //商品是否存在于订单中
           let isHave=false;
           for(let i=0;i<this.tableData.length;i++){
              //console.log(this.tableData[i].goodsId);
              if(this.tableData[i].goodsId==goods.goodsId){
                isHave=true;
              }
           }
           //根据判断的值编写业务逻辑
           if(isHave){
             //改变列表中的商品数量
             let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId);
             arr[0].count++;
           }else{
             //构造新goods
             let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
             this.tableData.push(newGoods);
           }
           this.getAllmoney();
         },
         //删除单个商品
         delSingelGoods(goods){
           this.tableData=this.tableData.filter(o=> o.goodsId!=goods.goodsId);
           this.getAllmoney();
         },
         //汇总数量金额
         getAllmoney(){
           this.totalMoney=0;
           this.totalCount=0;
           //计算总金额总数量
           this.tableData.forEach((element)=>{
               this.totalCount+=element.count;
               this.totalMoney=this.totalMoney+(element.price*element.count);
         })
         },
         //删除所有商品
         delAllGoods(){
            this.tableData=[];
            this.totalMoney=0;
            this.totalCount=0;
         },
         //模拟结账
         checkOut(){
           if(this.totalCount!=0){
              this.tableData=[];
              this.totalMoney=0;
              this.totalCount=0;
              this.$message({
                 message:'结账成功，欢迎下次光临',
                 type:'success'
              });
           }else{
              this.$message.error('不能空结账')
           }
         }
       }
    }
</script>
<style scoped>
  .pos-order{
     background-color: #FAF9FC;
     border-right: 1px solid #C0CCDA;
  }
  .div-btn{
    padding: 10px 0;
  }
  .hotSall-list ul li{
     list-style: none;
     float: left;
     border: 1px solid #E5E9F2;
     padding: 10px;
     margin: 10px;
    cursor: pointer;
  }
  .title{
    height: 20px;
    border-bottom: 1px solid #d3dce6;
    background-color: #F9FAFC;
    padding: 10px;
    text-align: left;
    background-color: white;
  }
  .o-price{
    color:#58B7FF;
  }
  .goods-type{
    clear: both;
  }
  .cookList li {
    list-style: none;
    padding: 2px;
    margin: 0 15px 0 0px;
    cursor: pointer;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: 80px;
    overflow: hidden;
    background-color: #fff;
    float: left;
    box-shadow: 0 2px 2px rgb(203, 208, 217);
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
    height: 50%;
    line-height: 30px;
    width: 60%;
    color: #6e8299;
    text-align: center;
  }

  .foodPrice {
    font-size: 16px;
    height: 50%;
    line-height: 23px;
    width: 60%;
    color: #FF5722;
    text-align: center;
  }
.totalDiv{
   padding: 10px;
   background-color: #fff;
   border-bottom: 1px solid #E5E9F2;
}
</style>
