# MintUI

和vant基本相似

# Vant

## 安装vant（npm i vant -S）

## 注册vant

##### main,js

```
import vant from 'vant'

Vue.use(vant)
```



## 组件

#### 1.  vant cell 单元格

##### van-cell-group 

|  属性  |      说明      |  类型   | 默认值 |
| :----: | :------------: | :-----: | :----: |
| title  |    分组标题    | String  |        |
| border | 是否显示外边框 | boolean |  true  |

##### van-cell

|      属性      |              说明               |
| :------------: | :-----------------------------: |
|     title      |            左侧标题             |
|     value      |            右侧内容             |
|     label      |            描述信息             |
|    is-link     |        是否显示右侧箭头         |
|   clickable    |        是否开启点击反馈         |
|      size      |              大小               |
|      icon      |            左侧图标             |
|    required    |        是否显示必写星号         |
| arow-direction | 箭头方向 可选值：left，up，down |



```vue
  <van-cell-group title="分组标题">
      <van-cell title="标题"  value="内容" label="描述信息" required size="large" icon="like-o"></van-cell>
      <van-cell title="标题" clickable  arrow-direction="left"  is-link></van-cell>
    </van-cell-group>
```

#### 2.  vant field输入框

​	van-field

|      属性       |                          说明                          |
| :-------------: | :----------------------------------------------------: |
|      label      |                        左侧文本                        |
|   placeholder   |                       输入框提示                       |
|    left-icon    |                        左侧图标                        |
|   right-icon    |                        右侧图标                        |
|    required     |                       显示小星星                       |
|    maxlength    |                      最大输入字数                      |
|      type       | 类型，可选值(tel,digit，text,number,textarea,password) |
| show-word-limit |                      显示字数统计                      |
|    clearable    |                 是否显示清除输入框的×                  |
|      colon      |                 是否在label后添加冒号                  |
|    autosize     |            自动高度 需要开启type="texearea"            |
|      rules      |                        表单验证                        |
|      error      |                      输入内容为红                      |
|  error-message  |                    底部错误提示内容                    |
|    disabled     |                          禁用                          |
|    readonly     |                         只可读                         |
|      name       |                     表单验证标识符                     |
|    autofocus    |                        自定对焦                        |
|    xxx-align    |        对齐方式 可选值（left，centerm，right）         |



```vue
<van-cell-group>
    <van-field placeholder="请输入值" left-icon="like" right-icon="like-o" required maxlength="10" show-word-limit colon  			clearable label="标题" v-model="value" type="text"></van-field>
</van-cell-group>
```

#### 3. vant button 按钮



|                     属性                     |                            说明                             |
| :------------------------------------------: | :---------------------------------------------------------: |
|        size (mini,small,normal,large)        |                            大小                             |
| type （primary,info,default,warning.danger） |                            类型                             |
|                    round                     |                          圆形按钮                           |
|                    color                     | 颜色 支持linear-gradient（to right #111111 #222222 ）渐变色 |
|                   loading                    |                      是否进入等待状态                       |
|                 loading-text                 |                        等待提示文字                         |
|                 loading-type                 |                  加载图标类型 （spinner）                   |
|                 loading-size                 |                        加载涂白大小                         |
|                   hairline                   |                           细边框                            |
|                     icon                     |                          左侧图标                           |
|                icon-position                 |                    图标位置   可选 right                    |
|                   disabled                   |                            禁用                             |
|                    plain                     |                          朴素按钮                           |
|                    square                    |                          方块按钮                           |



```vue
  <van-button size="large" type="danger">确定</van-button>
   <van-button type="danger" loading   loading-type="spinner" hairline>aaaaaaaa</van-button>
```

#### 4.  vant popup 弹出层



|        属性         |                       说明                       |
| :-----------------: | :----------------------------------------------: |
|      position       |        弹出位置 （top,bottom,left,right）        |
|       v-model       |                     绑定弹出                     |
|      closeable      |                 是否显示关闭按钮                 |
|     close-icon      |                  自定义关闭图标                  |
| close-icon-position | 关闭图标位置 (top-left,bottom-left,bottom-right) |



```vue
  <van-popup position="top" close-icon="close" round transition-appear  close-icon-position="top-left" closeable v-model="show">
    <van-button type="danger" loading   loading-type="spinner" size="large"  hairline>aaaaaaaa</van-button>
  </van-popup>
```

#### 5.vant tab 标签页	



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-tabs>
    <van-tab></vam-tab>
</van-tabs>
```

#### 6.vant card 卡片



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-card><.van-card>
```

#### 7.vant  icon 图标



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-icon></van-icon>
```

#### 8.vant image 图片



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-image></van-image>
```

#### 9.vant SwipeCell 滑栋单元格



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-swipe-cellr></van-swipcell>
```

#### 10.vant Toast 轻提示



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
this.$toast('提示内容')
```

#### 11.vant  search 搜索框



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-search></van-search>
```

#### 12.vant stepper 步进器



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-stepper></van-stepper>	
```

#### 13.vant dialog 弹出框



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
this.$dialog.alert({
	message:"弹出内容！"
	title:"标题"
})
```

#### 14.vant lazyload 懒加载



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
v-lazy="数组中的img"
```

#### 15.vant swipe 轮播图



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-swipe>
	<van-swipe-item></van-swipe-item>
    <van-swipe-item></van-swipe-item>
    <van-swipe-item></van-swipe-item>
</van-swipe>
```

####  16.vant Grid  网格



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-grid>
	<van-grid-item></van-grid-item>
	<van-grid-item></van-grid-item>
	<van-grid-item></van-grid-item>
</van-grid>
```

#### 17.vant GoodsAction 商品导航



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-goods-action>
	<van-goods-action-icon></van-goods-action-icon>
    <van-goos-action-button></van-goos-action-button>
</van-goods-action>
```

#### 18.vant SubmitBar  提交订单



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-submit-ba></van-submit-ba>r
```

#### 19.vant Sku 商品规格



| 属性 | 说明 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



```vue
<van-sku></van-sku>
```

