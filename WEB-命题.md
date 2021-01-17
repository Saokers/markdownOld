## PC

#### 收藏

```vue
<el-button type="danger" @click="addWish(productDetail.id)" plain icon="el-icon-star-off" circle></el-button>

//添加收藏
    addWish(productId) {
      var vm = this;
      var user = vm.Storage.Local.get("User");
      if (user && productId) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CUSTOMER_ADD_WISHLIST, {
          userId: userId,
          productId: productId
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            if (res.data.success) vm.Toastr.success("添加成功！");
            else vm.Toastr.error("请勿重复添加！");
          } else {
            vm.Toastr.error("添加失败！");
          }
        });
      } else {
        vm.Toastr.warning("请先登录再收藏商品!");
      }
    },
```

```vue


                   <div class="shop-option-over">
                        <a
                          class="btn btn-default add-wishlist"
                          @click="addWish(product.id)"
                          data-toggle="tooltip"
                          title="添加收藏"
                        >
                          <i class="fa fa-heart nopadding"></i>
                        </a>
                      </div>
    addWish(productId) {
      var vm = this;
      var userId = vm.Storage.Local.get("User").id;
      if (userId && productId) {
        vm.$post(vm.API.API_URL_CUSTOMER_ADD_WISHLIST, {
          userId: userId,
          productId: productId
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            vm.Toastr.success(res.data.message);
          }
        });
      }
    },
```



#### 二维码支付

```vue
                 <a @click="qrcode">
                      <img src="../../static/images/pay/alipaypcnew.png">
                    </a>

	    <div>
      <qr
      :size="200"
      :text="qrdate"
      v-if="qrcodeState"
      ></qr>
    </div>

computed: {
    qrdate() {
      return JSON.stringify({
        orderNum: this.payment.orderNum,
        userId: this.Storage.Local.get("User").id,
        id: this.selectContactId,
        paymentType: 1
      });
    }
  },
    qrcode() {
      var vm = this;
      if (!vm.selectContactId) {
        vm.Toastr.error("请选择联系人！");
        return;
      } else {
        vm.qrcodeState = true;
        var times = setInterval(() => {
          vm.$post(vm.API.API_CHECK_ORDER, {
            orderNum: this.payment.orderNum
          }).then(res => {
            if (res.data == 1) {
              clearInterval(times);
              vm.Toastr.success("支付成功，即将跳转页面！！！！！！！！");
              setTimeout(() => {
                vm.$router.push("/account/order/list");
              }, 2000);
            }
          });
        }, 2000);
      }
    },
import qr from "vue-qr";
  components: { qr },
```



#### 商品购买

```vue
Cart
<section class="page-header page-header-xs">
      <div class="container">
              <div class="row">
        <div class="col-lg-8">
    <template>
      <el-table
      :data="cartList.productList"
      style="width: 100%">
      <el-table-column
        label="产品名称"
        width="100">
       <template slot-scope="scope">
          <img v-bind:src="API.BASE_SERVER_URL+scope.row.defaultImg" width="50" height="50"/>
      </template>
      </el-table-column>
      <el-table-column
        prop="name"
        label=""
        width="290">
      </el-table-column>
      <el-table-column
        label="数量">
      <template slot-scope="scope">
            <input type="number" required min="1" v-model="scope.row.quantity" style="width:40px">&times; {{(scope.row.shopPrice/100).toFixed(2)}}   
      </template>
      </el-table-column>
       <el-table-column
        label="合计价格">
      <template slot-scope="scope">
            {{(scope.row.quantity*scope.row.shopPrice/100).toFixed(2)}}   
      </template>
      </el-table-column>
       <el-table-column>
      <template slot-scope="scope">
     <el-button type="info" plain @click="removeCartItem(scope.row.id)">&times;</el-button>
      </template>
      </el-table-column>
    </el-table>
    <div class="pull-right" style="margin-top:10px">
         <el-button  type="danger" plain  @click="removeCartAll"
                               >清空购物车</el-button>
                                       <el-button type="success"  plain 
                               >更新购物车</el-button>
    </div>
  </template>
        </div >
        <div class="col-lg-4">
          <div class="flexbox">
            <div class="flex">
              <span>商品总价：</span>
              <span>￥{{(cartList.price/100).toFixed(2)}}</span>
            </div>
            <div class="flex">
              <span>物流费用：</span>
              <span>包邮</span>
            </div>
            <hr>
            <div class="flex"> 
              <span>合计金额：</span>
             <span>￥{{(cartList.price/100).toFixed(2)}}</span>
            </div>
                   <el-button class="pull-right" type="success"  plain @click="cartOrOrder"
                               >立即支付</el-button>
          </div>
        </div>
         </div>
      </div>
    </section >    


getCartList() {
      var vm = this;
      var user = this.Storage.Local.get("User");
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART, {
          userId: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.cartList = res.data;
            console.log(res.data);
          }
        });
      }
    },
    //注意更新购物车的数量:vm.$store.commit("setCartCount",vm.getCartCount())
    removeCartItem(productId) {
      var vm = this;
      var user = this.Storage.Local.get("User");
      var cartCount = 0;
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART_ITEMS_REMOVE, {
          productId: productId,
          userId: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.Toastr.success("移除成功！");
            vm.getCartList();
          }
        });
      }
    },
    removeCartAll() {
      var vm = this;
      var user = this.Storage.Local.get("User");
      var cartCount = 0;
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART_ITEMS_REMOVEALL, {
          userId: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.Toastr.success("清空成功！");
            vm.getCartList();
          }
        });
      }
    },

    cartOrOrder() {
      var vm = this;
      var user = this.Storage.Local.get("User");
      var cartCount = 0;
      var orderItemList = vm.cartList.productList.map(val => {
        return {
          productId: val.id,
          quantity: val.quantity
        };
      });

      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART_COMMIT, {
          userId: userId,
          orderItemList: orderItemList
        }).then(res => {
          if (res.errorCode == 0) {
            vm.removeCartAll();
            vm.$router.push("/account/payment");
          }

        });
      }
    }

.flexbox {
  background-color: #ffffff;
  height: auto;
  font-size: 1.5em;
  padding: 5vh;
}
.flex {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1vw;
}
```

```vue
Payment
<section class="page-header page-header-xs">
      <div class="container">
              <div class="row">
        <div class="col-lg-8">
    <template>
      <el-table
      v-if="payment.orderItemList"
      :data="payment.orderItemList"
      style="width: 100%">
      <el-table-column
        label="产品名称"
        width="100">
       <template slot-scope="scope">
          <img v-bind:src="API.BASE_SERVER_URL+scope.row.productList[0].defaultImg" width="50" height="50"/>
      </template>
      </el-table-column>
       <el-table-column
        label=""
        width="290">
        <template slot-scope="scope">
            {{scope.row.productList[0].name}}
      </template>
      </el-table-column> 
      <el-table-column
        label="数量">
      <template slot-scope="scope">
           {{scope.row.quantity}}&times; {{(scope.row.productList[0].shopPrice/100).toFixed(2)}}   
      </template>
      </el-table-column>
       <el-table-column
        label="合计价格">
      <template slot-scope="scope">
           {{(scope.row.price/100).toFixed(2)}}
      </template>
      </el-table-column>
    </el-table>
  <h4>常用联系人</h4>
  <hr>
     <tr v-for="(z,index) in payment.contectList">
                  <td v-if="z.contactFlag==1">
                    <input
                      type="radio"
                      name="contect"
                      checked="checked"
                      @click="doOptions(z.id)"
                    >
                  </td>
                  <td v-else>
                    <input
                      type="radio"
                      name="contect"
                      @click="doOptions(z.id)"
                    >
                  </td>
                姓名：  {{z.name}}
                电话:    {{z.mobile}}
                地址： {{z.address}}
                </tr>

                  <h4>请选择支付方式</h4>
  <hr>  
   <img  src="../../static/pc_wxqrpay.png" @click="payTreasure(0)" >
    <img src="../../static/alipaypcnew.png" @click="qrcode" >

    <div>
      <qr
      :size="200"
      :text="qrdate"
      v-if="qrcodeState"
      ></qr>
    </div>
    
  </template>
        </div >
       <div class="col-lg-4">
          <div class="flexbox">
            <div class="flex">
              <span>商品总价：</span>
              <span>￥{{(payment.price/100).toFixed(2)}}</span>
            </div>
            <div class="flex">
              <span>物流费用：</span>
              <span>包邮</span>
            </div>
            <hr>
            <div class="flex"> 
              <span>合计金额：</span>
             <span>￥{{(payment.price/100).toFixed(2)}}</span>
            </div>
                 
          </div>
        </div> 
         </div>
      </div>
    </section >    

import qr from "vue-qr";
components: { qr },
computed: {
    qrdate() {
      return JSON.stringify({
        orderNum: this.payment.orderNum,
        userId: this.Storage.Local.get("User").id,
        id: this.selectContactId,
        paymentType: 1
      });
    }
  },

	 qrcode() {
      var vm = this;
      if (!vm.selectContactId) {
        vm.Toastr.error("请选择联系人！");
        return;
      } else {
        vm.qrcodeState = true;
        var times = setInterval(() => {
          vm.$post(vm.API.API_CHECK_ORDER, {
            orderNum: this.payment.orderNum
          }).then(res => {
            if (res.data == 1) {
              clearInterval(times);
              vm.Toastr.success("支付成功，即将跳转页面！！！！！！！！");
              setTimeout(() => {
                vm.$router.push("/account/order/list");
              }, 2000);
            }
          });
        }, 2000);
      }
    },
.flexbox {
  background-color: #ffffff;
  height: auto;
  font-size: 1.5em;
  padding: 5vh;
}
.flex {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1vw;
}
```

#### 个人信息设置

```vue
<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>个人信息设置</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">
          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li>
            <a @click="goto('/account/contact/list','ContactList')">常用联系人</a>
          </li>
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li class="active">
            <a>个人设置</a>
          </li>
          <li>
            <a @click="goto('/account/ewallet','EWallet')">充值</a>
          </li>
          <li>
            <a  @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>

    <section v-if="user!=null">
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">个人信息设置</h2>
          </div>
          <fieldset class="padding-40">
            <div class="row">
              <h3 class="text-center">个人信息</h3>
              <div class="col-md-6 col-sm-6">
                <label for="nickName">昵称</label>
                <el-input
                  id="nickName"
                  name="nickName"
                  type="text"
                  v-bind:class="{error:tips.nicknameTip}"
                  v-model="user.nickname"
                />
                <label class="error" for="nickName">{{tips.nicknameTip}}</label>
              </div>

              <div class="col-md-6 col-sm-6">
                <label for="email">Email</label>
                <el-input
                  id="email"
                  name="email"
                  type="text"
                  v-bind:class="{error:tips.emailTip}"
                  v-model="user.email"
                />
                <label class="error" for="email">{{tips.emailTip}}</label>
              </div>
            </div>
            <el-button
              @click="saveBasic"
              type="primary"
              class="pull-right margin-top-20"
              icon="el-icon-edit"
            >保存</el-button>
          </fieldset>

          <hr>
          <h3 class="text-center">修改密码</h3>
          <fieldset class="padding-40">
            <div class="row">
              <form id="userPwdFrom" onsubmit="return false;">
                <div class="col-md-12 col-sm-12">
                  <label for="oldpassword">输入当前密码</label>
                  <el-input
                    id="oldpassword"
                    name="oldpassword"
                    type="password"
                    v-bind:class="{error:tips.oldpwdTip}"
                    v-model="pwds.oldpwd"
                  />
                  <label class="error" for="oldpassword">{{tips.oldpwdTip}}</label>
                </div>

                <div class="col-md-6 col-sm-6">
                  <label for="password">新的密码</label>
                  <el-input
                    id="password"
                    name="password"
                    type="password"
                    v-bind:class="{error:tips.pwdTip}"
                    v-model="pwds.pwd"
                  />
                  <label class="error" for="password">{{tips.pwdTip}}</label>
                </div>

                <div class="col-md-6 col-sm-6">
                  <label for="rpassword">确认密码</label>
                  <el-input
                    id="rpassword"
                    name="rpassword"
                    type="password"
                    v-bind:class="{error:tips.rpwdTip}"
                    v-model="pwds.rpwd"
                  />
                  <label class="error" for="rpassword">{{tips.rpwdTip}}</label>
                </div>
              </form>
            </div>
            <el-button
              @click="savePwd"
              type="primary"
              class="pull-right margin-top-20"
              icon="el-icon-edit"
            >保存</el-button>
          </fieldset>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      user: null,
      pwds: {
        oldpwd: "",
        pwd: "",
        rpwd: ""
      },
      tips: {
        nicknameTip: null,
        emailTip: null,
        oldpwdTip: null,
        pwdTip: null,
        rpwdTip: null
      }
    };
  },
  created() {
    this.getUserBasic();
  },
  methods: {
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    },
    getUserBasic() {
      var vm = this;
      var userId = vm.Storage.Local.get("User").id;
      if (userId) {
        vm.$post(vm.API.API_URL_CUSTOMER_BASIC, {
          id: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.user = res.data;
          }
        });
      }
    },
    saveBasic() {
      var vm = this;
      var email = /^([0-9A-Za-z\-_\.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g;
      if (vm.user.nickname.length == 0 && vm.user.email.length == 0) {
        vm.tips.nicknameTip = "昵称不能为空!";
        vm.tips.emailTip = "邮箱不能为空!";
      } else if (vm.user.nickname.length > 20) {
        vm.tips.nicknameTip = "昵称不能超过20位!";
        vm.tips.emailTip = null;
      } else if (vm.user.nickname.length == 0) {
        vm.tips.nicknameTip = "昵称不能为空!";
        vm.tips.emailTip = null;
      } else if (vm.user.email.length > 50) {
        vm.tips.emailTip = "邮箱不能超过50位!";
        vm.tips.nicknameTip = null;
      } else if (!email.test(vm.user.email)) {
        vm.tips.emailTip = "邮箱格式不正确!";
        vm.tips.nicknameTip = null;
      } else if (vm.user.email.length == 0) {
        vm.tips.emailTip = "邮箱不能为空!";
        vm.tips.nicknameTip = null;
      } else {
        vm.tips.emailTip = null;
        vm.tips.nicknameTip = null;
        var user = vm.Storage.Local.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(vm.API.API_URL_UPDATE_CUSTOMER, {
            id: userId,
            nickname: vm.user.nickname,
            email: vm.user.email
          }).then(res => {
            if (res.errorCode == 0 && res.data != null) {
              if (res.data.success) {
                vm.Toastr.success("修改成功！");
                var user = {
                  id: userId,
                  userName: vm.user.userName,
                  nickname: vm.user.nickname
                };
                vm.Storage.Local.set("User", user);
                vm.$store.commit("setUser", user);
              } else {
                vm.Toastr.success("修改失败！");
              }
            }
          });
        }
      }
    },
    savePwd() {
      var vm = this;
      if (
        vm.pwds.oldpwd.length == 0 &&
        vm.pwds.pwd.length == 0 &&
        vm.pwds.rpwd.length == 0
      ) {
        vm.tips.oldpwdTip = "请输入原始密码！";
        vm.tips.pwdTip = "请输入新密码！";
        vm.tips.rpwdTip = "请输入确认密码！";
      } else if (vm.pwds.oldpwd.length == 0) {
        vm.tips.oldpwdTip = "请输入原始密码！";
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.oldpwd.length > 20) {
        vm.tips.oldpwdTip = "密码不能超过20位！";
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.pwd.length == 0) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = "请输入新密码！";
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.pwd.length > 20) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = "密码不能超过20位！";
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.rpwd.length == 0) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "请输入确认密码！";
      } else if (vm.pwds.rpwd.length > 20) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "密码不能超过20位！";
      } else if (vm.pwds.rpwd.length != vm.pwds.pwd.length) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "密码与确认密码不一致！";
      } else {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
        var user = vm.Storage.Local.get("User");
        if (user) {
          var userId = user.id;
          vm.$post('http://127.0.0.1:8080/Shopping/api/v1/user/change/password', {
            id: userId,
            oldpassword: vm.pwds.oldpwd,
            rpassword: vm.pwds.rpwd
          }).then(res => {
            if (res.errorCode == 0 && res.data.success) {
              vm.Toastr.success("密码修改成功！");
              vm.pwds.oldpwd = "";
              vm.pwds.pwd = "";
              vm.pwds.rpwd = "";
            } else{
                vm.Toastr.error("原密码不正确！");
            }
          });
        }
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>

```

#### 商品推广

```vue
          <qr
                  style="text-align: center"
                  :logoSrc="'../../static/images/pay/alipaypcnew.png'"
                  :text="'http://172.31.18.157:8000/#/samsara/productdetail/'+productDetail.id"
                  :size="200"
                  :margin="0"
                ></qr>

import qr from "vue-qr";

```

#### 分类切换

#### 幻灯片

#### 收货地址管理

```vue
<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>常用联系人</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">
          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li class="active">
            <a>常用联系人</a>
          </li>
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li>
            <a @click="goto('/account/setting','UserSetting')">个人设置</a>
          </li>
          <li>
            <a @click="goto('/account/ewallet','EWallet')">充值</a>
          </li>
          <li>
            <a  @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <section>
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">常用联系人</h2>
          </div>

          <table class="table contact">
            <thead>
              <tr>
                <th>默认联系人</th>
                <th>联系人姓名</th>
                <th>联系人电话</th>
                <th>联系人地址</th>
                <th>操作</th>
              </tr>
            </thead>
            <tbody>
              <template v-if="accountList!=null&&accountList.data.legnth!=0">
                <tr v-for="(z,index) in accountList.data">
                  <td v-if="z.contactFlag==1">
                    <input
                      type="radio"
                      name="contect"
                      checked="checked"
                      @click="doOptions(API.API_URL_CONTECT_DEFAULT,z.id,'设置成功！')"
                    >
                  </td>
                  <td v-else>
                    <input
                      type="radio"
                      name="contect"
                      @click="doOptions(API.API_URL_CONTECT_DEFAULT,z.id,'设置成功！')"
                    >
                  </td>
                  <td>{{z.name}}</td>
                  <td>{{z.mobile}}</td>
                  <td>{{z.address}}</td>
                  <td>
                    <a @click="changeById(z.id,z.name,z.mobile,z.address)">编辑</a>
                    <a @click="doOptions(API.API_URL_CONTECT_REMOVE,z.id,'删除成功')">删除</a>
                  </td>
                </tr>
              </template>
            </tbody>
          </table>
          <div class="panel-body">
            <fieldset class="margin-top-10">
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <input type="hidden" name="id">
                  <label for="billing_firstname">
                    姓名&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input id="billing_firstname" v-model="name" type="text" maxlength="50"/>
                  <div class="erro">{{tips.nameMsg}}</div>
                </div>
                <div class="col-md-6 col-sm-6">
                  <label for="billing_lastname">
                    电话&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input id="billing_lastname" v-model="mobile" type="text" maxlength="11"/>
                  <div class="erro">{{tips.mobileMsg}}</div>
                </div>
              </div>
              <div class="row">
                <div class="col-lg-12">
                  <label for="billing_address1">
                    地址&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input
                    id="billing_address1"
                    v-model="address"
                    type="text"
                    placeholder
                    maxlength="255"
                  />
                  <div class="erro">{{tips.addressMsg}}</div>
                </div>
              </div>
              <el-button
                @click="addContact"
                type="primary"
                class="pull-right margin-top-20"
                icon="el-icon-edit"
              >保存联系人</el-button>
            </fieldset>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "concatList",
  data() {
    return {
      accountList: null,
      name: null,
      mobile: null,
      address: null,
      editFlag: false,
      editId: null,
      tips: {
        nameMsg: "",
        mobileMsg: "",
        addressMsg: ""
      }
    };
  },
  created() {
    this.getAccountList();
  },
  methods: {
    getAccountList() {
      var vm = this;
      var user = vm.Storage.Local.get("User");
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CONTECT, 
        { userId: userId }
        ).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.accountList = res.data;
          }
        });
      }
    },
    changeById(id, name, mobile, address) {
      this.editId = id;
      this.name = name;
      this.mobile = mobile;
      this.address = address;
      this.editFlag = true;
    },
    addContact() {
      var url = this.API.API_URL_CONTECT_ADD;
      var msg1 = "添加成功!";
      var msg2 = "联系人至多添加5人!";
      if (this.editFlag) {
        url = this.API.API_URL_CONTECT_UPDATE;
        msg1 = "修改成功!";
        msg2 = "修改失败!";
      }
      var mobile = /^(13[0-9]{9})|(18[0-9]{9})|(14[0-9]{9})|(17[0-9]{9})|(15[0-9]{9})$/;
      var vm = this;
      if (!vm.name && !vm.mobile && !vm.address) {
        vm.tips.nameMsg = "请输入用户名";
        vm.tips.mobileMsg = "请输入手机号码";
        vm.tips.addressMsg = "请输入地址";
      } else if (!vm.name) {
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.nameMsg = "请输入用户名";
      } else if (!vm.mobile) {
        vm.tips.nameMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入手机号码";
      } else if (!mobile.test(vm.mobile) || vm.mobile.length != 11) {
        vm.tips.nameMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入正确的手机号码";
      } else if (!vm.address) {
        vm.tips.nameMsg = "";
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "请输入地址";
      } else {
        vm.tips.nameMsg = "";
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "";
        var user = vm.Storage.Local.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(url, {
            id: vm.editId,
            userId: userId,
            name: vm.name,
            mobile: vm.mobile,
            address: vm.address
          }).then(res => {
            if (res.errorCode == 0) {
              if (res.data) {
                vm.getAccountList();
                vm.name = "";
                vm.mobile = "";
                vm.address = "";
                vm.Toastr.success(msg1);
                if (vm.editFlag) {
                  vm.editId = null;
                  vm.editFlag = false;
                }
              } else {
                vm.Toastr.error(msg2);
              }
            }
          });
        }
      }
    },
    doOptions(path, id, msg) {
      var vm = this;
      var user = vm.Storage.Local.get("User");
      if (user) {
        var userId = user.id;
        if (msg == "删除成功" && vm.editFlag && vm.editId == id) {
          vm.Toastr.error("联系人编辑中,不允许删除");
        } else {
          if (userId && id) {
            vm.$post(path, {
              userId: userId,
              id: id
            }).then(res => {
              if (res.errorCode == 0 && res.data) {
                vm.Toastr.success(msg);
                vm.getAccountList();
              }
            });
          }
        }
      }
    },
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    }
  }
};
</script>

<style scoped>
.contact {
  margin-top: 0px !important;
  border-bottom: 1px solid #ddd;
}
.erro,
.required {
  color: red;
}
</style>

```

#### 购物车

#### 充值

```vue
<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>充值</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">
          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li>
            <a @click="goto('/account/contact/list','ContactList')">常用联系人</a>
          </li>
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li>
            <a @click="goto('/account/setting','UserSetting')">个人设置</a>
          </li>
          <li class="active">
            <a>充值</a>
          </li>
          <li>
            <a  @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <section>
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">账户充值</h2>
          </div>
          <br>
          <div class="row">
            <h4 class="text-center">当前电子钱包账户余额为：{{(userAccount.amount).toFixed(2)}}</h4>
          </div>
          <div class="panel-body">
            <fieldset class="margin-top-10">
              <div class="row">
                <div class="form-group col-lg-12">
                  <label for="account">
                    请输入充值金额
                    <span style="color:red">*</span>
                  </label>
                  <el-input
                    type="text"
                    class="required"
                    v-model="account"
                    v-bind:class="{error:msg}"
                    placeholder="请输入充值金额"
                    maxlength="255"
                  />
                  <div class="erro">{{msg}}</div>
                </div>
              </div>
              <el-button
                @click="recharge"
                type="primary"
                class="pull-right margin-top-20"
                icon="el-icon-circle-plus-outline"
              >充值</el-button>
            </fieldset>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "EWallet",
  data() {
    return {
      account: "",
      msg: null,
      userAccount: {
        amount: 0
      }
    };
  },
  created() {
    this.getUserAccount();
  },
  methods: {
    getUserAccount() {
      var vm = this;
      var userId = vm.Storage.Local.get("User").id;
      if (userId) {
        vm.$post(vm.API.API_URL_CUSTOMER_Account, {
          userId: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            vm.userAccount = res.data;
          }
        });
      }
    },
    recharge() {
      var vm = this;
      if (vm.account.length == 0) {
        vm.msg = "请输入金额！";
      } else if (this.account < 0) {
        vm.msg = "金额不能为负";
      } else {
        vm.msg = null;
        var userId = vm.Storage.Local.get("User").id;
        if (userId) {
          vm.$post(vm.API.API_URL_CUSTOMER_Recharge_Account, {
            userId: userId,
            amount: vm.account
          }).then(res => {
            if (res.errorCode == 0 && res.data) {
              vm.account = "";
              vm.getUserAccount();
              vm.Toastr.success("充值成功！");
              vm.$post(vm.API.API_URL_CART_ORDER, {
                userId: userId,
                type: 1
              }).then(res => {
                  vm.payment = res.data;
              });
            } else {
              vm.Toastr.error("充值失败！");
            }
          });
        }
      }
    },
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>

```

#### 热销产品

```vue
           <div class="col-lg-3 col-md-3 col-sm-3">
              <!-- FEATURED -->
              <div class="margin-bottom-60">
                <h2 class="owl-featured">热销商品</h2>
                <div class="">
                  <div>
                    <!-- SLIDE -->
                    <ul class="list-unstyled nomargin nopadding text-left">
                      <template v-if="productHots&&productHots.length>0">
                        <li class="clearfix" v-for="productHot in productHots">
                          <!-- item -->
                          <div class="thumbnail featured clearfix pull-left">
                            <a @click="getDetail(productHot.id)">
                              <img v-bind:src="API.BASE_SERVER_URL+productHot.defaultImg" width="80" height="80"
                                   alt="featured item">
                            </a>
                          </div>
                          <a class="block size-12">{{productHot.name}}</a>
                          <div class="size-18 text-left padding-top-8">￥{{(productHot.shopPrice / 100).toFixed(1)}}
                          </div>
                          <span class="line-through nopadding-left">市场价:￥{{(productHot.price / 100).toFixed(1)}}</span>
                        </li>
                      </template>
                      <!-- /item -->
                    </ul>
                  </div>
                  <!-- /SLIDE -->
                </div>
              </div>
              <!-- /FEATURED -->
              <!-- HTML BLOCK -->
            </div>


//获取当前商品的热销推荐商品
    productHotList(productId, hot, pagesize) {
      var vm = this;
      vm.$get(
        vm.API.API_URL_CATALOG_PRODUCTS_HOT +
          "/" +
          productId +
          "/hot/" +
          hot +
          "/pagesize/" +
          pagesize
      ).then(res => {
        if (res.errorCode == 0) {
          vm.productHots = res.data;
          console.log(res.data);
        }
      });
    },


```

#### 发票抬头

#### 积分

#### 商品规格

```vue
          <template v-if="optionList!=null && optionList.length>0">
                    <div class="text-center" id="">
                      <h5>请选择购买商品选项</h5>
                      <div v-for="option in optionList">
                        <div class='optionGroup' option>
                          {{option.name}}:
                          <span v-for="(optionValues,index) in option.optionValuesList" :key="index"
                                class='option' @click="optionSelected(optionValues.id+'_'+option.id)"
                                v-bind:class="{optionSelected:optionvalueone==optionValues.id||optionvaluetwo==optionValues.id}">{{optionValues.name}}</span>
                        </div>
                      </div>
                    </div>
                  </template>
```

```
      minmaxprice: null,
      optionone: null,
      optionvalueone: null,
      optiontwo: null,js
      optionvaluetwo: null,
```

```js
			productOptionList(productId) {
      var vm = this;
      // 调取已选商品选项所有规格
      vm.$get(vm.API.API_URL_CATALOG_PRODUCT_OPTION + "/" + productId).then(
        res => {
          if (res.errorCode == 0) {
            //接收所有规格
            var optionList = res.data;
            if (res.data) {
              // 判断不为空后遍历optionList
              for (var option in optionList) {
                //定义optionShow数组 放不符合的规格
                var optionShow = [];
                //遍历每个商品选项里的规格
                for (var valuesList in optionList[option].optionValuesList) {
                  //定义是否副歌规格的中间变量
                  var itembool = false;
                  //遍历当前商品的规格
                  for (var sku in this.skuList) {
                    //分离key值
                    var skuGroup = this.skuList[sku].optionValueKeyGroup.split(
                      "_"
                    );
                    //遍历key值数值
                    for (var item in skuGroup) {
                      //判断   所有规格是否符合当前商品所含规格id 不符合itembool为false
                      if (
                        skuGroup[item] ==
                        optionList[option].optionValuesList[valuesList].id
                      ) {
                        itembool = true;
                      }
                    }
                    
                    //如果已有符合 退出循环
                     if(itembool){
                          break;
                     }
                  }
                  //如果不符合当前商品 放入optionShow
                  if (!itembool) {
                    optionShow.push(valuesList);
                  }
                }
                //反向数组
                optionShow.reverse();
                //遍历数组 然后从后到前删除不符合的规格
                for (var show in optionShow) {
                  optionList[option].optionValuesList.splice(
                    optionShow[show],
                    1
                  );
                }
              }
              //得到处理好的规格数组
              vm.optionList = optionList;
              console.log(vm.optionList);
            }
          }
        }
      );
    },
```

```js
//监听包装类型选项
    optionSelected(optionId) {
      //分离选项和规格
      var optionGroup = optionId.split("_");
      //判断选项个数
      if (this.optionList.length == 1) {
        // 赋值valueone
        this.optionvalueone = optionGroup[0];
        this.optionone = optionGroup[1];
        //调用获取该选项sku
        this.getskudata();
      } else {
        //如果valueone为空
        if (this.optionone == null) {
          //将第一个值赋值给valueone
          this.optionvalueone = optionGroup[0];
          this.optionone = optionGroup[1];
          //valueone不为空
        } else {
          //假如点击的值商品选项和vvalueone一样 则将该值替换valueone
          if (optionGroup[1] == this.optionone) {
            this.optionvalueone = optionGroup[0];
            this.optionone = optionGroup[1];
            //假如valuetwo不为空
            if (this.optiontwo != null) {
              //则获取规划参数
              this.getskudata();
            }
            //点击的值商品选项不和valueone一样，将值给valuetwo并且获得规格参数
          } else {
            this.optionvaluetwo = optionGroup[0];
            this.optiontwo = optionGroup[1];
            this.getskudata();
          }
        }
      }
    },
    //获得规格数据
    getskudata() {
      if (this.skuList) {
        //定义两个值存储valueone valuetwo的组合体
        var value1, value2;
        //判断valuetwo 不能为空 并将组合体储存
        if (this.optionvaluetwo != null) {
          value1 = this.optionvalueone + "_" + this.optionvaluetwo;
          value2 = this.optionvaluetwo + "_" + this.optionvalueone;
          //判断没有valuetwo 即商品只有一个选项
        } else {
          value1 = value2 = this.optionvalueone;
        }
        //遍历库存
        for (var sku in this.skuList) {
          //得到每个sku的value key
          var skuValue = this.skuList[sku].optionValueKeyGroup;
          // 判断key和组合体是否一样
          // 一样就把价格，库存 渲染到页面
          if (skuValue == value1 || skuValue == value2) {
            this.productDetail.shopPrice = this.skuList[sku].price;
            this.productDetail.quantity = this.skuList[sku].quantity;
            this.params.optionValueKeyGroup = this.skuList[
              sku
            ].optionValueKeyGroup;
          }
        }
      }
    },

```

## Shopping

#### Echart

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/echarts.min.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
		<!-- 为ECharts准备一个具备大小（宽高）的Dom -->
		<div id="main" style="width: 600px;height:400px;"></div>
		<script type="text/javascript">
			// 基于准备好的dom，初始化echarts实例
			var myChart = echarts.init(document.getElementById('main'));

			// 指定图表的配置项和数据
			var option = {
				title: {
					text: '第一个 ECharts 实例'
				},
				tooltip: {},
				legend: {
					data: ['销量']
				},
				xAxis: {
					data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
				},
				yAxis: {},
				series: [{
					name: '销量',
					type: 'bar',
					data: [5, 20, 36, 10, 10, 20]
				}]
			};

			// 使用刚指定的配置项和数据显示图表。
			myChart.setOption(option);
		</script>
	</body>
</html>

```

#### 幻灯片列表 （模态框）

#### 幻灯片列表 （DataTable）

- ##### 分析题目

  任务11：     管理端：实现幻灯片管理功能（8分）

  【功能说明】

  在Shopping管理端实现幻灯片管理功能。

  【任务要求】 

  1. 实现幻灯片详情数据显示（幻灯片名称、描述、缩略图、更新者、操作），如图3-10。 

  2. 点击【编辑】按钮，可编辑当前幻灯片的名称、描述、图片、排序序号等信息，编辑后可以保存当前幻灯片信息。如图3-11。

3. 点击【删除】按钮，可以将当前幻灯片信息删除。

- ##### 进行逆向生成代码

  ###### 1.数据库

  ###### 2.配置

  ​	2.1根目录(需要 mysql jar 和 新建 src 文件夹)

  ​	2.2配置文件 （用记事本转换为ANSI编码）

  ###### 3.编译

  ###### 4.将逆向编译的代码文件放进项目 （src文件夹）

- ##### 编写Service层

###### 1.AdvertService

  ```java
  	/**
  	 * 获取幻灯片列表
  	 */
  	public Map<String, Object> getAdvertInByCondition(PageInformation pageInfo,Advert advert);
  	/**
  	 *删除
  	 */
  	public int delteAdvert(String id);
  	
  	public int addAdvert(Advert advert);
  	
  	public int updateAdvert(Advert advert);
  	
  	public List<Advert> getAdvertById(int id);
  ```

###### 2.AdvertServiceImpl

  

```java
@Service
public class AdvertServiceImpl implements AdvertService {
	@Autowired
	AdvertMapper advertMapper;
	@Autowired
	PagingTool pagingTool;

	@Override
	public Map<String, Object> getAdvertInByCondition(PageInformation pageInfo,Advert advert) {
		// TODO Auto-generated method stub
		AdvertExample advertExample = new AdvertExample();
		Criteria createCriteria = advertExample.createCriteria();
		long countByExample = advertMapper.countByExample(advertExample);
		advertExample.setOrderByClause("sort_id asc");
		createCriteria.andTitleLike("%"+advert.getTitle()+"%");
		createCriteria.andMessageLike("%"+advert.getMessage()+"%");
		
		
		List<Advert> pagingData = pagingTool.PagingData("com.sybinal.shop.mapper.AdvertMapper.selectByExample", advertExample,
				Integer.parseInt(pageInfo.getiDisplayStart()) / Integer.parseInt(pageInfo.getiDisplayLength()),
				Integer.parseInt(pageInfo.getiDisplayLength()));
		
		
		HashMap<String, Object> datas = new HashMap<String, Object>();
		datas.put("sEcho", pageInfo.getsEcho());
		datas.put("iTotalRecords", countByExample);
		datas.put("iTotalDisplayRecords", countByExample);
		datas.put("aaData", pagingData);
		return datas;
	}

	@Override
	public int delteAdvert(String id) {
		// TODO Auto-generated method stub
		int i = 0;
		List<Integer> ids = new ArrayList<Integer>();
		String[] categoryIds = id.split("-");
		if(categoryIds != null && categoryIds.length != 0){
			for(String categoryId : categoryIds){
				Integer idTemp = Integer.parseInt(categoryId);
				ids.add(idTemp);
			}
		}
		i = advertMapper.deleteAdvertList(ids);
		return i;
	}

	@Override
	public int addAdvert(Advert advert) {
		// TODO Auto-generated method stub
		return advertMapper.insertSelective(advert);
	}

	@Override
	public int updateAdvert(Advert advert) {
		// TODO Auto-generated method stub
		return advertMapper.updateByPrimaryKeySelective(advert);
	}

	@Override
	public List<Advert> getAdvertById(int id) {
		// TODO Auto-generated method stub
		AdvertExample advertExample=new AdvertExample();
		Criteria createCriteria = advertExample.createCriteria();
		createCriteria.andIdEqualTo(id);
		List<Advert> selectByExample = advertMapper.selectByExample(advertExample);
		return selectByExample;
	}

}
```

###### 3.AdvertController

```java
@Controller
public class AdvertController {
	@Autowired
	AdvertService advertService;

	@RequestMapping(value = "/admin/advert", method = RequestMethod.GET)
	public ModelAndView getAdvert() {
		ModelAndView model = new ModelAndView();
		model.setViewName("admin/advert/advertMain");
		return model;
	}

	@RequestMapping(value = "/admin/advertInList", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> productCategorynfoDataTable(PageInformation pageInfo, Advert advert) {
		return advertService.getAdvertInByCondition(pageInfo, advert);
	}

	@RequestMapping(value = "/admin/delAdvert", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> checkVerifyProduct(@RequestBody String id) {
		Map<String, Object> map = new HashMap<String, Object>();
		if (StringUtils.isEmpty(id) == true) {
			map.put("error", "-1"); // 失败
		} else {
			int i = advertService.delteAdvert(id);
			if (i > 0) {
				map.put("error", "0"); // 成功
			} else {
				map.put("error", "-1"); // 失败
			}
		}
		return map;
	}

	@RequestMapping(value = "/admin/addAdvert", method = RequestMethod.GET)
	public ModelAndView addAdvert() {
		ModelAndView model = new ModelAndView();
		model.setViewName("admin/advert/advertEdit");
		return model;
	}
	
	@RequestMapping(value = "/admin/advert/edit", method = RequestMethod.POST)
	@ResponseBody
	public ModelAndView editAdvert(@RequestParam("id") Integer id) {
		ModelAndView model = new ModelAndView();
		List<Advert> advertById = advertService.getAdvertById(id);
		Advert advert=new Advert();
		advert=advertById.get(0);
		if (advert != null) {
			model.addObject("advert", advert);
		}
		model.setViewName("admin/advert/advertEdit");
		return model;
	}

	@RequestMapping(value = "/admin/advert/save", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> saveAdvert(@RequestBody Advert advert) {
		Map<String, Object> map = new HashMap<String, Object>();
		int i = 0;
		if (advert.getId() == null) {
			i = advertService.addAdvert(advert);
		} else {
			i = advertService.updateAdvert(advert);
		}
		if (i > 0) {
			map.put("error", "0"); // 成功
			map.put("data", advert); // 数据
		} else {
			map.put("error", "-1"); // 失败
			map.put("data", advert); // 数据成
		}
		return map;
	}
}

```

###### 4.advertMain.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
	<jsp:include page="../main/top.jsp" />
	<jsp:include page="../main/left.jsp" />
	<jsp:include page="../commonJs.jsp" />
	<div id="wrapper">
		<div class="small-header transition">
			<div class="content">
				<div class="hpanel">
					<div class="panel-body">
						<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_productCategory" name="form_productCategory"
									action="" method="POST">
									<div class="form-group col-lg-6">
										<label>幻灯片</label> <input type="text" id="name" name="name"
											class="form-control" maxlength="50" placeholder="幻灯片" />
									</div>
									<div class="form-group col-lg-6">
										<label>描述</label> <input type="text" id="description"
											name="description" class="form-control" maxlength="50"
											placeholder="描述" />
									</div>
									<div class="text-right m-t-xs">
										<button id="search" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>查询</strong>
										</button>
										<button id="reset" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px"
											style="margin-right: 20px;" type="reset">
											<strong>重置</strong>
										</button>
										<button id="add" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>添加幻灯</strong>
										</button>
										<button id="delBtn" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>删除幻灯</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>

			</div>

		</div>
		<div class="content">
			<div class="row">
				<div class="col-lg-12">
					<div class="hpanel">
						<div class="panel-body">
							<table id="advertlist" style="width: 100%;"
								class="table table-striped table-bordered table-hover"></table>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>

	<form id="forwordform"
		action='<c:url value="/admin/advert/edit?${_csrf.parameterName}=${_csrf.token}" />'
		method='POST'>
		<input type="hidden" name="id" id="id" />
	</form>

	<script type="text/javascript">
		$('#leftMenuProduct').addClass('active');
		var oTable;
		var header = $("meta[name='_csrf_header']").attr("content");
		var token = $("meta[name='_csrf']").attr("content");
		var contextPath = '${pageContext.request.contextPath}';
		$(function() {
			$('.date').datepicker();
			$(document).ajaxSend(function(e, xhr, options) {
				xhr.setRequestHeader(header, token);
			});
			AdvertListData();
		});
		$('#add').click(function() {
			window.location.href = contextPath + '/admin/addAdvert';
		});

		$('#search').click(function() {
			AdvertListData();
		});

		$('#delBtn').click(function() {
			var ids = document.getElementsByName("ids");
			var strIds = new Array();
			var idTemp = "";
			for (var i = 0; i < ids.length; i++) {
				if (ids[i].checked) {
					strIds[strIds.length] = ids[i].value;
					idTemp += ids[i].value + "-";
				}
			}
			if (strIds.length == 0) {
				swal({
					title : "提示",
					text : "请选择需要删除的的幻灯片！"
				});
				return false;
			} else {
				swal({
					title : "确定删除吗?",
					type : "warning",
					showCancelButton : true,
					confirmButtonColor : "#62cb31",
					confirmButtonText : "删除",
					cancelButtonText : '取消',
				}, function() {
					deleteAdvert(idTemp);
				});
			}
		});

		function AdvertListData() {
			if (typeof oTable == 'undefined') {
				// dataTable load
				oTable = $('#advertlist')
						.dataTable(
								{
									'bPaginate' : true, // 翻页功能
									'sServerMethod' : 'POST', // 提交方式
									'bServerSide' : true,
									'bProcessing' : true,
									'bFilter' : false,
									"bSort" : false,
									'sAjaxSource' : contextPath
											+ '/admin/advertInList',
									"fnServerParams" : function(aoData) { // 查询条件
										aoData.push({
											"name" : "title",
											"value" : $('[name=name]').val()
													.trim()
										}, {
											"name" : "message",
											"value" : $('[name=description]')
													.val().trim()
										});
									},
									aoColumns : [
											{
												'mData' : 'id',
												'sTitle' : '<input type="checkbox" id="checkedAll" name="checkedAll" onclick="checkedAll()">',
												'sWidth' : '2%',
												'mRender' : function(data) {
													var sRendor = '<input type="checkbox" name="ids" value="'+ data +'">';
													return sRendor;
												}
											},
											{
												'sTitle' : '排序',
												'sWidth' : '10%',
												'mData' : 'sortId'
											},
											{
												'sTitle' : '幻灯片',
												'sWidth' : '10%',
												'mData' : 'title'
											},
											{
												'sTitle' : '缩略图',
												'sWidth' : '20%',
												'mData' : 'url',
												'mRender' : function(data) {
													var returnValue = '<img src="/Shopping'+
													data+'" style="width:400px;"/>'
													return returnValue;
												}
											},
											{
												'sTitle' : '描述',
												'sWidth' : '30%',
												'mData' : 'message'

											},
											{
												'sTitle' : '操作',
												'mData' : 'id',
												'sWidth' : '9%',
												'mRender' : function(data) {
													var returnValue = '<button id="update" class="btn btn-default btn-sm" onclick="editAdvert('
															+ data
															+ ')"><i class="fa fa-pencil"></i>编辑</button>&nbsp&nbsp'
															+ '<button id="update" class="btn btn-default btn-sm" onclick="delAdvert('
															+ data
															+ ')"><i class="fa fa-pencil"></i>删除</button>';
													return returnValue;
												}
											} ]

								});
			} else {
				oTable.fnDraw();
			}
		}
		function editAdvert(id) {
			$("#id").val(id);
			$("#forwordform").submit();
		}

		function deleteAdvert(id) {
			$.ajax({
				type : "POST",
				contentType : "application/json;charset=utf-8",
				url : contextPath + '/admin/delAdvert',
				data : id,
				dataType : "json",
				async : false,
				success : function(json) {
					if (json.error == '0') {
						AdvertListData();
					} else {
						alert("幻灯片删除失败！");
					}
				},
				error : function(json) {
					existFlag = true;
				}
			});
		}

		function checkedAll() {
			if ($("#checkedAll").is(':checked') == true) {
				//全选
				$("input[name='ids']:checkbox").prop('checked', true);
			} else {
				//取消全选
				$("input[name='ids']").removeAttr("checked");
			}
		}
		function delAdvert(id) {
			swal({
				title : "确定删除吗?",
				type : "warning",
				showCancelButton : true,
				confirmButtonColor : "#62cb31",
				confirmButtonText : "删除",
				cancelButtonText : '取消',
			}, function() {
				deleteAdvert(id + '-');
			});
		}
	</script>
</body>
</html>
```

###### 5.advertEdit

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>   

<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<link rel="stylesheet" type="text/css" href="${pageContext.request.contextPath}/resources/admin/vendor/webuploader/webuploader.css">
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
<jsp:include page="../main/top.jsp" />
<jsp:include page="../main/left.jsp" />
<jsp:include page="../commonJs.jsp" />
<script src="${pageContext.request.contextPath}/resources/admin/vendor/jquery-validation/jquery.validate.js"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/bootstrap-fileinput-master/js/fileinput.js" type="text/javascript"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/bootstrap-fileinput-master/js/fileinput_locale_zh.js" type="text/javascript"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/webuploader/webuploader.js" type="text/javascript"></script>
<div id="wrapper">
	<div class="small-header transition">
		<div class="content">
			<div class="hpanel">
				<div class="panel-body">
					<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_advert" name="form_advert" action='<c:url value="/admin/save?${_csrf.parameterName}=${_csrf.token}" />'  method="POST">
									<input type="hidden" id="id" name="id" value='${advert.id}'/>	
									<div class="form-group col-lg-12">
										<label>幻灯片</label>
										<input type="text" id="title" name="title" class="form-control" maxlength="50" placeholder="幻灯片"	value='${advert.title}' />
										<input type="hidden" id="titleOld" value='${advert.title}'/>
									</div>
									<div class="form-group col-lg-12">
										<label>排序</label>
										<input type="text" id="sortId" name="sortId" class="form-control" maxlength="50" placeholder="排序" value='${advert.sortId}' />
									</div>
									<div class="form-group col-lg-12">
										<label>图片</label>
										<input type="hidden" id="url" name="url" value='${advert.url}' />
                                           <!--dom结构部分-->
										<div id="uploader-demo">
										    <!--用来存放item-->
										    <div id="fileList" class="uploader-list"></div>
										    <div id="filePicker">选择图片</div>
										    <div id="imageFile">
										    	<c:if test="${not empty advert.url}">
										    		<img src="${pageContext.request.contextPath}${advert.url}" style="width: 150px;height: 150px"/>
											    	</c:if>
											    	<c:if test="${empty advert.url}">
											    		<img src=""/>
											    	</c:if>
										    </div>
										</div>
                                           <div style='padding-top:10px'>上传图片的最佳尺寸：300像素*300像素，其他尺寸会影响页面效果，格式png，jpeg，jpg，gif。大小不超过1M</div>
									</div>
									<div class="form-group col-lg-12">
										<label>描述</label>
										<textarea rows="3" cols="20" id="message" name="message" class="form-control" maxlength="2000"></textarea>
										<input type="hidden" id="messageName" value='${advert.message}' />
									</div>
									<div class="text-left m-t-xs">
										<button id="save" class="btn btn-success" style="margin-left: 2px;" type="button">
											<strong>保存</strong>
										</button>
										<button id="back" class="btn btn-success" type="button">
											<strong>返回</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
<script type="text/javascript">
$('#leftMenuProduct').addClass('active');
	var oTable;
	var header = $("meta[name='_csrf_header']").attr("content");
	var token = $("meta[name='_csrf']").attr("content");
	var contextPath = '${pageContext.request.contextPath}';
	var validate;
	// 初始化Web Uploader
	var uploader = WebUploader.create({
	    // 选完文件后，是否自动上传。
	    auto: true,
	    // swf文件路径
	 	swf:  contextPath + '/resources/vendor/webuploader/Uploader.swf',
	    // 文件接收服务端。
	    server: contextPath + '/admin/newUploadfile?${_csrf.parameterName}=${_csrf.token}' ,
	    // 选择文件的按钮。可选。
	    // 内部根据当前运行是创建，可能是input元素，也可能是flash.
	    pick: '#filePicker',
	    // 只允许选择图片文件。
	    accept: {
	        title: 'Images',
	        extensions: 'gif,jpg,jpeg,bmp,png',
	        mimeTypes: 'image/*'
	    }
	});
	
	uploader.on( 'uploadAccept', function( object , ret ) {
// 		console.log(object);
// 		console.log(ret.fileName);
		$("#url").val(ret.fileName);
	});
	
	// 当有文件添加进来的时候
	uploader.on( 'fileQueued', function( file ) {
		 $('#imageFile').html('');
		 var $li = $(
		     '<div id="' + file.id + '" class="file-item">' +
		          '<img>' +
		     '</div>'
		 );
		 var $img = $li.find('img');
		 // $list为容器jQuery实例
		 $('#imageFile').append( $li );
		 
		 var ratio = window.devicePixelRatio || 1;
	     // 缩略图大小
	     var  thumbnailWidth = 150 * ratio;
	     var  thumbnailHeight = 150 * ratio;
         uploader.makeThumb( file, function( error, src ) {
			 if ( error ) {
			     $img.replaceWith('<span>不能预览</span>');
			     return;
			 }
	         $img.attr( 'src', src );
		 }, thumbnailWidth, thumbnailHeight );
	});
	
	$(function() {
		document.getElementById("message").innerText=$("#message").val();
		$(document).ajaxSend(function(e, xhr, options) {
			xhr.setRequestHeader(header, token);
		});
		validate = $("#form_advert").validate({
			onsubmit:false,
			focusInvalid:false,
			errorClass:"error",
			onfocusout:false,
			onkeyup:false,
			onclick:false,
			ignoreTitle:true,
	        rules:{
	        	name:{
                	required:true,
                	maxlength : 50,
                },
                sortOrder:{
                  	required:true,
                  	digits:true,
                  	maxlength : 4
                }
            },
            messages:{
            	name:{
                    required:"商品类别名必填",
                    maxlength : "最大位数不能超过50位",
                },
                sortOrder:{
                    required:"排序必填",
                    digits : "只能输入正整数",
                    maxlength : "只能输入4位整数"
                }                                   
            }
        });    
	});
	
	/** 返回  */
	$("#back").bind("click",function(){
		window.location.href =contextPath+'/admin/advert';
	});
	
	/** 保存  */
	$("#save").bind("click",function(){
		if (!$("#form_advert").valid()) {
			return;
		}
		$.ajax({
			type : "POST",
			contentType : "application/json;charset=utf-8", 
			url : contextPath + '/admin/advert/save',
			data : formToJson($("#form_advert")),
			dataType : "json", 
			async : false,
			success : function(json) {
				$("#id").val(json.data.id)
				swal({
				    title: "保存成功!",
				    text: "",
				    type: "success"
				});
			},
			error : function(json) {
				alert(json);
			}
		});
	});
	
	/** 获取from表单数据  */
	function formToJson($form) {
		var data = {};
		$($form.serializeArray()).each(function(i, v) {
			data[v.name] = v.value;
		});
		return JSON.stringify(data);
	}
	
</script>
</body>
</html>
```

#### 订单管理

###### 	Java compareTo() 方法	

​		如果参数字符串等于此字符串，则返回值 0；

​		如果此字符串小于字符串参数，则返回一个小于 0 的值；

​		如果此字符串大于字符串参数，则返回一个大于 0 的值。

```java
	/**
	 * 图表数据来源
	 * APIController
	 * @param dateTime
	 * @return
	 * @throws ParseException
	 */
	@RequestMapping(value = "/order/data", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject getOrderData(@RequestBody Map<String, Object> rmap) throws ParseException {
		DateFormat dateFormat = DateFormat.getDateInstance();
		Date beginDate = dateFormat.parse(rmap.get("beginDate").toString());
		Date endDate = dateFormat.parse(rmap.get("endDate").toString());
		if (beginDate.compareTo(endDate) >= 1) {
			return reponseJSON(ApiResponseEnum.FAIL.getCode(), ApiResponseEnum.FAIL.getName(), "开始日期不能大于结束日期");
		}
		return reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				orderService.getOrderDate(rmap));
	}
	//Service
	public Map<String, List<String>> getOrderDate(Map<String, Object> reqMap);	

	//ServiceImpl
	@Override
	public Map<String, List<String>> getOrderDate(Map<String, Object> reqMap) {
		String beginDate = reqMap.get("beginDate").toString();
		beginDate = beginDate.substring(0, 7) + "-01";
		String endDate = reqMap.get("endDate").toString();
		endDate = endDate.substring(0, 7) + "-01";
		List<String> listCount = new ArrayList<>();
		List<String> listDate = new ArrayList<>();
		List<String> listPrice = new ArrayList<>();
		List<Map<String, Object>> orderData = orderMapper.getOrderDate(beginDate, endDate);
		if (orderData != null) {
			for (Map<String, Object> map : orderData) {
				listDate.add(map.get("date").toString());
				listCount.add(map.get("count").toString());
				listPrice.add(map.get("price").toString());
			}
		}
		Map<String, List<String>> resultMap = new HashMap<>();
		resultMap.put("count", listCount);
		resultMap.put("date", listDate);
		resultMap.put("price", listPrice);
		return resultMap;
	}
	
	Mapper.java
        List<Map<String, Object>> getOrderDate(@Param("beginDate")String beginDate,@Param("endDate")String endDate);

	Mapper.xml
	<select id="getOrderDate" parameterType="java.lang.String"
		resultType="java.util.Map">
		SELECT
		COUNT( t3.order_num ) AS `count`,
		IFNULL( SUM( t3.price ), 0 ) / 1000000 AS `price`,
		DATE_FORMAT( t2.date, '%Y.%m' ) AS `date`
		FROM
		(
		SELECT
		@date := DATE_ADD( @date, INTERVAL - 1 MONTH ) AS `date`
		FROM
		( SELECT @date := DATE_ADD( #{endDate}, INTERVAL + 1 MONTH ) FROM
		order_info ) t1
		WHERE
		@date >= DATE_ADD( #{beginDate}, INTERVAL + 1 MONTH )
		) t2
		LEFT JOIN order_info t3 ON DATE_FORMAT( t2.date, '%Y.%m' ) = DATE_FORMAT(
		t3.create_time, '%Y.%m' )
		GROUP BY
		t2.date
	</select>
```



```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
	<jsp:include page="../main/top.jsp" />
	<jsp:include page="../main/left.jsp" />
	<jsp:include page="../commonJs.jsp" />
	<div id="wrapper">
		<div class="small-header transition animated fadeIn">
			<div class="content animate-panel">
				<div class="hpanel">
					<div class="panel-body">
						<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_userInfo" name="form_userInfo" method="POST">
									<div class="form-group col-lg-6">
										<label>选择区间</label>
										<div class="input-group date">
											<input name="searchTimeFrom" type="text" class="form-control"/>
											<span class="input-group-addon"><i
												class="glyphicon glyphicon-calendar"></i></span>
										</div>
									</div>
									<div class="form-group col-lg-6">
										<label></label>
										<div class="input-group date">
											<input name="searchTimeTo" type="text" class="form-control"/>
											<span class="input-group-addon"><i
												class="glyphicon glyphicon-calendar"></i></span>
										</div>
									</div>
									<div class="text-right m-t-xs">
										<button id="search" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>查询</strong>
										</button>
										<button id="reset" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px"
											style="margin-right: 20px;" type="reset">
											<strong>重置</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>

			</div>

		</div>
		<div class="content animate-panel">
			<div class="row">
				<div class="col-lg-12">
					<div class="hpanel">
						<div class="panel-body">
							<div id="char" style="width: 100%; height: 600px;"></div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>

	<form id="forwordform"
		action='<c:url value="/admin/order/History?${_csrf.parameterName}=${_csrf.token}" />'
		method='POST'>
		<input type="hidden" name="userId" id="userId" /> <input
			type="hidden" name="roleStr" id="roleStr" /> <input type="hidden"
			name="orderNum" id="ordernum">

	</form>
	<script type="text/javascript">
		$('#leftMenuOrder').addClass('active');
		$('.date').datepicker();
		var myChart = echarts.init(document.getElementById('char'));
		var option = {
	            tooltip: {},
	            legend: {
	                data:['订单数(条)','订单金额(万元)']
	            },
	            xAxis: {
	                data: ${data.date}
	            },
	            yAxis: {},
	            series: [{
	                name: '订单数(条)',
	                type: 'bar',
	                data: ${data.count},
	                markPoint: {
	                   data: [
	                       {type: 'max'},
	                       {type: 'min'}
	                   ]
	               },
	               markLine: {
	                   data: [
	                       {type: 'average'}
	                   ]
	               }

	            },
	            {
	            	name: '订单金额(万元)',
	                type: 'bar',
	                data: ${data.price},
	                markPoint: {
	                   data: [
	                       {type: 'max'},
	                       {type: 'min'}
	                   ]
	               },
	               markLine: {
	                   data: [
	                       {type: 'average'}
	                   ]
	               }

	            }
	            ]
	        };
		myChart.setOption(option);
		$('#search').click(function() {
			var beginDate=$('[name=searchTimeFrom]').val();
			var endDate=$('[name=searchTimeTo]').val();
			if (beginDate==""||endDate=="") {
				return false;	
			}
			searchOrderData(beginDate,endDate);
		});
		
		function searchOrderData(beginDate,endDate){
			$.ajax({
				contentType : 'application/json;charset=utf-8',
				url : '/Shopping/api/v1/order/data',
				type : "POST",
				data : JSON.stringify({"beginDate":beginDate,"endDate":endDate}),
				dataType : "json", //ajax返回值设置为text(json格式也可用它返回，可打印出结果，也可设置成json)  
				success : function(result) {
					result=result.data;
					console.log(result);
					option.xAxis.data=result.date;
				option.series[0].data=result.count;
					option.series[1].data=result.price;
					myChart.setOption(option);
				}
			});
		}
	</script>
</body>
</html>
```

#### 物流管理

#### 动态详情管理

#### 动态标签管理

```java
//Service
	public Map<String, Object> getDataDivt(PageInformation pageInfo,Dict dict);
	
	public Dict getdictByid(int id);
	
	public int saveDict(Dict dict);
	
	public int updDict(Dict dict);
```

```java
//ServiceIpml
	@Override
	public Map<String, Object> getDataDivt(PageInformation pageInfo,Dict dict) {
		HashMap<String, Object> datas = new HashMap<String, Object>();
		
		DictExample example=new DictExample();
		Criteria criteria = example.createCriteria();
		criteria.andDictTypeEqualTo("10");
		
		if(dict.getRemark()!=null&&dict.getRemark()!="") {
			criteria.andRemarkLike("%"+dict.getRemark()+"%");
		}
		if(dict.getDictName()!=null&&dict.getDictName()!="") {
			criteria.andDictNameLike("%"+dict.getDictName()+"%");
		}
		long pageCount=dictMapper.countByExample(example);
		List<ProductCategory> list = pagingTool.PagingData("com.sybinal.shop.mapper.DictMapper.selectByExample", example,
				(Integer.parseInt(pageInfo.getiDisplayStart())/Integer.parseInt(pageInfo.getiDisplayLength()))+1,Integer.parseInt(pageInfo.getiDisplayLength()));
		
		datas.put("sEcho", pageInfo.getsEcho());
		datas.put("iTotalRecords", pageCount);
		datas.put("iTotalDisplayRecords", pageCount);
		datas.put("aaData", list);
		return datas;
	}

	@Override
	public Dict getdictByid(int id) {
		// TODO Auto-generated method stub
		return dictMapper.selectByPrimaryKey(id);
	}
	@Override
	public int saveDict(Dict dict) {
		// TODO Auto-generated method stub
	
		return 	dictMapper.insertSelective(dict);
	}

	@Override
	public int updDict(Dict dict) {
		// TODO Auto-generated method stub
		return dictMapper.updateByPrimaryKeySelective(dict);
	}
```

```java
//Controller
	@RequestMapping(value = "/admin/dictInfoDataTable", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> productCategorynfoDataTable(PageInformation pageInfo,Dict dict){
		return noteService.getDataDivt(pageInfo, dict);
	}
	
	@RequestMapping(value = "/admin/receiveUpdateDict")
	public @ResponseBody Dict receiveUpdateDict(@RequestBody int id){
		return noteService.getdictByid(id);
	}
	
	@RequestMapping(value = "/admin/receiveSaveDict")
	public @ResponseBody int receiveSaveDict(@RequestBody Dict dict){
		dict.setDictType("10");
		dict.setDictCode("1");
		dict.setFlagSort(10);
		dict.setFlagStatus(0);
		dict.setMakeEmp(UserUtils.getUserName());
		dict.setMakeDate(new Date());
		dict.setModifyDate(new Date());
		dict.setModifyEmp(UserUtils.getUserName());
		return noteService.saveDict(dict);
	}
	
	@RequestMapping(value = "/admin/receiveupdDict")
	public @ResponseBody int receiveupdDict(@RequestBody Dict dict){
		dict.setModifyDate(new Date());
		dict.setModifyEmp(UserUtils.getUserName());
		return noteService.updDict(dict);
	}
```

```jsp
//jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
	<jsp:include page="../main/top.jsp" />
	<jsp:include page="../main/left.jsp" />
	<jsp:include page="../commonJs.jsp" />
	<div id="wrapper">
		<div class="small-header transition animated fadeIn">
			<div class="content animate-panel">
				<div class="hpanel">
					<div class="panel-body">
						<div id="error_div" class="text-left"></div>
						<form id="form_userInfo" name="form_userInfo" method="POST">
							<div class="row">
								<div class="form-group col-lg-6">
									<label>标签名</label> 
									<input type="text" name="dictName"	class="form-control" maxlength="50"  />
								</div>
								<div class="form-group col-lg-6">
									<label>备注</label> 
									<input type="text" name="remark" class="form-control" maxlength="50" placeholder="分类名" />
								</div>
							</div>
							<div class="row">
								<div class="form-group col-lg-12">
									<div class="text-right m-t-xs">
										<button id="search" class="btn btn-success" type="button">
											<strong>查询</strong>
										</button>
										<button id="reset" class="btn btn-success" type="reset">
											<strong>重置</strong>
										</button>
										<button id="new" onclick="resetId()" data-toggle="modal" data-target="#myModal" class="btn btn-success"
											type="button">
											<strong>新增</strong>
										</button>
									</div>
								</div>
							</div>
						</form>
					</div>
				</div>
			</div>
		</div>
		<div class="content animate-panel">
			<div class="row">
				<div class="col-lg-12">
					<div class="hpanel">
						<div class="panel-body">
							<table id="userInfoList" style="width: 100%;"
								class="table table-striped table-bordered table-hover"></table>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
	
		<div class="text-center">
              <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-hidden="true">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="color-line"></div>
                                <div class="text-center col-lg-12" style="width: 100%;height: 60px;background-color: white;">
                                    <h6 class="modal-title">修改/新增</h6>
                                    <small class="font-bold"></small>
                                </div>
                                <div class="modal-body">
                                <form id="form_saveArticleCategory name="form_userInfo" action="" method="">
										<div class="form-inline">
											<strong>标签名:</strong>
											
											<input type="hidden"  id="dictId"/>	
												<input type="text"  id="dictName" onchange="illegalChar(this)"
													class="form-control" maxlength="50" placeholder="标签名"/>	
														&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp<strong>备注:</strong>
													<input type="text"  id="remark" onchange="illegalChar(this)"
													class="form-control" maxlength="50" placeholder="备注信息"/>
											
										</div>
										<div class="text-right m-t-xs">
												<button id="save" onclick="saveDict()" class="btn btn-success" style="margin-right: 5px;margin-top: 5px"
													type="button">
													<strong>保存</strong>
												</button>
												<button type="button" id="over" class="btn btn-default" data-dismiss="modal"><strong>关闭</strong></button>
									     </div>
									</form>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
	
	<script type="text/javascript">
		$('#leftMenuArticle').addClass('active');
		var oTable;
		//网页传输过来“头数据”+“token”
		var header = $("meta[name='_csrf_header']").attr("content");
		var token = $("meta[name='_csrf']").attr("content");

		$(function() {
			//日期插件
			$('.date').datepicker(); 
			$(document).ajaxSend(function(e, xhr, options) {
				xhr.setRequestHeader(header, token);
			});
			userInfoListData();
		});
		$('#search').click(function() {	
				userInfoListData();
			
		});
		function saveDict() {
			var saveVo = {};
			var ur;
			if ($("#dictName").val() != "" && $("#dictName").val() != undefined) {
				saveVo.dictName = $("#dictName").val();
				saveVo.remark = $("#remark").val();
				if ($("#dictId").val() == "" || $("#dictId").val() == undefined) {
					ur = "admin/receiveSaveDict";
				} else {
					ur = "admin/receiveupdDict";
					saveVo.id = $("#dictId").val();
				}

				$.ajax({
					type : "POST",
					url : "${pageContext.request.contextPath}/" + ur,
					data : JSON.stringify(saveVo),
					contentType : "application/json;charset=utf-8",
					dataType : "json",
					success : function(data) {
						$("#over").click();
						showcommon(data);
						userInfoListData();
					}
				});
			}

		}
		
		function resetId(){
			$("#dictId").val("");
			$("#dictName").val("");
			$("#remark").val("");
		}
		
		function update(id) {
			$.ajax({
						type : "POST",
						url : "${pageContext.request.contextPath}/admin/receiveUpdateDict",
						data : JSON.stringify(id),
						contentType : "application/json;charset=utf-8",
						dataType : "json",
						success : function(data) {
							$("#dictId").val(data.id);
							$("#dictName").val(data.dictName);
						}
					});
		}
		
		function showcommon(data) {
			var type = "";
			var title = "";
			if (data == 1) {
				type = "success";
				title = "成功";
			} else {
				type = "error";
				title = "失败";
			}
			swal({
				title : title,
				type : type
			});
		}

		function removeArticle(i) {
			swal(
					{
						title : "确定要删除该条记录?",
						type : "warning",
						showCancelButton : true,
						confirmButtonColor : "#DD6B55",
						confirmButtonText : "是",
						cancelButtonText : "否",
						closeOnConfirm : false,
						closeOnCancel : true
					},
					function(isConfirm) {
						if(isConfirm) {
							$.ajax({
								type : "POST",
								url : "${pageContext.request.contextPath}/admin/receiveDelArticle",
								data : JSON.stringify(i),
								contentType : "application/json;charset=utf-8",
								dataType : "json",
								success : function(data) {
									showcommon(data);
									userInfoListData();
								}
						    });
						}
					});

		}
		function userInfoListData() {
			var contextPath = '${pageContext.request.contextPath}';
			if (typeof oTable == 'undefined') {
				// dataTable load
				oTable = $('#userInfoList').dataTable({
									'bPaginate' : true, // 翻页功能
									'sServerMethod' : 'POST', // 提交方式
									'bServerSide' : true,
									'bProcessing' : true,
									'bFilter' : false,
									"bSort" : false,
									'sAjaxSource' : contextPath+ '/admin/dictInfoDataTable',
									"fnServerParams" : function(aoData) { // 查询条件
										
										aoData.push(
														{
															"name" : "dictName",
															"value" : $('[name=dictName]').val()
														},{
															"name" : "remark",
															"value" : $('[name=remark]').val()
														}

													);
									},
									aoColumns : [
											{
												'mData' : 'id',
												'sTitle' : '编号',
												'visible' : false
											},
											{
												'sTitle' : '标签名',
												'mData' : 'dictName'
											},
											{
												'sTitle' : '分类名',
												'mData' : 'dictType'
											},
											{
												'sTitle' : '最后更新时间',
												'mData' : 'makeDate'
											},
											{
												'sTitle' : '备注',
												'mData' : 'remark'
											},
											{
												'sTitle' : '操作',
												'mData' : 'id',
												'sWidth' : '10%',
												'mRender' : function(data) {
													var returnValue = '<button id="update"  onclick="update('
															+ data
															+ ')"  data-toggle="modal" data-target="#myModal"  class="btn btn-default btn-sm"><i class="fa fa-pencil"></i>编辑</button><button id="remove" onclick="removeArticle('
															+ data
															+ ')" class="btn btn-default btn-sm"><i class="fa fa-pencil"></i>删除</button>';
													return returnValue;
												}
											} ]

								});
			} else {
				var oSettings = oTable.fnSettings();
				oSettings._iDisplayLength = parseInt($('[name=userInfoList_length] option:selected').val());
				$('.dataTables_length select').val($('[name=userInfoList_length] option:selected').val());
				oSettings._iDisplayStart = 0;
				oTable.fnDraw();
			}
		}

		function illegalChar(data) {
			var re = /^[0-9a-zA-Z\u4e00-\u9fa5]+$/;
			if (!re.exec(data.value)) {
				data.value = "";
			}
		}
		function openPostWindow(params) {
			$("#ArticleId").val(params);
			$("#forwordform").submit();
		}
	</script>
</body>
</html>
```



####  动态评论管理

## Mobile

#### Mobile充值(18~20)

后端实体时间get()方法前面加 时间格式化

@JsonFormat(pattern="yyyy-MM-dd HH:mm:ss",timezone = "GMT+8") 

```vue
<template>
  <div>
    <mHeader>钱包充值</mHeader>
    <div style="text-align:left">
      <mt-cell title="电子钱包充值"></mt-cell>
      <mt-field label="当前余额" v-model="balance" disabled></mt-field>
      <mt-field
        label="充值金额"
        v-model="money"
        placeholder="请输入充值金额"
      ></mt-field>
      <mt-button plain size="large" type="danger" @click="rechange"
        >确认充值</mt-button
      >
    </div>
    <mt-cell>
      <div class="main" slot="title">
        <span style="margin-left: 8px;">ID</span>
        <span style="margin-right:35px;">充值金额</span>
        <span style="margin-right:40px;">充值时间</span>
      </div>
    </mt-cell>
    <mt-cell v-for="(item, index) in cc" :key="index">
      <div class="main" slot="title">
        <span>{{ item.id }}</span>
        <span>{{ item.amount }}</span>
        <span>{{ item.updTime }}</span>
      </div>
    </mt-cell>
  </div>
</template>
```

```vue
<script>
import mHeader from "./header";
export default {
  components: { mHeader },
  data() {
    return {
      balance: null,
      money: null,
      cc: []
    };
  },
  created() {
    this.getbalance();
  },
  methods: {
    getbalance() {
      this.$post(this.$api.API_URL_CUSTOMER_Account, {
        userId: this.$store.getters.user.id
      }).then(res => {
        this.getcc();
        this.balance = res.data.amount;
      });
    },
    rechange() {
      if (this.money > 0) {
        this.$post(this.$api.API_URL_CUSTOMER_Recharge_Account, {
          userId: this.$store.getters.user.id,
          amount: this.money
        }).then(res => {
          if (res.errorCode == 0) {
            this.$toast("充值成功");
          }
        });
      }
    },
    getcc() {
      this.$post(this.$api.API_GETCC, {
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.cc = res.data;
        }
      });
    }
  }
};
</script>
```

```vue
<style>
.main {
  display: flex;
  justify-content: space-between;
}
</style>
```

```java
mapper	
<select id="getAccountByUserId" parameterType="int"  resultType="com.sybinal.shop.model.Account">
		SELECT a.id,a.amount,a.updtime FROM account a JOIN `user` b ON a.username = b.user_name WHERE b.id = 123518  and a.source="充值"
	</select>
        
service
    @Override
	public List<Account> getAccountByUserId(Map<String, Object> reqMap) {
		int userId = Integer.parseInt(reqMap.get("userId").toString());
		return accountMapper.getAccountByUserId(userId);
	}

controller
    	@RequestMapping(value = "/account/getcc", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject getrecord(@RequestBody Map<String, Object> reqMap) {

		return reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				accountService.getAccountByUserId(reqMap));

	}
```

#### 动态上传&发布动态（50以内）

```vue
mounted() {
    let that = this,
      src = [],
      list = [];
    // 初始化Web Uploader
    var uploader = WebUploader.create({
      // 选完文件后，是否自动上传。
      auto: true,
      // swf文件路径
      swf: "/static/uploader/Uploader.swf",
      // 文件接收服务端。
      server: this.$api.API_uploadImg,
      // 选择文件的按钮。可选。
      // 内部根据当前运行是创建，可能是input元素，也可能是flash.
      pick: "#picker",
      // 只允许选择图片文件。
      accept: {
        title: "Images",
        extensions: "gif,jpg,jpeg,bmp,png",
        mimeTypes: "image/*"
      }
    });
    uploader.on("error", function(type) {
      if (type == "Q_TYPE_DENIED") {
        that.$toast("请选择图片！");
        uploader.destroy();
        return;
      }
    });

    // 当有文件添加进来的时候
    uploader.on("fileQueued", function(file) {
      uploader.makeThumb(
        file,
        function(error, sc) {
          if (!that.$store.getters.user) {
            that.$toast("请先登录");
            uploader.destroy();
            return;
          }
          if (error) {
            that.$toast(error);
            return;
          }
          src.push(sc);
        },
        100,
        100
      );
    });

    uploader.on("uploadAccept", function(object, ret) {
      list.push(ret);
    });

    uploader.on("uploadSuccess", function(object, ret) {
      that.Storage.Local.set("res", list);
      that.$store.commit("setFile", src);
      that.$router.push("/samsara/form");
      uploader.destroy();
    });
  },
```



```vue
<template
  ><div>
    <mHeader>提交动态</mHeader>
    <div class="img">
      <img :src="item" v-for="(item, index) in src" :key="index" />
    </div>

    <div style="text-align:left">
      <mt-cell title="选择标签" @click.native="show = true"> </mt-cell>
      <mt-cell
        v-for="(item, index) in re"
        :key="index"
        :title="item.dictName"
      ></mt-cell>
        
    </div>
    <mt-popup v-model="show" class="popup" position="bottom">
      <van-checkbox-group v-model="re">
        <van-cell
          clickable
          :title="item.dictName"
          v-for="(item, index) in num"
          @click="toggle(index)"
          :key="item.id"
        >
          <van-checkbox :name="item" ref="box" slot="right-icon"></van-checkbox>
        </van-cell>
      </van-checkbox-group>
    </mt-popup>
    <mt-field
      v-model="values"
      type="textarea"
      rows="4"
      placeholder="这一刻，想说些什么！"
    ></mt-field>

    <mt-button plain type="primary" size="large" @click="save"
      >提交动态</mt-button
    >
  </div>
</template>
```

```vue
<script>
import mHeader from "../other/header";
export default {
  components: { mHeader },
  data() {
    return {
      src: [],
      list: [],
      show: false,
      re: [],
      num: [],
      values: null
    };
  },
  mounted() {
    this.$post(this.$api.API_getLabel, {
      dictType: 10
    }).then(res => {
      if (res.errorCode == 0) {
        this.num = res.data;
      }
    });
    this.src = this.$store.state.file;
    this.list = this.Storage.Local.get("res");
  },
  methods: {
    toggle(index) {
      this.$refs.box[index].toggle();
    },
    save() {
      let biao = this.re.map(val => {
        dataDictId: val.id;
      });
      console.log(this.list);
      var aw;
      aw = {
        attachmentName: this.list[0].attachmentName,
        attachmentPath: this.list[0].attachmentPath
      };
      console.log(aw);
      let attachment_rows_data = [];

      attachment_rows_data.push(aw);
      console.log(attachment_rows_data);

      if ((biao.lenth != 0)) {
        this.$post(this.$api.API_SET_NOTEDATA, {
          dataDictId: 0,
          attachment_rows_data: attachment_rows_data,
          makeEmp: this.$store.getters.user.id,
          noteContent: this.values,
          label_rows_data: biao
        }).then(res => {
          if (res.errorCode == 0) {
              this.$router.push("/");
          }else{
               this.$toast("网络错误！");
          }
        });
      } else {
        this.$toast("请输入标签！");
      }
    }
  }
};
</script>
```

```vue
<style  >
.img {
  display: flex;
  flex-wrap: wrap;
  background-color: #cccccc;
}

.imgitem {
  width: 100px;
  height: 100px;
  margin: 10px;
  border-radius: 10%;
} 

.popup {
  width: 100%;
}
</style>
```

#### Mobile首页瀑布流（30）

```vue
<template>
  <div>
    <van-search
      placeholder="请输入你想要搜索的内容"
      show-action
      v-model="value"
    >
      <div slot="action" @click="$router.push('/samsara/search/' + value)">
        搜索
      </div>
    </van-search>

    <div
      class="main"
      infinite-scroll-disabled="loading"
      infinite-scroll-distance="10"
      v-infinite-scroll="more"
    >
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) in img1"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
               //img宽度需要100% 不然变形
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="dis">
              <img src="static/1.jpg" alt="" />
              <span>{{ item.userName }}</span>
            </div>
            <div class="dis">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) in img2"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="dis">
              <img src="static/1.jpg" alt="" />
              <span>{{ item.userName }}</span>
            </div>
            <div class="dis">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
```

```vue
<script>
export default {
  data() {
    return {
      value: null,
      count: 1,
      pageCount: 0,
      img1: [],
      img2: [],
    };
  },
  mounted() {
    this.$post(this.$api.API_getDict, {
      userName: this.$store.getters.user.userName,
      dataType: 2,
      nowPage: this.count,
    }).then((res) => {
      if (res.errorCode == 0) {
          //一定让要拿到页数 
        this.pageCount = res.data.pageCount;
        this.$store.commit("setDict", res.data.rows_data);
        res.data.rows_data.forEach((val, index) => {
          if (index % 2) {
            this.img2.push(val);
          } else {
            this.img1.push(val);
          }
        });
      } else {
        this.$dialog.alert({ message: "网络错误！" });
      }
    });
  },
  methods: {
    nav(id) {
      this.$router.push("/samsara/indexdetail/" + id);
    },
    more() {
      if (this.count <= this.pageCount) {
        this.count += 1;
        this.$post(this.$api.API_getDict, {
          userName: this.$store.getters.user.userName,
          dataType: 2,
          nowPage: this.count,
        }).then((res) => {
          if (res.errorCode == 0) {
            this.$store.commit("addDict", res.data.rows_data);
            res.data.rows_data.forEach((val, index) => {
              if (index % 2) {
                this.img2.push(val);
              } else {
                this.img1.push(val);
              }
            });
          } else {
            this.$toast("网络错误！");
          }
        });
      }
    },
  },
};
</script>
```

```vue
<style>
.main {
  display: flex;
  justify-content: space-between;
}
.col {
  display: flex;
  flex-direction: column;
  flex: 1;
}
.item {
  padding: 5px;
}
.img {
  width: 100%;
  border-radius: 10px 10px 0 0;
}
.user {
  align-items: center;
  font-size: 0.6em;
  display: flex;
  justify-content: space-between;
  margin: 5px;
}
.dis {
  display: flex;
  justify-content: center;
  align-items: center;
}
.dis > img {
  margin-right: 5px;
  width: 20px;
  height: 20px;
  border-radius: 20px;
}
.dis > span {
  margin-left: 5px;
}
</style> 
```

#### 商品六宫格

```java
	/**
	 * Controller
	 * 根据分页与时间查询热门商品
	 */
	@RequestMapping(value = "catalog/product/pagesize/{pageSize}", method = RequestMethod.GET, headers = "Accept=application/json")
	public ApiResponseObject searchProductByHott(@PathVariable("pageSize") int  pageSize) {
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), productService.getProductHotbytime(pageSize));
	}
```

```java
	/**
	 * Service
	 * @param pagesize
	 * @return 商品列表
	 */
	List<Product> getProductHotbytime(int pagesize);
```

```java
	/**
	 * ServiceImpl
	 */
	@Override
	public List<Product> getProductHotbytime(int pagesize) {
		// TODO Auto-generated method stub
		ProductExample example = new ProductExample();
		Criteria criteria = example.createCriteria();
		criteria.andProductStatusEqualTo(0);
		criteria.andHotEqualTo(1);
		example.setOrderByClause("create_time DESC");

		List<Product> pagingData = pagingTool.PagingData("com.sybinal.shop.mapper.ProductMapper.selectByExample",
				example, 1, pagesize);

		return pagingData;
	}
```

```vue
 1. API_URL_CATALOG_HOTT:v1+'catalog/product/pagesize'

 2. this.$get(this.$api.API_URL_CATALOG_HOTT + "/6").then(res => {
      this.hot = res.data;
    });
 3.
	<div>
        <div class="title">热门商品</div>
        <div class="smallbox" >
          <div class="hotitem" v-for="(hotitem, index) in hot" :key="index">
            <img :src="$api.BASE_SERVER_URL + hotitem.defaultImg" alt="" />
            <div>{{hotitem.name}}</div>
          </div>
        </div>
      </div>

4.	
.smallbox{
  padding 5px
  display flex
  flex-wrap wrap
  background #ffffff
}
.hotitem{
  width 33%
  display flex
  flex-direction column
}
.hotitem>img{
  width 100%
  height: 33vw;
   margin 5px 0
}
.hotitem>div{
  text-overflow ellipsis
  overflow hidden
  white-space nowrap
}

```

#### 首页轮播图

####  登录/注册

此页面有登录和注册两大功能，通过登录确定用户身份后， 可以下单、评论、进入用户中心。通过简单的添写注册信 息，可以快速的成为网站的客户，在添写信息时，实时的验证并提醒用户名是否已被使用，密码是否足够安全。使用 axios技术。所有必须以客户身份访问或操作的页面都会经 过此页面进行登录或注册

###### 登录

```vue
<template>
  <div class="login">
    <mt-header fixed title="用户登录" style="font-size:20px;"></mt-header>
    <div class="container">
      <img alt="" src="static/1.jpg" style="width: 20vw;
    height: 20vw;
    border-radius: 50%;
    position: absolute;
    top: 10vh;
    left: 40vw;">
      <mt-cell>
        <mt-field label="用户名" v-model="userName" class="cell"></mt-field>
        <img slot="icon" src="static/img/user.png" style="margin-top:-20px"></span>
      </mt-cell>
      <mt-cell>
         <mt-field label="密码"   v-model="userPassword" type="password"  class="cell"></mt-field>
         <img slot="icon" src="static/img/eye.png" style="margin-top:-20px"></span>
      </mt-cell>
  
      <mt-button  @click="login" block class="cell"  large size="large" square type="primary">登录</mt-button>
      <mt-button  @click="$router.push({path:'/reg'})" block class="cell"  plain size="large" square type="default">注册</mt-button>
      <div class="cell spear">
        <router-link to="/recover">忘记密码</router-link>
         <img slot="icon" src="static/img/love3.png">
      </div>
    </div>
  </div>
</template>

<script>
  import header from './other/header'

  export default {
    data() {
      return {
        userName: '',
        userPassword: '',
      }
    },
    components: {'m-header': header},
    methods: {
      login() {
        var mobile = /^(13[0-9]{9})|(18[0-9]{9})|(14[0-9]{9})|(17[0-9]{9})|(15[0-9]{9})$/;
        if (!this.userName) this.$dialog.alert({message: '请填写手机号❤'})
        else if (!this.userPassword) this.$dialog.alert({message: '请填写密码❤'})
        else if (!mobile.test(this.userName)) this.$dialog.alert({message: '请正确填写您的手机号码❤'})
        else if (this.userName.length > 11) this.$dialog.alert({message: '手机号码必须为11位❤'})
        else this.$post(this.$api.API_URL_LOGIN, {userName: this.userName, pwd: this.userPassword}).then(res => {
            if (res.errorCode == '0') {
              this.$store.commit('setUser', res.data)
              this.$router.push({path: '/me'})
            } else this.$dialog.alert({message: res.data})
          })
      },
    }
  }

</script>

<style scoped lang="stylus">
  .container
    margin 25vh 10px 

    .cell
      margin-bottom 20px

    .spear
      display flex
      justify-content space-between
      padding 0 10px

</style>

```



###### 注册

```vue
<template>
  <div class="reg">
    <m-header>用户注册</m-header>
    <div class="container">
      <div class="cell">
         <div class="hezi">
         <img class="img" src="/static/img/dianhua.png" style="float:left;height:1.1em;width:1.1em;" >
          <mt-field label="电话号:" placeholder="请输入电话号码" type="tel" v-model="phone">
          </mt-field>
         <mt-button class="button" slot="button" size="small" @click="send" type="primary">发送验证码</mt-button>
        </div>
        

 <div class="hezi">
         <img class="img" src="/static/img/duanxin.png" style="float:left;height:1.1em;width:1.1em;" >
          <mt-field class="duanxin" label="短信验证码:" placeholder="请输入短信验证码" type="tel" v-model="sms">
          </mt-field>
        </div>
        
        <div class="hezi">
         <img class="img" src="/static/img/close-eye.png" style="float:left;height:1.1em;width:1.1em;" >
          <mt-field label="密码:" placeholder="请输入密码" type="password" v-model="userPassword">
          </mt-field>
        </div>
        
         <div class="hezi">
         <img class="img" src="/static/img/close-eye.png" style="float:left;height:1.1em;width:1.1em;" >
          <mt-field class="queren" label="确认密码:" placeholder="" type="password" v-model="rePassword">
          </mt-field>
        </div>
       
      </div>
      <mt-button @click="reg" block class="cell" large size="large" square type="primary">立即注册</mt-button>
    </div>
  </div>


</template>

<script>
  import header from './other/header'

  export default {
    data () {
      return {
        sms: '',
        userPassword: '',
        rePassword: '',
        phone: '',
        test: /^(13[0-9]{9})|(18[0-9]{9})|(14[0-9]{9})|(17[0-9]{9})|(15[0-9]{9})$/
      }
    },
    components: {'m-header': header},
    methods: {
      reg () {
        if (!this.phone) this.$dialog.alert ({message: '请填写手机号❤'})
        else if (!this.userPassword) this.$dialog.alert ({message: '请填写密码❤'})
        else if (!this.test.test (this.phone)) this.$dialog.alert ({message: '请正确填写您的手机号码❤'})
        else if (this.phone.length > 11) this.$dialog.alert ({message: '手机号码必须为11位❤'})
        else if (!this.sms) this.$dialog.alert ({message: '请填写验证码❤'})
        else if (!this.userPassword) this.$dialog.alert ({message: '请填写密码❤'})
        else if (!this.rePassword) this.$dialog.alert ({message: '请重复密码❤'})
        else if (this.userPassword != this.rePassword) this.$dialog.alert ({message: '两次密码输入不一致❤'})
        else this.$post(this.$api.REGISTER, {userName: this.phone, pwd: this.userPassword, amount: 0}).then (res => {
            if (res.errorCode == '0') {
              this.$dialog.alert ({message: '注册成功'}).then (ree => {
                this.$router.push ({path: '/login'})
              })
            } else this.$dialog.alert ({message: '注册失败'})
          })
      },
      send () {
        if (!this.phone) this.$dialog.alert ({message: '请填写手机号❤'})
        else if (!this.test.test (this.phone)) this.$dialog.alert ({message: '请正确填写您的手机号码❤'})
        else if (this.phone.length > 11) this.$dialog.alert ({message: '手机号码必须为11位❤'})
        else this.$post(this.$api.API_URL_CUSTOMER, {userName: this.phone}).then (res => {
            if (res.errorCode == '-3') this.$post(this.$api.SEND_SMS, {userName: this.phone}).then (ret => {
              if (ret.errorCode == '0') this.$dialog.alert ({message: '短信已发送'}).then (() => this.sms = ret.data)
              else this.$dialog.alert ({message: '短信发送失败'})
            })
            else this.$dialog.alert ({message: '您已注册,请登录'})
          })
      }
    }
  }

</script>

<style scoped lang="stylus">
  .container
    margin 20vh 10px

    .cell
      margin-bottom 20px
.hezi
  position relative;
  top 0;
  left 0;
.button
     position absolute;
     right 0;
     top 10px;
.img
  position absolute;
  left 12px;
  top 15px;
  z-index  999;
.duanxin
  margin-left 17px;
.queren
  margin-left 6px;
</style>

```



####  商品分类

```vue
      <div v-for="(category, index) in productList" :key="index">
        <div class="title">{{ category.name }}</div>
        <van-cell
          :to="'/samsara/productdetail/' + item.id"
          v-for="(item, index) in category.productRelationList"
          :key="index"
          is-link
        >
          <img
            slot="icon"
            :src="$api.BASE_SERVER_URL + item.defaultImg"
            height="50"
            width="50"
            alt=""
          />
          <span>
            <div class="productNa">{{ item.name }}</div>
            <div>
              <span class="shopp"
                >店面价格:
                <span class="shops">￥{{ item.shopPrice / 100 }}</span></span
              >
              <span class="shopp"
                >市场价格:
                <span class="shopz">￥{{ item.price / 100 }}</span></span
              >
            </div>
          </span>
        </van-cell>
        <van-cell
          :to="'../../samsara/category/' + category.id"
          is-link
          title="查看更多"
        ></van-cell>
      </div>
```

```js
  	/* 查询商品 */
  	API_URL_CATALOG_INDEX_PRODUCTS: v1 + "catalog/category/product",   

	this.$post(this.$api.API_URL_CATALOG_INDEX_PRODUCTS + "/3").then(res => {
      this.productList = res.data;
    });
```

```CSS
.productNa{
	margin-left:15px;
    overflow: hidden;
	white-space: nowrap;
	text-overflow:ellipsis;
	font-size:16px
}
.shopp{
  	margin-left:15px;
  	color #cccccc

}
.title{
  	text-align left
  	margin-left 10px
}
.shops{
  	color #209F23
}
.shopz{
 	text-decoration line-through
}
```

####  收藏

###### 后端

```java
//Service
	public void saveUserWishlist(Wishlist wishlist);

	public boolean delUserWishlist(Map<String, Object> data);

	public boolean delWishlistById(Map<String, Object> reqMap);
	
	public Map<String, Object> getUserWishlist(Map<String, Object> data);

	public boolean selectIsWishlist(Wishlist wishlist);
	
	public boolean delWishlistByProductId(Map<String, Object> data);

//ServiceImpl

	@Autowired
	WishlistMapper wishlistMapper;
	
	@Autowired
	ProductMapper productMapper;
	

	@Autowired
	PagingTool pagingTool;

	@Override
	public Map<String, Object> getUserWishlist(Map<String, Object> data) {
		Object condition = null;
		long totalCount = wishlistMapper.selectRelationCountByPrimaryKey((int) data.get("userId"));
		/* 当前页 */
		int currentPage = 1;
		if ((condition = data.get("currentPage")) != null)
			currentPage = (int) condition < 1 ? 1 : (int) condition;
		else
			data.put("currentPage", currentPage);
		/* 分页大小 */
		int pageSize = 8;
		if ((condition = data.get("pageSize")) != null)
			pageSize = (int) condition;
		else
			data.put("pageSize", pageSize);
		long totalPage = Page.confirmPage(totalCount, pageSize);
		if (currentPage > totalPage) {
			currentPage = (int) totalPage;
			data.put("currentPage", currentPage);
		}
		data.put("totalPage", totalPage);
		data.put("totalCount", totalCount);
		data.put("wishlist", pagingTool.PagingData("com.sybinal.shop.mapper.WishlistMapper.selectRelationByPrimaryKey", (int) data.get("userId"), currentPage, pageSize));
		return data;
	}
	
	@Override
	@Transactional
	public void saveUserWishlist(Wishlist wishlist) {
		wishlist.setId(null);
		wishlist.setMarkTime(new Date());
		wishlistMapper.insertSelective(wishlist);
	}

	@Override
	@Transactional
	public boolean delWishlistById(Map<String, Object> reqMap) {
		return this.delUserWishlist(reqMap);
	}

	@Override
	@Transactional
	public boolean delUserWishlist(Map<String, Object> data) {
		Object condition = null;
		if ((condition = data.get("userId")) == null)
			return false;
		WishlistExample wishlistExample = new WishlistExample();
		WishlistExample.Criteria criteria = wishlistExample.createCriteria();
		/* 指定 User id*/
		criteria.andUserIdEqualTo((int) condition);
		/* 指定 Wishlist id*/
		if ((condition = data.get("wishlistId")) != null)
			criteria.andIdEqualTo((int) condition);
		if ((condition = data.get("productId")) != null)
			criteria.andProductIdEqualTo((int)condition);
		return wishlistMapper.deleteByExample(wishlistExample) != 0;
	}

	@Override
	public boolean selectIsWishlist(Wishlist wishlist) {
		if (wishlist.getUserId() == null || wishlist.getProductId() == null)
			return false;
		WishlistExample example = new WishlistExample();
		WishlistExample.Criteria criteria = example.createCriteria();
		criteria.andUserIdEqualTo(wishlist.getUserId());
		criteria.andProductIdEqualTo(wishlist.getProductId());
		long count = wishlistMapper.countByExample(example);
		return count == 0;
	}

	@Override
	public boolean delWishlistByProductId(Map<String, Object> data) {
		return this.delUserWishlist(data);
	}

//Controller

	@Autowired
	WishlistService wishlistService;

	/**
	 * 获取用户心愿单信息
	 * 
	 * @param reqMap
	 * @return
	 */
	@RequestMapping(value = "user/wishlist/{currentPage}", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject saveOption(@PathVariable("currentPage") int currentPage,@RequestBody Wishlist wishlist) {
		Map<String, Object> reqMap = new HashMap<String, Object>();
		reqMap.put("userId",wishlist.getUserId());
		reqMap.put("currentPage",currentPage);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), wishlistService.getUserWishlist(reqMap));
	}

	/**
	 * 删除用户心愿单
	 * 
	 * @param reqMap
	 * @return
	 */
	@RequestMapping(value = "user/remove/wishlist", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject delWishlist(@RequestBody Wishlist wishlist) {
		Map<String, Object> reqMap = new HashMap<>();
		reqMap.put("userId", wishlist.getUserId());
		reqMap.put("wishlistId", wishlist.getId());
		if (wishlistService.delWishlistById(reqMap))
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), true);
		else
			return this.reponseJSON(ApiResponseEnum.ERROR_PARAM.getCode(), ApiResponseEnum.ERROR_PARAM.getName(), null);
	}

		
	/**
	 * 根据产品删除收藏	
	 * 
	 */
	@RequestMapping(value = "user/delproductid/wishlist", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject delWishlistByproductid(@RequestBody Wishlist wishlist) {
		Map<String, Object> reqMap = new HashMap<>();
		reqMap.put("userId", wishlist.getUserId());
		reqMap.put("productId", wishlist.getProductId());
		if (wishlistService.delWishlistByProductId(reqMap))
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), true);
		else
			return this.reponseJSON(ApiResponseEnum.ERROR_PARAM.getCode(), ApiResponseEnum.ERROR_PARAM.getName(), null);
	}

	
	/**
	 * 新增用户心愿单信息
	 * 
	 * @param wishlist
	 * @return
	 */
	@RequestMapping(value = "user/add/wishlist", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject saveWishlist(@RequestBody Wishlist wishlist) {
		if (!wishlistService.selectIsWishlist(wishlist))
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), new AjaxResult(false, "商品已被收藏里了!"));
		wishlistService.saveUserWishlist(wishlist);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), new AjaxResult(true, "商品已被成功收藏了!"));
	}
	
	/**
	 * 查询用户心愿单信息
	 * 
	 * @param wishlist
	 * @return
	 */
	@RequestMapping(value = "user/select/wishlist", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject SelectWishlist(@RequestBody Wishlist wishlist) {
		if (!wishlistService.selectIsWishlist(wishlist))
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), true);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), false);
	}
```

###### 前端

```vue
<template>
  <div class="dev">
    <mHeader>我的收藏</mHeader>
    <ul class="wishList" v-if="wishProductlist.length > 0">
      <li v-for="wish in wishProductlist" @click="$router.push('/samsara/productdetail/'+wish.productId)">
        <div class="wishLeft">
          <img :src="$api.BASE_SERVER_URL+wish.product.defaultImg" />
        </div>
        <div class="wishRight">
          <p>{{wish.product.name}}</p>
          <div class="wishPrice">
            <h4>￥<span>{{(wish.product.shopPrice)/100}}</span></h4>
            <input @click.stop="deleteWish(wish.id)" class="deleteBtn" type="button" value="删除" />
          </div>
        </div>
      </li>
    </ul>
    <div class="NoWishProduct" v-else>
      <h2>暂无收藏商品！</h2>
    </div>
  </div>
</template>

<script>
  import mHeader from './header'

  export default {
    data () {
      return {
        wishProductlist: []
      }
    },
    created () {
      this.getWishList();
    },
    methods: {
      getWishList() {
        var userId = this.Storage.Local.get('user').id;
        this.$post(this.$api.API_URL_CUSTOMER_WISHLIST + '/1', {
          'userId': userId
        }).then (res => {
          this.wishProductlist = res.data.wishlist;
        })
      },
      deleteWish(wishId) {
        var userId = this.Storage.Local.get('user').id;
        this.$post(this.$api.API_URL_CUSTOMER_REMOVE_WISHLIST, {
          'userId': userId,
          'id': wishId
        }).then (res => {
          this.getWishList();
          this.$dialog.alert({ message: '删除成功' });
        })
      }
    },
    components: {mHeader}
  }

</script>

<style scoped>
.wishList{
  width: calc(100% - 1em);
  margin: 0 auto;
}
.wishList li{
  width: 100%; height: 8em;
  overflow: hidden;
}
.wishList li:after,.wishPrice:after{
  content: '';
  display: block;
  clear: both;
}
.wishLeft{
  width: 6em; height: 6em;
  margin: 1em 0;
  float: left;
}
.wishLeft>img{
  width: 100%; height: 100%;
  display: block;
}
.wishRight{
  width: calc(100% - 7em);
  float: right;
}
.wishRight>p{
  text-overflow: -o-ellipsis-lastline;
  overflow: hidden;
  text-overflow: ellipsis;
  text-align: left;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
}
.wishPrice>h4{
  color: red;
  line-height: 2em;
  float: left;
  margin: 0;
}
.wishPrice>h4>span{
  font-size: 1.2em;
}
.deleteBtn{
  width: 4em; height: 2em;
  float: right;
  background: no-repeat;
  border: 1px #e6e6e6 solid;
  border-radius: 1em;
}
.NoWishProduct{
  width: 100%;
  position: absolute;
  left: 0; top: 45%;
  text-align: center;
}
.NoWishProduct>h2{
  font-size: 1.4em;
}
</style>

```



#### 积分

```vue
    <mt-cell style="margin-bottom: 30px" v-if="$store.getters.user">
      <template slot="icon">
        <img
          src="../../static/1.jpg"
          style="float:left;height:5em;width:5em;border-radius: 50%"
        />
      </template>
      <template slot="title">
        <div
          v-if="$store.getters.user.userName"
          style="text-align: right;top:-10px;position:relative"
        >
          <p>
            {{
              name != "" && name != null ? name : $store.getters.user.userName
            }}
          </p>
          <p>积分：{{ integral }}</p>
        </div>
      </template>
    </mt-cell>
```

```vue
    this.$post(this.$api.API_INTEGRAL_ALL, {
      userid: this.$store.getters.user.id
    }).then(res => {
      if (res.errorCode == 0) {
        this.integral = res.data.integralAll;
      } else {
        this.$toast("网络错误！！ ");
      }
    });
```

```vue
<template>
  <div> <mHeader>积分明细</mHeader>
  <div style="text-align:left">
      <mt-cell title="积分明细"></mt-cell>
      <mt-field label="当前积分" v-model="integralall" disabled></mt-field>
    </div>
     <mt-cell>
      <div class="main" slot="title">
        <span style="margin-left: 35px;">订单号</span>
        <span style="margin-left:65px;">购买金额</span>
        <span >获得积分</span>
      </div>
    </mt-cell>
    <mt-cell v-for="(item, index) in integral" :key="index">
      <div class="main" slot="title">
        <span>{{ item.orderNum }}</span>
        <span>{{ item.productPrice/100 }}</span>
        <span style="margin-right:25px;">{{ item.integral }}</span>
      </div>
    </mt-cell>
  </div>
</template>
<script>
import mHeader from "../other/header";
export default {
  name: "integral",
  data() {
    return {
      integralall:0,
      integral:[]
    };
  },
  created(){  
   this.$post(this.$api.API_INTEGRAL_ALL, {
      userid: this.$store.getters.user.id
    }).then(res => {
      if (res.errorCode == 0) {
        this.integralall = res.data.integralAll;
        this.integral=res.data.integral;
      } else {
        this.$toast("网络错误！！ ");
      }
    });
    
  },  components: { mHeader }
};
</script>
<style scoped>
.main {
  display: flex;
  justify-content: space-between;
}
</style>

```

```java
public interface IntegralService {
	
	public Map<String,Object> getIntegral(int userid);
}
```

```java
@Service
public class IntegralServiceImpl implements IntegralService{
	@Autowired
	PaymentHistoryMapper paymentHistoryMapper;


	@Override
	public  Map<String,Object> getIntegral(int userid) {
		Map<String,Object> map =new HashMap<String, Object>();
		PaymentHistoryExample example=new  PaymentHistoryExample();
		com.sybinal.shop.model.PaymentHistoryExample.Criteria criteria = example.createCriteria();
		criteria.andUserIdEqualTo(userid);
		example.setOrderByClause("payment_time DESC");
		List<PaymentHistory> selectByExample = paymentHistoryMapper.selectByExample(example);
		int integral = 0; 
		for (PaymentHistory paymentHistory : selectByExample) {
			int jifen=(int) Math.floor((double) (paymentHistory.getProductPrice()/1000));
			integral+=jifen;
			paymentHistory.setIntegral(jifen);
		}
		map.put("integralAll", integral);
		map.put("integral", selectByExample);
		
		return map;
	}
}
```

```java
@RestController
@RequestMapping("api/v1")
public class IntegralController extends AbstractApiController {

	@Autowired
	IntegralService integralService;
	
	@RequestMapping(value = "integral/selectall",method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject getintegral(@RequestBody Map<String, Integer> rmp) {
			Map<String, Object> integral = integralService.getIntegral(rmp.get("userid"));
			return reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),integral);
	}

}
```

```java
package com.sybinal.shop.model;

import java.util.Date;

public class PaymentHistory {
	private Integer integral;

	public Integer getIntegral() {
		return integral;
	}

	public void setIntegral(Integer integral) {
		this.integral = integral;
	}

	private Integer id;

	private String orderNum;

	private Integer productPrice;

	private Integer paymentType;

	private Integer userId;

	private Date paymentTime;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getOrderNum() {
		return orderNum;
	}

	public void setOrderNum(String orderNum) {
		this.orderNum = orderNum;
	}

	public Integer getProductPrice() {
		return productPrice;
	}

	public void setProductPrice(Integer productPrice) {
		this.productPrice = productPrice;
	}

	public Integer getPaymentType() {
		return paymentType;
	}

	public void setPaymentType(Integer paymentType) {
		this.paymentType = paymentType;
	}

	public Integer getUserId() {
		return userId;
	}

	public void setUserId(Integer userId) {
		this.userId = userId;
	}

	public Date getPaymentTime() {
		return paymentTime;
	}

	public void setPaymentTime(Date paymentTime) {
		this.paymentTime = paymentTime;
	}
}
```



#### 足迹

```java
public interface FootPrintService {
	//增加
	public int insert(FootPrint record);
	//判断有误足迹
	public int equal(FootPrint record);
	//修改
	public int update(FootPrint record);
	//查询
	public List<FootPrint> getfoot(FootPrint footPrint,int type);
	//删除
	public int delete(FootPrint footPrint);
}
```

```java
@Service
public class FootPrintServiceImpl implements FootPrintService {
	@Autowired
	private FootPrintMapper footprintmapper;

	@Autowired
	private ProductMapper producrmapper;

	@Override
	public int insert(FootPrint record) {
		// TODO Auto-generated method stub
		return footprintmapper.insert(record);
	}

	@Override
	public int equal(FootPrint record) {
		FootPrintExample example = new FootPrintExample();
		FootPrintExample.Criteria createCriteria = example.createCriteria();
		createCriteria.andUserIdEqualTo(record.getUserId());
		createCriteria.andProductIdEqualTo(record.getProductId());
		List<FootPrint> selectByExample = footprintmapper.selectByExample(example);
		if (selectByExample.size() > 0) {
			return selectByExample.get(0).getId();
		} else {
			return 0;
		}
	}

	@Override
	public int update(FootPrint record) {
		return footprintmapper.updateByPrimaryKey(record);
	}

	@Override
	public List<FootPrint> getfoot(FootPrint footPrint, int type) {
		// TODO Auto-generated method stub
		SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd 00:00:00");
		String format = df.format(new Date());
		List<FootPrint> footPrints = new ArrayList<FootPrint>();
		FootPrintExample example = new FootPrintExample();
		FootPrintExample.Criteria createCriteria = example.createCriteria();
		Date parse;
		try {
			parse = df.parse(format);
			createCriteria.andUserIdEqualTo(footPrint.getUserId());
			if (type == 1) {
				createCriteria.andDateTimeGreaterThanOrEqualTo(parse);
			} else if (type == 2) {
				createCriteria.andDateTimeLessThan(parse);
			}else {
				return null;
			}
			example.setOrderByClause("date_time	 desc");
			footPrints = footprintmapper.selectByExample(example);
			for (FootPrint fo : footPrints) {
				Product product = producrmapper.selectByPrimaryKey(fo.getProductId());
				fo.setProduct(product);
			}
		} catch (ParseException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return footPrints;
	}

	@Override
	public int delete(FootPrint footPrint) {
		// TODO Auto-generated method stub
		FootPrintExample example = new FootPrintExample();
		FootPrintExample.Criteria createCriteria = example.createCriteria();
		createCriteria.andProductIdEqualTo(footPrint.getProductId());
		return footprintmapper.deleteByExample(example);
	}

}
```

```java
@RestController
@RequestMapping("api/v1")
public class FootPrintController extends AbstractApiController {
	@Autowired
	FootPrintService footPrintService;

	@RequestMapping(value = "footprint/add", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject addFootPrint(@RequestBody FootPrint footPrint) {
		footPrint.setDateTime(new Date());
		int id = footPrintService.equal(footPrint);
		if (id != 0) {
			footPrint.setId(id);
			footPrintService.update(footPrint);
		} else {
			footPrintService.insert(footPrint);
		}
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				new AjaxResult(true));
	}

	@RequestMapping(value = "footprint/select", method = RequestMethod.POST, headers = "Accept=application/json")
	@ResponseBody
	public Map<String, List<FootPrint>> getFootPrint(@RequestBody FootPrint footPrint) {
		Map<String, List<FootPrint>> map = new HashMap<>();
		List<FootPrint> gettodayfoot = footPrintService.getfoot(footPrint,1);
		List<FootPrint> gettodayfoot2 = footPrintService.getfoot(footPrint,2);
		map.put("today", gettodayfoot);
		map.put("history", gettodayfoot2);
		return map;
	}

	@RequestMapping(value = "footprint/delete", method = RequestMethod.POST, headers = "Accept=application/json")
	@ResponseBody
	public ApiResponseObject delectFootPrint(@RequestBody FootPrint footPrint) {
		int delete = footPrintService.delete(footPrint);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				new AjaxResult(true));
	}
}
```

```js
//在mobile商品详情页里添加足迹
    //添加足迹
    getfootPrint() {
      this.$post(this.$api.API_URL_FOOTPRINT_ADD, {
        userId: this.user.id,
        productId: this.$route.params.id
      });
    },
```

```vue
<!-- 我的足迹 -->
<template>
  <div>
    <mHeader>我的足迹</mHeader>
    <van-tabs v-model="active">
      <van-tab title="今天">
        <div
          class="footprintbox"
          v-for="(item, index) in todayfoot"
          :key="index"
        >
          <van-swipe-cell v-if="item.product">
            <van-card
              @click="$router.push('/samsara/productdetail/' + item.productId)"
              class="goods-card"
              :price="item.product.shopPrice / 100 + '.0'"
              :origin-price="item.product.price"
              :thumb="$api.BASE_SERVER_URL + item.product.defaultImg"
            >
              <div slot="title" class="title">{{ item.product.name }}</div>
            </van-card>
            <template #right>
              <van-button
                square
                text="删除"
                type="danger"
                @click="deletefootprint(item.productId)"
                class="delete-button"
              />
            </template>
          </van-swipe-cell>
        </div>
      </van-tab>
      <van-tab title="历史">
        <div
          class="footprintbox"
          v-for="(item, index) in historyfootp"
          :key="index"
        >
          <van-swipe-cell v-if="item.product">
            <van-card
              @click="$router.push('/samsara/productdetail/' + item.productId)"
              class="goods-card"
              :price="item.product.shopPrice / 100 + '.0'"
              :origin-price="item.product.price"
              :thumb="$api.BASE_SERVER_URL + item.product.defaultImg"
            >
              <div slot="title" class="title">{{ item.product.name }}</div>
            </van-card>
            <template #right>
              <van-button
                square
                text="删除"
                type="danger"
                @click="deletefootprint(item.productId)"
                class="delete-button"
              />
            </template>
          </van-swipe-cell>
        </div>
      </van-tab>
    </van-tabs>
  </div>
</template>

<script>
import mHeader from "../other/header";
export default {
  name: "myFootPrint",
  data() {
    return {
      active: null,
      todayfoot: [],
      historyfootp: []
    };
  },
  created() {
    this.getfootprint();
  },
  methods: {
    getfootprint() {
      this.$post(this.$api.API_URL_FOOTPRINT_SELECT, {
        userId: this.$store.getters.user.id
      }).then(res => {
        this.todayfoot = res.today;
        this.historyfootp = res.history;
      });
    },
    deletefootprint(productId) {
      this.$post(this.$api.API_URL_FOOTPRINT_DELETE, {
        productId: productId
      }).then(res => {
        this.getfootprint();
      });
    }
  },
  components: { mHeader }
};
</script>


<style>
.goods-card {
  margin: 0;
  text-align: left;
  background-color: #ffffff;
}
.title {
  font-size: 15px;
  margin-top: 5px;
}
.footprintbox {
  position: relative;
  margin-top: 10px;
}

.delete-button {
  height: 100%;
}
</style>

```

#### 关注动态

###### 后端-显示关注动态和我的动态

```Java
//Service
	public Map<String, Object> getNoteData(Head head);@Override
	public Map<String, Object> getNoteData(Head head) {
		// TODO Auto-generated method stub
		Map<String, Object> map = new LinkedHashMap<>();
		int dataType = head.getDataType();
		int pageCount = 0;

		List<Head> rows_data = new ArrayList<>();
		HeadVo headvo = new HeadVo();
		headvo.setPageSize(Constants.PAGE_NUMBER);
		headvo.setPageNum((head.getNowPage() - 1) * Constants.PAGE_NUMBER);
		List<User> users = new ArrayList<>();
		if (dataType == 1) {
			System.out.println(head);
			headvo.setMakeEmp(head.getMakeEmp());
			pageCount = headMapper.getCountByMakeEmp(head);
			rows_data = headMapper.getNoteData(headvo);
			System.err.println(rows_data);
			for (Head head1 : rows_data) {
				User user = userMapper.selectByName(head1.getUserName());
				users.add(user);
			}
		} else if (dataType == 2) {
			pageCount = headMapper.getCount();
			rows_data = headMapper.getAllNoteData(headvo);
			for (Head head1 : rows_data) {
				User user = userMapper.selectByName(head1.getUserName());
				users.add(user);
			}
		} else if (dataType == 3) {
			pageCount = 0;
			List<Head> data = new ArrayList<>();
			data = headMapper.getAllNoteData(headvo);

			FollowExample example = new FollowExample();
			com.sybinal.shop.model.FollowExample.Criteria createCriteria = example.createCriteria();
			createCriteria.andFollowEmpEqualTo(head.getMakeEmp());
			List<Follow> follow = followmapper.selectByExample(example);

			for (Head head1 : data) {
				for (Follow follow2 : follow) {
					if (head1.getId() == follow2.getNoteDataId()) {
						pageCount++;
						rows_data.add(head1);
						User user = userMapper.selectByName(head1.getUserName());
						users.add(user);
					}
				}
			}
		}
		map.put("nowPage", head.getNowPage());
		// 计算总页数
		map.put("pageCount", Page.confirmPage(pageCount, Constants.PAGE_NUMBER));
		map.put("rows_data", rows_data);
		map.put("users", users);
		return map;
	}

//ServiceImpl
@Override
	public Map<String, Object> getNoteData(Head head) {
		// TODO Auto-generated method stub
		Map<String, Object> map = new LinkedHashMap<>();
		int dataType = head.getDataType();
		int pageCount = 0;

		List<Head> rows_data = new ArrayList<>();
		HeadVo headvo = new HeadVo();
		headvo.setPageSize(Constants.PAGE_NUMBER);
		headvo.setPageNum((head.getNowPage() - 1) * Constants.PAGE_NUMBER);
		List<User> users = new ArrayList<>();
		if (dataType == 1) {
			System.out.println(head);
			headvo.setMakeEmp(head.getMakeEmp());
			pageCount = headMapper.getCountByMakeEmp(head);
			rows_data = headMapper.getNoteData(headvo);
			System.err.println(rows_data);
			for (Head head1 : rows_data) {
				User user = userMapper.selectByName(head1.getUserName());
				users.add(user);
			}
		} else if (dataType == 2) {
			pageCount = headMapper.getCount();
			rows_data = headMapper.getAllNoteData(headvo);
			for (Head head1 : rows_data) {
				User user = userMapper.selectByName(head1.getUserName());
				users.add(user);
			}
		} else if (dataType == 3) {
			pageCount = 0;
			List<Head> data = new ArrayList<>();
			data = headMapper.getAllNoteData(headvo);

			FollowExample example = new FollowExample();
			com.sybinal.shop.model.FollowExample.Criteria createCriteria = example.createCriteria();
			createCriteria.andFollowEmpEqualTo(head.getMakeEmp());
			List<Follow> follow = followmapper.selectByExample(example);

			for (Head head1 : data) {
				for (Follow follow2 : follow) {
					if (head1.getId() == follow2.getNoteDataId()) {
						pageCount++;
						rows_data.add(head1);
						User user = userMapper.selectByName(head1.getUserName());
						users.add(user);
					}
				}
			}
		}
		map.put("nowPage", head.getNowPage());
		// 计算总页数
		map.put("pageCount", Page.confirmPage(pageCount, Constants.PAGE_NUMBER));
		map.put("rows_data", rows_data);
		map.put("users", users);
		return map;
	}

//Controller
	/**
	 * 笔记瀑布流
	 * 
	 * @param head
	 * @return
	 * @throws ApiServiceException
	 */
	@RequestMapping(value = "note/getNoteData", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject getNoteData(@RequestBody Head head) throws ApiServiceException {
		Object re = null;
		if ((re = noteService.getNoteData(head)) != null) {
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), re);
		} else {
			return this.reponseJSON(ApiResponseEnum.FAIL.getCode(), ApiResponseEnum.SUCCESS.getName(), re);
		}
	}
```

###### 后端-关注动态

```java
//Service
	int insertfollow(Follow follow);

	int delfollow(Follow follow);

	List<Follow> selectNoteId(int useriid);

	Follow selectFollowBynoteid(Follow follow);

//ServiceImpl
	@Autowired
	FollowMapper followmapper;

	@Override
	public int insertfollow(Follow follow) {
		return followmapper.insertSelective(follow);
	}

	@Override
	public int delfollow(Follow follow) {
		return followmapper.deleteByPrimaryKey(follow.getId());
	}

	@Override
	public Follow selectFollowBynoteid(Follow follow) {
		// TODO Auto-generated method stub
		FollowExample example = new FollowExample();
		Criteria criteria = example.createCriteria();
		criteria.andFollowEmpEqualTo(follow.getFollowEmp());
		criteria.andNoteDataIdEqualTo(follow.getNoteDataId());
		List<Follow> selectByExample = followmapper.selectByExample(example);
		if (selectByExample.size() != 0) {
			return selectByExample.get(0);
		}
		return null;

	}

	@Override
	public List<Follow> selectNoteId(int useriid) {
		FollowExample example = new FollowExample();
		Criteria criteria = example.createCriteria();
		criteria.andFollowEmpEqualTo(useriid);
		return followmapper.selectByExample(example);
	}

//Controller
@Autowired
	FollowService followService;

	@RequestMapping(value = "follow/add", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject addFollow(@RequestBody Follow follow) {
		follow.setFollowDate(new Date());
		followService.insertfollow(follow);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				new AjaxResult(true));
	}

	@RequestMapping(value = "follow/del", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject delFollow(@RequestBody Follow follow) {
		followService.delfollow(follow);
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				new AjaxResult(true));
	}

	@RequestMapping(value = "follow/selByNodeId", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject selByNodeId(@RequestBody Follow follow) {
		Follow selectFollowBynoteid = followService.selectFollowBynoteid(follow);
		if(selectFollowBynoteid!=null) {
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
					new AjaxResult(true,"取消关注",selectFollowBynoteid));
		}
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				new AjaxResult(false,"关注",null));
	}

```



###### 前端-关注动态

```vue
<mt-button
	style="width:90px"
	type="primary"
	@click="followClick"
	size="small"
	plain>{{ follow }}
</mt-button>
js
 getfollow() {
      this.$post(this.$api.API_FOLLOW_SELECTById, {
        noteDataId: this.$route.params.id,
        followEmp: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.follow = res.data.message;
          this.followList = res.data.dataSet;
          this.followBlo = res.data.success;
        }
      });
    },
    followClick() {
      this.followBlo = !this.followBlo;
      if (this.followBlo) {
        this.$post(this.$api.API_FOLLOW_ADD, {
          noteDataId: this.$route.params.id,
          authorEmp: this.dict.makeEmp,
          followEmp: this.$store.getters.user.id
        }).then(res => {
          this.getfollow();
          this.$toast("关注成功");
        });
      } else {
        this.$post(this.$api.API_FOLLOW_DEL, {
          id: this.followList.id
        }).then(res => {
          this.getfollow();
        });
      }
    },

```

###### 前端-显示我的动态和关注动态

```vue
<template>
  <div>
    <van-search
      placeholder="请输入你想要搜索的内容"
      show-action
      v-model="value"
    >
      <div slot="action" @click="$router.push('/samsara/search/' + value)">
        搜索
      </div>
    </van-search>
    <mt-swipe :auto="4000" class="article">
      <mt-swipe-item v-for="(item,index) in articleList" :key="index">{{item.title}}</mt-swipe-item>
    </mt-swipe>

    <mt-navbar v-model="selected">
      <mt-tab-item id="1">推荐</mt-tab-item>
      <mt-tab-item id="2">我的</mt-tab-item>
      <mt-tab-item id="3">关注</mt-tab-item>
    </mt-navbar>

    <div
      class="main"
      infinite-scroll-disabled="loading"
      infinite-scroll-distance="10"
      v-infinite-scroll="more(val)"
    >
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) in img1"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="dis">
              <img src="static/1.jpg" alt="" />
              <span>{{ item.userName }}</span>
            </div>
            <div class="dis">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) in img2"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="dis">
              <img src="static/1.jpg" alt="" />
              <span>{{ item.userName }}</span>
            </div>
            <div class="dis">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Toast } from "mint-ui";
export default {
  data() {
    return {
      value: null,
      count: 1,
      pageCount: 0,
      img1: [],
      img2: [],
      selected: "1",
      val: 2,
      articleList:[]
    };
  },
  mounted() {
    this.getdict(this.val);
    this.getArticle();
  },
  methods: {
    nav(id) {
      this.$router.push("/samsara/indexdetail/" + id);
    },
    getArticle(){
       this.$get(this.$api.API_ARTICLE_SELECT+"/21/pagesize/3").then(res=>{
        this.articleList=res.data
       })
    },
    getdict(val) {
      this.$post(this.$api.API_getDict, {
        makeEmp: this.$store.getters.user.id,
        dataType: val,
        nowPage: 1
      }).then(res => {
        if (res.errorCode == 0) {
          this.pageCount = res.data.pageCount;
          this.$store.commit("setDict", res.data.rows_data);
          res.data.rows_data.forEach((val, index) => {
            if (index % 2) {
              this.img2.push(val);
            } else {
              this.img1.push(val);
            }
          });
        } else {
          this.$dialog.alert({ message: "网络错误！" });
        }
      });
    },
    more(val) {
      if (this.count <= this.pageCount) {
        this.count += 1;
        this.$post(this.$api.API_getDict, {
          makeEmp: this.$store.getters.user.id,
          dataType: val,
          nowPage: this.count
        }).then(res => {
          if (res.errorCode == 0) {
            this.$store.commit("addDict", res.data.rows_data);
            res.data.rows_data.forEach((val, index) => {
              if (index % 2) {
                this.img1.push(val);
              } else {
                this.img2.push(val);
              }
            });
          } else {
            this.$toast("网络错误！");
          }
        });
      }
    }
  },
  watch: {
    selected: function(newval, oldval) {
      switch (newval) {
        case "1":
          this.val = 2;
          this.img1 = [];
          this.img2 = [];
          this.count = 1;
          this.getdict(this.val);
          break;
        case "2":
          this.val = 1;
          this.img1 = [];
          this.img2 = [];
          this.count = 1;
          this.getdict(this.val);
          break;
        case "3":
          this.val = 3;
          this.img1 = [];
          this.count = 1;
          this.img2 = [];
          this.getdict(this.val);
          break;
      }
    }
  }
};
</script>

<style>
.main {
  display: flex;
  justify-content: space-between;
}
.col {
  display: flex;
  flex-direction: column;
  flex: 1;
}
.item {
  padding: 5px;
}
.img {
  width: 100%;
  border-radius: 10px 10px 0 0;
}
.user {
  align-items: center;
  font-size: 0.6em;
  display: flex;
  justify-content: space-between;
  margin: 5px;
}
.dis {
  display: flex;
  justify-content: center;
  align-items: center;
}
.dis > img {
  margin-right: 5px;
  width: 20px;
  height: 20px;
  border-radius: 20px;
}
.dis > span {
  margin-left: 5px;
}
.article{
  height: 3vh;
}
</style>

```



#### 订单管理

分为未支付订单、支付式订单类型，并可以对订单状态进行 调整，订单明细和各项目小计，客户一览无疑，提交订单即 可生成。

#### 个人信息显示和修改

```vue
<template>
  <div class="div">
    <mHeader>会员设置</mHeader>
    <div style="margin-top: 20px;text-align: left">
      <mt-cell title="完善基本信息" />
      <mt-field
        label="昵称"
        placeholder="请输入昵称"
        required
        type="text"
        v-model="name"
      />
      <mt-field
        label="Email"
        placeholder="请输入邮箱"
        required
        type="text"
        v-model="email"
      />
    </div>
    <mt-button
      @click="updateUser"
      block
      class="cell"
      plain
      size="large"
      square
      style="margin-top: 20px"
      type="primary"
      >修改用户信息
    </mt-button>

    <div style="margin-top: 20px;text-align: left">
      <mt-cell title="密码修改" />
      <mt-field
        label="原始密码"
        placeholder="请输入密码"
        required
        type="text"
        v-model="pass_"
      />
      <mt-field
        label="新密码"
        placeholder="请输入密码"
        required
        type="password"
        v-model="pass__"
      />
      <mt-field
        label="确认密码"
        placeholder="请输入密码"
        required
        type="password"
        v-model="pass___"
      />
    </div>
    <mt-button
      @click="updatePass"
      block
      class="cell"
      plain
      size="large"
      square
      style="margin-top: 20px"
      type="primary"
      >修改密码
    </mt-button>

    <div style="margin-top: 20px;text-align: left">
      <mt-field label="增票资质" disabled v-model="disabled">
        <mt-switch v-model="value"></mt-switch>
      </mt-field>
    </div>
    <div style="margin-top: 20px;text-align: left" v-if="value">
      <mt-field
        label="单位名称"
        placeholder="必填"
        required
        type="text"
        v-model="organization"
      ></mt-field>
      <mt-field
        label="纳税人识别号"
        placeholder="必填"
        required
        type="text"
        v-model="taxpayerId"
      ></mt-field>
      <mt-field
        label="注册地址"
        placeholder="必填"
        required
        type="text"
        v-model="address"
      ></mt-field>
      <mt-field
        label="注册电话"
        placeholder="必填"
        required
        type="tel"
        v-model="tel"
      ></mt-field>
      <mt-field
        label="开户银行"
        placeholder="必填"
        required
        type="text"
        v-model="bank"
      ></mt-field>
      <mt-field
        label="银行卡号"
        placeholder="必填"
        required
        type="number"
        v-model="banknum"
      ></mt-field>
      <mt-button
        block
        class="cell"
        plain
        @click="next"
        size="large"
        square
        style="margin-top: 20px"
        type="primary"
        >修改增票资质
      </mt-button>
    </div>
    <mt-popup class="popup" v-model="sw" position="bottom">
      <div style="text-align: left">
        <mt-cell title="增票资质填写"></mt-cell>
        <mt-field
          label="单位名称"
          placeholder="必填"
          required
          type="text"
          v-model="organization"
        ></mt-field>
        <mt-field
          label="纳税人识别号"
          placeholder="必填"
          required
          type="text"
          v-model="taxpayerId"
        ></mt-field>
        <mt-field
          label="注册地址"
          placeholder="必填"
          required
          type="text"
          v-model="address"
        ></mt-field>
        <mt-field
          label="注册电话"
          placeholder="必填"
          required
          type="tel"
          v-model="tel"
        ></mt-field>
        <mt-field
          label="开户银行"
          placeholder="必填"
          required
          type="text"
          v-model="bank"
        ></mt-field>
        <mt-field
          label="银行卡号"
          placeholder="必填"
          required
          type="number"
          v-model="banknum"
        ></mt-field>
        <mt-button
          block
          class="cell"
          plain
          @click="next"
          size="large"
          square
          style="margin-top: 20px"
          type="primary"
          >确定
        </mt-button>
      </div>
    </mt-popup>
  </div>
</template>

<script>
import mHeader from "./header";

export default {
  name: "setting",
  components: { mHeader },
  data() {
    return {
      value: false,
      disabled: null,
      consignor: null,
      sw: false,
      name: "",
      email: "",
      pass_: "",
      pass__: "",
      pass___: "",
      organization: "",
      taxpayerId: "",
      address: "",
      tel: "",
      bank: "",
      banknum: "",
      telRe: /^(13[0-9]|14[5|7]|15[0|1|2|3|4|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$/,
      taxRe: /[0-9A-HJ-NPQRTUWXY]{2}\d{6}[0-9A-HJ-NPQRTUWXY]{10}/,
      banknumRe: /^([1-9]{1})(\d{14}|\d{18})$/
    };
  },

  methods: {
    next() {
      if (!this.organization)
        this.$dialog.alert({ message: "请填写企业名称❤" });
      else if (!  this.taxRe.test(this.taxpayerId))
        this.$dialog.alert({ message: "请填写正确的纳税人识别号❤" });
      else if (this.taxpayerId.length > 18)
        this.$dialog.alert({ message: "纳税人识别号必须为18位❤" });
      else if (!this.address)
        this.$dialog.alert({ message: "请填写注册地址❤" });
      else if (!this.telRe.test(this.tel))
        this.$dialog.alert({ message: "请填写正确的手机号码❤" });
      else if (this.tel.length > 11)
        this.$dialog.alert({ message: "手机号码必须为11位❤" });
      else if (!this.bank) this.$dialog.alert({ message: "请填写开户银行❤" });
      else if (!this.banknumRe.test(this.banknum))
        this.$dialog.alert({ message: "请填写正确的银行卡号❤" });
      else if (this.banknum.length > 19)
        this.$dialog.alert({ message: "手银行卡号必须为19位❤" });
      else
        this.$post(this.$api.API_CONSIGNOR_INSERT, {
          userId: this.$store.getters.user.id,
          receivingName: this.organization,
          consignorId: this.taxpayerId,
          openingBank: this.bank,
          bankAccount: this.banknum,
          businessAddress: this.address,
          enterprisePhone: this.tel
        }).then(res => {
          if (res.errorCode == 0) {
            this.sw = false;
            this.value = true;
            this.$toast(res.data.message);
          }
        });
    },
    updateUser() {
      var vm = this;
      this.$post(this.$api.API_URL_UPDATE_CUSTOMER, {
        id: this.$store.getters.user.id,
        nickname: this.name,
        email: this.email
      }).then(res => {
        if (res.errorCode == 0) {
          this.$dialog.alert({ message: "修改成功" });
        } else {
          this.$dialog.alert({ message: "网络超时！" });
        }
      });
    },
    updatePass() {
      if (this.pass__ == this.pass___ && this.pass__.length)
        this.$post(this.$api.API_URL_CHANGE_PASSWORD, {
          id: this.$store.getters.user.id,
          oldpassword: this.pass_,
          rpassword: this.pass__
        }).then(res => {
          if (res.errorCode == 0) this.$dialog.alert({ message: "修改成功" });
          else this.$dialog.alert({ message: "当前密码不正确" });
        });
      else this.$dialog.alert({ message: "请重新核对新密码" });
    },
    selectconsignor() {
      this.$post(this.$api.API_CONSIGNOR_SELECT, {
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.value = res.data.stauts;
          if (res.data.consignor != null) {
            this.organization = res.data.consignor.receivingName;
            this.taxpayerId = res.data.consignor.consignorId;
            this.bank = res.data.consignor.openingBank;
            this.banknum = res.data.consignor.bankAccount;
            this.address = res.data.consignor.businessAddress;
            this.tel = res.data.consignor.enterprisePhone;
          }
        }
      });
    },
    updateStuats(type) {
      this.$post(this.$api.API_CONSIGNOR_UPDATE, {
        userId: this.$store.getters.user.id,
        stauts: type
      }).then(res => {
        if (res.errorCode == 0) {
          if (!res.data.success) {
            this.value = false;
            this.$toast("请先完成增票资质的填写！！");
            this.sw = true;
          }else{
             this.selectconsignor();
          }
        }
      });
    }
  },
  mounted() {
    this.selectconsignor();
    this.$post(this.$api.API_URL_CUSTOMER_BASIC, {
      id: this.$store.getters.user.id
    }).then(res => {
      this.name = res.data.nickname;
      this.email = res.data.email;
    });
    if (this.value) this.disabled = "公司";
    else this.disabled = "个人";
  },
  watch: {
    value: function(now, old) {
      if (now) {
        this.disabled = "公司";
        this.updateStuats(1);
      } else {
        this.disabled = "个人";
        this.updateStuats(0);
      }
    }
  }
};
</script>

<style lang="stylus" scoped>
.popup{
width: 100%;
}
</style>

```

####  用联系人设置和修改

```vue
<!--常用联系人-->
<template>
  <div class="div">
    <mHeader>常用联系人</mHeader>
    <van-address-list
      :list="list"
      @add="$router.push({path:'/samsara/address/0'})"
      @edit="onEdit"
      @select="select"
      v-model="chosenAddressId"
    />
  </div>
</template>

<script>
import mHeader from "./header";

export default {
  methods: {
    onEdit(item, index) {
      this.$store.commit("setAddress", item);
      this.$router.push("/samsara/address/1");
    },
    select() {
      this.$post(this.$api.API_URL_CONTECT_DEFAULT, {
        id: this.chosenAddressId,
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) this.$toast("设置成功");
        else this.$dialog.alert({ message: "网络超时" });
      });
    }
  },
  mounted() {
    this.$post(this.$api.API_URL_CONTECT, {
      userId: this.$store.getters.user.id
    }).then(res => {
      let list = [];
      if (res.errorCode == 0) {
        res.data.data.forEach(value => {
          if (value.contactFlag) this.chosenAddressId = value.id;
          let obj = Object.assign({}, value);
          obj.tel = obj.mobile;
          obj.id = value.id; //-
          obj.label = obj.name + "  " + obj.mobile; //-
          list.push(obj);
        });
        this.list = list;
      } else this.$dialog.alert({ message: "网络超时" });
    });
  },
  components: { mHeader },
  data() {
    return {
      chosenAddressId: "1",
      list: []
    };
  }
};
</script>

<style lang="stylus" scoped>
.list {
  position: relative;
  height: 50px;
  font-size: 14px;
  text-align: left;
  padding: 10px 10px 10px 40px;
  border-bottom: 1px solid #ebedf0;

  .line {
    display: flex;

    .left {
      .address {
        width: 200px;
        margin-top: 5px;
        color: #7D7E80;
        font-size: 12px;
      }
    }
  }

  .mint-checkbox-core {
    position: absolute;
    margin: 13px 0px 0px -37px;
    padding: 0.5px;
  }
}

.check {
  text-align: left;
}

.button {
  position: fixed;
  bottom: 40px;
  left: 0;
  width: 100%;
}
</style>

```

```vue
<!--地址管理 添加与修改联系人 -->
<template>
  <div class="Address">
    <mHeader>地址编辑</mHeader>
    <van-address-edit
      :address-info="info"
      :area-list="areaList"
      @delete="onDelete"
      @save="onSave"
      show-delete
    />
  </div>
</template>
  
<script> 
  import mHeader from './other/header'
  import areaList from './other/area'

  export default {
    name: 'Address',
    data() {
      return {
        areaList,
        info: {
          id: '1',
          name: '张三',
          tel: '13000000000',
          addressDetail: '浙江省杭州市西湖区文三路 138 号东方通信大厦 7 楼 501 室',
          isDefault: true
        }
      }
    },
    props: {},
    methods: {
      onSave(res) {
        if (parseInt(this.$route.params.id)) this.$post(this.$api.API_URL_CONTECT_UPDATE, {
          userId: this.$store.getters.user.id,
          id: res.id,
          name: res.name,
          mobile: res.tel,
          address: `${res.province}-${res.city}-${res.county}-${res.addressDetail}`
        }).then(res => {
          if (res.errorCode == 0) this.$router.push({path: '/samsara/concat'})
          else this.$dialog.alert({message: '网络超时'})
        })
        else this.$post(this.$api.API_URL_CONTECT_ADD, {
          userId: this.$store.getters.user.id,
          name: res.name,
          mobile: res.tel,
          address: `${res.province}-${res.city}-${res.county}-${res.addressDetail}`
        }).then(res => {
          if (res.errorCode == 0) this.$router.push({path: '/samsara/concat'})
          else this.$dialog.alert({message: '网络超时'})
        })
      },
      onDelete(res) {
        if (res.id) this.$post(this.$api.API_URL_CONTECT_REMOVE, {
          id: res.id,
          userId: this.$store.getters.user.id
        }).then(res => {
          if (res.errorCode == 0) this.$router.push({path: '/samsara/concat'})
          else this.$dialog.alert({message: '网络超时'})
        })
        else this.$router.push({path: '/samsara/concat'})
      }
    },
    components: {mHeader},
    mounted() {
      if (parseInt(this.$route.params.id)) this.info = this.$store.state.address
      else this.info = {}
    },
    beforeDestroy() {
    }
  }
</script>

<style scoped lang='stylus'>

</style>

```



#### 购物车

对购物车内的商品进行添加、修改和删 除操作，确定后可以提交订单进行结账。

```
接口
  /** 提交订单 */
  API_URL_CART_COMMIT: v1 + 'user/orders/commitOrder'
  参数
  	orderItemList[orderItem["productId:产品id","quantity:数量"]]
  	userId：用户id
  /* 移除购物车 */
  API_URL_CART_ITEMS_REMOVE: v1 + "cart/items/remove"
  	productId：产品id
  	userId：用户id
  /* 移除全部购物车 */
  API_URL_CART_ITEMS_REMOVEALL: v1 + "cart/items/removeall"
  	userId：用户id
```

```vue
<template>
  <div class="car">
    <vHeader>购物车</vHeader>
    <div class="div" v-if="cartList">
      <div class="notice">
        <img
          src="../../static/img/tongzhi.png"
          style="float:left;height:1.3em;width:1.3em;"
        />
        <span
          >当前购物车:{{ count }}件商品,合计金额:{{
            cartList.price / 100
          }}</span
        >
      </div>
      <div
        :key="index0"
        class="cell0"
        style="margin-top: 20px;text-align: left"
        v-for="(item0, index0) of cartList.productList"
      >
        <van-swipe-cell>
          <van-card
            class="card-box"
            :num="item0.quantity"
            :origin-price="item0.price / 100 + '.0'"
            :price="item0.shopPrice / 100 + '.0'"
            :title="item0.name"
            :thumb="$api.BASE_SERVER_URL + item0.defaultImg"
          />
          <template #right>
            <van-button
              square
              text="删除"
              type="danger"
              @click="onClose(item0.id)"
              class="delete-button"
            />
          </template>
        </van-swipe-cell>
      </div>
      <van-submit-bar
        class="submit"
        :price="cartList.price"
        button-text="去结算"
        @submit="payment"
      >
        <van-button type="danger" round size="small" @click="removeAll"
          >清空购物车</van-button
        >
      </van-submit-bar>
    </div>
  </div>
</template>

<script>
import vHeader from "./other/header";

export default {
  components: {
    vHeader
  },
  data() {
    return {
      checked: true,
      cartList: {},
      count: 0,
      removeIndex: "0"
    };
  },
  created() {
    if (!this.$store.getters.user) this.$router.push({ path: "/login" });
    else this.getList();
  },
  methods: {
    onSubmit() {},
    getList() {
      this.$post(this.$api.API_URL_CART, {
        userId: this.$store.getters.user.id
      }).then(res => {
        let i = 0;
        if (res.data)
          res.data.productList.forEach(value => {
            i += value.quantity;
          });
        this.cartList = res.data;
        console.log( this.cartList)
        this.count = i;
      });
    },
    onClose(id) {
      this.$dialog
        .confirm({
          message: "确定删除吗？"
        })
        .then(() => {
          this.remove(id
          );
          instance.close();
        });
    },
    removeAll() {
      this.$post(this.$api.API_URL_CART_ITEMS_REMOVEALL, {
        userId: this.$store.getters.user.id
      }).then(res => {
        this.getList();
      });
    },
    remove(id) {
      this.$post(this.$api.API_URL_CART_ITEMS_REMOVE, {
        productId: id,
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) this.$dialog.alert({ message: "删除成功！" });
        else this.$dialog.alert({ message: "删除失败！" });
        this.getList();
      });
    },
    payment() {
      var vm = this;
      var userId = vm.$store.getters.user.id;
      if (userId) {
        var orderItemList = [];
        var orderList = this.cartList.productList;
        for (var order in orderList) {
          var orderItem = {};
          orderItem["productId"] = orderList[order].id;
          orderItem["quantity"] = orderList[order].quantity;
          orderItemList.push(orderItem);
        }
      }
      this.$post(this.$api.API_URL_CART_COMMIT, {
        orderItemList: orderItemList,
        userId: userId
      }).then(res => {
        vm.$router.push({ path: "/samsara/payment" });
        vm.removeAll();
      });
    }
  }
};
</script>

<style lang="stylus">
.delete-button {
    height: 100%;
  }
.card-box{
  margin: 0;
  background-color: @white;
}

.submit{
  margin-bottom 75px
}
.notice
  text-align left;
  padding 5px 20px;
  box-sizing border-box;
  background-color #fffbe8;
  color #ed6a0c;
  font-size 14px;
  img
    margin-right 5px;
.div{
  margin-bottom 80px
}
.good
  display block;
  width 260px;
  text-align right;
</style>
```



####  结算页

本系统提供的是一步结算方式，从上到下分别为设置收货地 址，地址簿中可以选择之前使用过的收货地址，也可以直接 创建新地址，新地址会自动保存到地址簿。系统会根据送货 地区自动给出可以使用的配送方式，再根据选定的配送方式 提供可使用的支付方式

```
<template>
  <div class="Payment">
    <mHeader>支付</mHeader>
    <template v-if="cartList && cartList.orderItemList">
    <div class="notice">
       <img src="/static/img/tongzhi.png" style="float:left;height:1.3em;width:1.3em;" >
        <span>当前购物车:{{count}}件商品,合计金额:{{cartList.price/100}}</span>
    </div>
    <div
      :key="item0.id"
      class="cell0"
      style="margin-top: 20px;text-align: left"
      v-for="(item0) of cartList.orderItemList"
    >
      <div :key="index1" class="cell1" v-for="(item1,index1) of item0.productList">  
          <mt-cell :value="item1.name" title="商品名称"/>
          <mt-cell :value="(item1.shopPrice)/100" title="单价"/>
          <mt-cell :value="item0.quantity" title="数量"/>   
      </div>
    </div>

    <div v-for="(item,index) of optionList" :key="item.id" style="margin-top: 20px">
      <mt-cell
        :title="item.name"
        v-model="key[index]"
        @click.native="item.show=true;optionIndex=index"
        style="text-align: left"
      ></mt-cell>
      <mt-popup v-model="item.show" position="bottom" style="width:100%">
        <div class="c cell">
          <div class="left" @click="onchange(index,item)">确定</div>
          <div class="picker-toolbar-title b">{{item.name}}</div>
          <div class="right" @click="item.show=!item.show">取消</div>
        </div>
        <mt-picker :slots="item.columns" @change="valuesChange"></mt-picker>
      </mt-popup>
    </div>
    <mt-button  @click="pay"  block  class="cell"  large  size="large" square style="margin-top: 20px" type="primary" >立即支付</mt-button>
    </template>
    <template v-else>
      <div class="NoOrder">
        <h2>暂无未支付订单！</h2>
      </div>
    </template>
  </div>
</template>

<script>
import mHeader from "./other/header";

export default {
  name: "Payment",
  data() {
    return {
      concatList: [],
      cartList: [],
      balance: [],
      count: 0,
      optionIndex: 0,
      option: { values: [] },
      key: ["", "支付宝"],
      cx: "",
      optionList: [
        {
          name: "选择联系人",
          show: false,
          columns: [],
          picked: 0,
          value: []
        },
        {
          name: "付款方式",
          show: false,
          columns: [{ values: ["支付宝", "微信", "电子钱包付款"] }],
          picked: 0,
          value: [0, 1, 2]
        }
      ]
    };
  },
  props: {},
  methods: {
    valuesChange(picker, values) {
      this.cx = values[0];
    },
    onchange(index, item) {
      this.key.splice(index, 1, this.cx);
      item.show = !item.show;
    },
    pay() {
      if (this.balance.amount >= this.cartList.price / 100)
        this.$post(this.$api.API_URL_ORDER_PAYMENT, {
          orderNum: this.cartList.orderNum,
          id: this.optionList[0].value[this.optionList[0].picked],
          paymentType: this.optionList[1].value[this.optionList[1].picked],
          userId: this.$store.getters.user.id
        }).then(res => {
          if (res.errorCode == 0) {
            this.$dialog.alert({ message: "支付成功！您将进入历史订单" });
            this.$router.replace("history");
            if (this.optionList[1].value[this.optionList[1].picked] == 2)
              this.$post(this.$api.API_URL_CUSTOMER_Pay_Order, {
                amount: -(this.cartList.price / 100),
                userId: this.$store.getters.user.id
              });
          } else this.$dialog.alert({ message: "网络超时" });
        });
      else this.$dialog.alert({ message: "余额不足" });
    }
  },
  components: { mHeader },
  created() {
    this.$post(this.$api.API_URL_CUSTOMER_Account, {
      userId: this.$store.getters.user.id
    }).then(res => {
      this.balance = res.data;
    });
    this.$post(this.$api.API_URL_CONTECT, {
      userId: this.$store.getters.user.id
    }).then(res => {
      let data = res.data.data;
      this.concatList = data;
      data.forEach(value => {
        if (value.contactFlag) {
          this.option.values.unshift(value.name);
          this.optionList[0].value.unshift(value.id);
        } else {
          this.option.values.push(value.name);
          this.optionList[0].value.push(value.id);
          this.optionList[0].columns.push(this.option);
        }
      });
      data.forEach(value => {
        this.key.splice(0, 1, value.name);
      });
    });
    this.$post(this.$api.API_URL_CART_ORDER, {
      userId: this.$store.getters.user.id,
      type: 1
    }).then(res => {
      let i = 0;
      if (res.data)
        res.data.orderItemList.forEach(value => {
          i += value.quantity;
        });
      this.cartList = res.data;
      console.log(this.cartList)
      this.count = i;
    });
  },
  beforeDestroy() {}
};
</script>

<style scoped lang='stylus'>
.c {
  padding: 20px 20px;
  position: relative;
  box-sizing: border-box;
  font-size: 16px;

  .left {
    float: left;
  }

  .right {
    float: right;
    position: absolute;
    right: 20px;
    top: 20px;
  }

  .b {
    text-align: center;
    margin-left: -30px;
    width: 100%;
    height: 20px;
  }
}
.notice
  text-align left;
  padding 5px 20px;
  box-sizing border-box; 
  background-color #fffbe8;
  color #ed6a0c;
  font-size 14px;
  img 
    margin-right 5px;
.NoOrder
  width: 100%
  text-align center
  font-size 14px
  position absolute
  top 50%
  transform translateY(-50%)
</style>

```

生成订单二维码，手机模拟支付宝支付

####   发票抬头

实现订单发票信息管理等

```vue
 <div style="margin-top: 20px;text-align: left">
      <mt-field label="增票资质" disabled v-model="disabled">
        <mt-switch v-model="value"></mt-switch>
      </mt-field>
    </div>
    <div style="margin-top: 20px;text-align: left" v-if="value">
      <mt-field
        label="单位名称"
        placeholder="必填"
        required
        type="text"
        v-model="organization"
      ></mt-field>
      <mt-field
        label="纳税人识别号"
        placeholder="必填"
        required
        type="text"
        v-model="taxpayerId"
      ></mt-field>
      <mt-field
        label="注册地址"
        placeholder="必填"
        required
        type="text"
        v-model="address"
      ></mt-field>
      <mt-field
        label="注册电话"
        placeholder="必填"
        required
        type="tel"
        v-model="tel"
      ></mt-field>
      <mt-field
        label="开户银行"
        placeholder="必填"
        required
        type="text"
        v-model="bank"
      ></mt-field>
      <mt-field
        label="银行卡号"
        placeholder="必填"
        required
        type="number"
        v-model="banknum"
      ></mt-field>
      <mt-button
        block
        class="cell"
        plain
        @click="next"
        size="large"
        square
        style="margin-top: 20px"
        type="primary"
        >修改增票资质
      </mt-button>
    </div>
    <mt-popup class="popup" v-model="sw" position="bottom">
      <div style="text-align: left">
        <mt-cell title="增票资质填写"></mt-cell>
        <mt-field
          label="单位名称"
          placeholder="必填"
          required
          type="text"
          v-model="organization"
        ></mt-field>
        <mt-field
          label="纳税人识别号"
          placeholder="必填"
          required
          type="text"
          v-model="taxpayerId"
        ></mt-field>
        <mt-field
          label="注册地址"
          placeholder="必填"
          required
          type="text"
          v-model="address"
        ></mt-field>
        <mt-field
          label="注册电话"
          placeholder="必填"
          required
          type="tel"
          v-model="tel"
        ></mt-field>
        <mt-field
          label="开户银行"
          placeholder="必填"
          required
          type="text"
          v-model="bank"
        ></mt-field>
        <mt-field
          label="银行卡号"
          placeholder="必填"
          required
          type="number"
          v-model="banknum"
        ></mt-field>
        <mt-button
          block
          class="cell"
          plain
          @click="next"
          size="large"
          square
          style="margin-top: 20px"
          type="primary"
          >确定
        </mt-button>
      </div>
    </mt-popup>
```

   

```js
export default {
  name: "setting",
  data() {
    return {
      value: false,
      disabled: null,
      consignor: null,
      sw: false,
      organization: "",
      taxpayerId: "",
      address: "",
      tel: "",
      bank: "",
      banknum: "",
      telRe: /^(13[0-9]|14[5|7]|15[0|1|2|3|4|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$/,
      taxRe: /[0-9A-HJ-NPQRTUWXY]{2}\d{6}[0-9A-HJ-NPQRTUWXY]{10}/,
      banknumRe: /^([1-9]{1})(\d{14}|\d{18})$/
    };
  },

 methods: {
    next() {
      if (!this.organization)
        this.$dialog.alert({ message: "请填写企业名称❤" });
      else if (!this.taxRe.test(this.taxpayerId))
        this.$dialog.alert({ message: "请填写正确的纳税人识别号❤" });
      else if (this.taxpayerId.length > 18)
        this.$dialog.alert({ message: "纳税人识别号必须为18位❤" });
      else if (!this.address)
        this.$dialog.alert({ message: "请填写注册地址❤" });
      else if (!this.telRe.test(this.tel))
        this.$dialog.alert({ message: "请填写正确的手机号码❤" });
      else if (this.tel.length > 11)
        this.$dialog.alert({ message: "手机号码必须为11位❤" });
      else if (!this.bank) this.$dialog.alert({ message: "请填写开户银行❤" });
      else if (!this.banknumRe.test(this.banknum))
        this.$dialog.alert({ message: "请填写正确的银行卡号❤" });
      else if (this.banknum.length > 19)
        this.$dialog.alert({ message: "手银行卡号必须为19位❤" });
      else
        this.$post(this.$api.API_CONSIGNOR_INSERT, {
          userId: this.$store.getters.user.id,
          receivingName: this.organization,
          consignorId: this.taxpayerId,
          openingBank: this.bank,
          bankAccount: this.banknum,
          businessAddress: this.address,
          enterprisePhone: this.tel
        }).then(res => {
          if (res.errorCode == 0) {
            this.sw = false;
            this.value = true;
            this.$toast(res.data.message);
          }
        });
    },
    selectconsignor() {
      this.$post(this.$api.API_CONSIGNOR_SELECT, {
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.value = res.data.stauts;
          if (res.data.consignor != null) {
            this.organization = res.data.consignor.receivingName;
            this.taxpayerId = res.data.consignor.consignorId;
            this.bank = res.data.consignor.openingBank;
            this.banknum = res.data.consignor.bankAccount;
            this.address = res.data.consignor.businessAddress;
            this.tel = res.data.consignor.enterprisePhone;
          }
        }
      });
    },
    updateStuats(type) {
      this.$post(this.$api.API_CONSIGNOR_UPDATE, {
        userId: this.$store.getters.user.id,
        stauts: type
      }).then(res => {
        if (res.errorCode == 0) {
          if (!res.data.success) {
            this.value = false;
            this.$toast("请先完成增票资质的填写！！");
            this.sw = true;
          }else{
             this.selectconsignor();
          }
        }
      });
    }
  },
    
    
 mounted() {
    this.selectconsignor();
    this.$post(this.$api.API_URL_CUSTOMER_BASIC, {
      id: this.$store.getters.user.id
    }).then(res => {
      this.name = res.data.nickname;
      this.email = res.data.email;
    });
    if (this.value) this.disabled = "公司";
    else this.disabled = "个人";
  },
  watch: {
    value: function(now, old) {
      if (now) {
        this.disabled = "公司";
        this.updateStuats(1);
      } else {
        this.disabled = "个人";
        this.updateStuats(0);
      }
    }
  }
    
<style lang="stylus" scoped>
.popup{
width: 100%;
}
</style>    
```

  

```java
//Service
public interface ConsignorService {

	Consignor getContectByUser(int userId);

	int insertContcet(Consignor  consignor);

	int updContect(Consignor consignor);
	
	int eqContect(int userId);

}
```

```java
//ServiceImpl
@Service
public class ConsignorServiceImpl implements ConsignorService {
	@Autowired
	ConsignorMapper consignorMapper;

	@Autowired
	PagingTool pagingTool;

	@Override
	public Consignor getContectByUser(int userId) {
		// TODO Auto-generated method stub
		ConsignorExample example = new ConsignorExample();
		Criteria criteria = example.createCriteria();
		criteria.andUserIdEqualTo(userId);
		List<Consignor> list = consignorMapper.selectByExample(example);
		if(list.size()>0) {
			return list.get(0);
		}else {
			return null;
		}
	}

	@Override
	public int insertContcet(Consignor contect) {
		// TODO Auto-generated method stub
		return consignorMapper.insertSelective(contect);
	}

	@Override
	public int updContect(Consignor contect) {
		// TODO Auto-generated method stub
		return consignorMapper.updateByPrimaryKeySelective(contect);
	}

	@Override
	public int eqContect(int userId) {
		// TODO Auto-generated method stub
		ConsignorExample example = new ConsignorExample();
		Criteria criteria = example.createCriteria();
		criteria.andUserIdEqualTo(userId);
		List<Consignor> list = consignorMapper.selectByExample(example);
		if(list.size()>0) {
			return list.get(0).getId();
		}else {
			return -1;
		}
	}
}
```

```java
//Controller
@RestController
@RequestMapping("api/v1")
public class ConsignorController extends AbstractApiController {
	@Autowired
	ConsignorService consignorService;

	@RequestMapping(value = "consignor/insert", method = RequestMethod.POST, headers = "Accept=application/json")
	@ResponseBody
	public ApiResponseObject insertConsignor(@RequestBody Consignor consignor) {
		consignor.setCreateTime(new Date());
		consignor.setType(1);
		consignor.setStauts(1);
		int eqContect = consignorService.eqContect(consignor.getUserId());
		if (eqContect != -1) {
			consignor.setId(eqContect);
			consignorService.updContect(consignor);
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
					new AjaxResult(true,"修改成功"));
		} else {
			consignorService.insertContcet(consignor);
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
					new AjaxResult(true ,"设置成功"));
		}
	}

	@RequestMapping(value = "consignor/update", method = RequestMethod.POST, headers = "Accept=application/json")
	@ResponseBody
	public ApiResponseObject updateConsignor(@RequestBody Consignor consignor) {
		int eqContect = consignorService.eqContect(consignor.getUserId());
		if (eqContect != -1) {
			consignor.setId(eqContect);
			consignorService.updContect(consignor);
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
					new AjaxResult(true));
		} else {
			return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
					new AjaxResult(false));
		}
	}

	@RequestMapping(value = "consignor/select", method = RequestMethod.POST, headers = "Accept=application/json")
	@ResponseBody
	public ApiResponseObject selectConsignor(@RequestBody Consignor consignor) {
		Consignor contectByUser = consignorService.getContectByUser(consignor.getUserId());
		Map<String, Object> map = new HashMap<String, Object>();
		if (contectByUser != null) {
			map.put("judge", true);
			if (contectByUser.getStauts() == 1) {
				map.put("stauts", true);
			} else {
				map.put("stauts", false);
			}
			map.put("consignor", contectByUser);
		} else {
			map.put("judge", false);
			map.put("stauts", false);
			map.put("consignor", null);
		}
		return this.reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(), map);
	}
}
```

