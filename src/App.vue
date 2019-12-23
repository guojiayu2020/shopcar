<template>
  <div id="app">
    <h2>购物车</h2>
    <shop-car :Sitems="items"
              :Mitem="markitem"
              :StotalMoney="totalMoney"
              :ScheckedLength="checkedLength"
              :SisCheckedAll="isCheckedAll"
              @calcCount="calcCount"
              @removeHandle="removeHandle"
              @removeFav="removeFav"
              @putFav="putFav"
              @deleteChecked="deleteChecked"
              @changeMoney="changeMoney"
              @calcChecked="calcChecked"
              @Allchecked="Allchecked"
    >
    </shop-car>
  <!--页脚-->
  <div id="footer" class="clearfix">
    <span class="footStyle">
      <label class="checkbox">
              <span class="checkbox-input">
                <input type="checkbox"
                       class="checkbox-original"
                       :checked="isCheckedAll"
                       @click="checkedAll">
                 <span class="checkbox-inner"></span>
              </span>
            <span class="checkbox-label blueBtn">全选</span>
      </label>
    </span>
    <span class="deleteCheck footStyle blueBtn" @click="deleteChecked">删除选中的商品或服务</span>
    <span class="removeCheck footStyle blueBtn" @click="removeChecked">移到我的收藏</span>
    <span class="btn4 footStyle blueBtn" @click="ShowMarkPage"><div class="btn-text">收藏夹</div></span>
    <span class="foot1 footStyle">已选择<span class="redText"></span>
      <span class="redColor">{{checkedLength}}</span>件商品
      <span class="redColor">{{checkedLength}}</span>项服务
    </span>
    <span class="foot2 footStyle">总价：<span class="redColor total">{{totalMoney | showPrice}}</span></span>
    <span class="btn3"><div class="btn-text" @click="checkAllPrice">去结算</div></span>
  </div>

    <h2 v-if="markFlag">收藏夹</h2>
    <mark-page v-if="markFlag"
               :Sitems="items"
               :Mitem="markitem"
               @deleteMark="deleteMark">
    </mark-page>
  </div>
</template>

<script>
import MarkPage from './components/MarkPage'
import ShopCar from './components/ShopCar'
import pic1 from './assets/img/pic1.jpg'
import pic2 from './assets/img/pic2.jpg'
import pic3 from './assets/img/pic3.jpg'
import pic4 from './assets/img/pic4.jpg'
import pic5 from './assets/img/pic5.jpg'

export default {
  name: 'app',
  components: {
    MarkPage,
    ShopCar
  },
  data () {
    return {
      // 显示收藏夹标志
      markFlag: false,
      // 总价
      totalMoney: 0,
      // 初始化全选按钮，默认不选
      isCheckedAll: false,
      // 选择商品数量
      checkedLength: 0,
      shopitem: [],
      markitem: [],
      items: [
        {
          id: 0,
          img: pic1,
          name: 'Gucci古驰花悦绽放女士香水',
          supplier: '法国',
          address: '广东深圳',
          price: 355.00,
          count: 1,
          isActive: false,
          checked: false
        },
        {
          id: 1,
          img: pic2,
          name: 'Versace范思哲爱神之水男士香水',
          supplier: '意大利',
          address: '广东深圳',
          price: 263.00,
          count: 1,
          isActive: false,
          checked: false
        },
        {
          id: 2,
          img: pic3,
          name: '马克雅克布莫杰梦境小雏菊女士淡香水',
          supplier: '法国',
          address: '香港',
          price: 488.00,
          count: 1,
          isActive: false,
          checked: false
        },
        {
          id: 3,
          img: pic4,
          name: 'Gucci古驰绚丽栀子花女士淡香水',
          supplier: '法国',
          address: '北京',
          price: 580.00,
          count: 1,
          isActive: false,
          checked: false
        },
        {
          id: 4,
          img: pic5,
          name: 'CK凯文克莱喜欢你女士男士香水',
          supplier: '美国',
          address: '浙江杭州',
          price: 255.00,
          count: 1,
          isActive: false,
          checked: false
        }
      ]
    }
  },
  methods: {
    // 子组件传回数据
    // 计算商品数量
    calcCount (count, index) {
      this.items[index].count = count
    },
    // 删除单个商品
    removeHandle (Sitems) {
      this.items = Sitems
    },
    // 单个移入收藏夹
    removeFav (Sitems, Mitem) {
      this.items = Sitems
      this.markitem = Mitem
    },
    // 加入收藏和取消收藏
    putFav (Mitem) {
      this.markitem = Mitem
    },
    // 由子组件回传isCheckAll判断是否全选
    Allchecked (x) {
      this.isCheckedAll = x
    },
    // 计算选中商品数量
    calcChecked (length) {
      this.checkedLength = length
    },
    // 接收子组件回传的总价
    changeMoney (price) {
      this.totalMoney = price
    },
    // 父组件的方法
    // 全选
    checkedAll () {
      this.isCheckedAll = !this.isCheckedAll
      this.items.forEach((item, index) => {
        item.checked = this.isCheckedAll
      })
      this.calcTotalPrice()
      this.calcTotalChecked()
    },
    // 删除选中的商品或服务
    deleteChecked () {
      // 过滤掉需要删除的item，剩余的赋值给list1
      const list1 = this.items.filter(item => !item.checked)
      // console.log(list)
      this.items = list1
      this.calcTotalPrice()
      this.calcTotalChecked()
    },
    // 批量移入收藏夹
    removeChecked () {
      // list2保存选中的商品对象，连接到markitem数组后
      const list2 = this.items.filter(item => item.checked)
      this.markitem = this.markitem.concat(list2)
      // list3保存剩余商品对象
      const list3 = this.items.filter(item => !item.checked)
      this.items = list3
      this.calcTotalPrice()
      this.calcTotalChecked()
    },
    // 显示收藏夹
    ShowMarkPage () {
      if (this.markFlag === true) {
        this.markFlag = !this.markFlag
      } else if (this.markitem.length < 1) {
        alert('收藏夹为空')
      } else {
        this.markFlag = !this.markFlag
      }
    },
    // 计算选中商品数量
    calcTotalChecked () {
      this.checkedLength = 0
      let itemisChecked = []
      this.items.forEach((item, index) => {
        if (item.checked === true) {
          itemisChecked.push(item)
        }
      })
      this.checkedLength = itemisChecked.length
    },
    // 计算总价
    calcTotalPrice () {
      // 每次计算时总价先归零
      this.totalMoney = 0
      this.items.forEach(item => {
        if (item.checked) {
          this.totalMoney += item.price * item.count
        }
      })
    },
    // 收藏夹取消收藏
    deleteMark (Mitem, Sitems) {
      this.markitem = Mitem
      this.items = Sitems
      // this.items[index].isActive = false
      this.calcTotalPrice()
      this.calcTotalChecked()
      // console.log(index)
      // console.log(this.items[index].isActive)
    },
    // 结算按钮
    checkAllPrice () {
      alert('土豪，您一共消费了：' + this.totalMoney + '元')
      this.items = this.items.filter(item => !item.checked)
      this.calcTotalPrice()
      this.calcTotalChecked()
    }
  },
  filters: {
    // 显示价格特殊格式
    showPrice (price) {
      return '￥' + price.toFixed(2)
    }
  }
}
</script>

<style scoped>
@import "./assets/css/index.css";

</style>
