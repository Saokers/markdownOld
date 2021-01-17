# Mobile充值(18~20)

后端实体时间get()方法前面加 时间格式化

#### 	@JsonFormat(pattern="yyyy-MM-dd HH:mm:ss",timezone = "GMT+8") 

### 前台

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

## 后台

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





# 动态上传&发布动态（50以内）

#### 图片上传和跳转

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

## Mobile首页瀑布流（30）

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
        this.$store.commit("addDict", res.data.rows_data);
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
            this.$store.commit("setDict", res.data.rows_data);
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

## MOBILE六宫格（20~25）

#### 后端

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

### 前端

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

