<template>
    <div id="shop-car">
        <div class="container">
            <table>
                <!--导航栏-->
                <thead>
                <tr class="lightGray">
                    <th class="line1">
                        <label class="checkbox">
                        <span class="checkbox-input">
                        <input type="checkbox"
                          class="checkbox-original"
                          :checked="SisCheckedAll"
                          @click="checkedAll">
                        <span class="checkbox-inner"></span>
                        </span>
                        <span class="checkbox-label blueBtn" id="ch2">全选</span>
                        </label>
                    </th>
                    <th class="productName">商品或服务名称</th>
                    <th>单价</th>
                    <th>数量</th>
                    <th>小计</th>
                    <th>操作</th>
                </tr>
                </thead>
                <!--商品列表-->
                <tbody>
                <tr v-for="(item,index) in Sitems" :key="index">
                    <!--单选框-->
                    <td class="line2">
                        <label class="checkbox">
              <span class="checkbox-input">
                <input type="checkbox"
                       class="checkbox-original"
                       :checked="item.checked"
                       @click="checkedOne(item)">
                 <span class="checkbox-inner"></span>
              </span>
                            <span class="checkbox-label"></span>
                        </label>
                    </td>
                    <!--商品图-->
                    <td class="product"><img :src="item.img">
                        <!--商品或服务名称-->
                        <p class="textLine text1">{{item.name}}</p>
                        <p class="specialColor textLine">商品产地：{{item.supplier}}</p>
                        <p class="specialColor textLine">发货地：{{item.address}}</p>
                    </td>
                    <!--单价-->
                    <td class="specialColor text2">{{item.price | showPrice}}</td>
                    <!--数量-->
                    <td class="text2">
                        <div id="countBtn">
                            <button class="btn1 lightGray" @click="calcCount(index,-1)" :disabled="item.count <= 1">-</button>
                            <input class="countText" type="text"
                                   onKeyPress="if(window.event.keyCode==13) {this.blur()}"
                                   oninput = "value=value.replace(/[^\d]/g,'')" maxlength="6"
                                   @input="calcTotalPrice"
                                   v-model="item.count">
                            <button class="btn1 lightGray" @click="calcCount(index,1)" :disabled="item.count >= 999999">+</button>
                        </div>
                    </td>
                    <!--小计-->
                    <td class="text2"><span class="ShowMoney">{{item.price * item.count | showPrice}}</span></td>
                    <!--操作-->
                    <td class="text2">
                        <div class="removeBtn specialColor redBtn" @click="removeHandle(index)">删除</div>
                        <div class="removeFav specialColor greenBtn" @click="removeFav(item,index)">移到我的收藏</div>
                        <div v-if="!item.isActive"  class="addFav specialColor blueBtn" @click="putFav(item,index,1)">加入收藏</div>
                        <div v-else :class="{isActive: item.isActive}" class="deleteFav specialColor" @click="putFav(item,index,-1)">取消收藏</div>
                    </td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
export default {
  name: 'ShopCar',
  props: {
    //  父组件传递过来的原数据data
    Sitems: {
      type: Array,
      default () {
        return []
      }
    },
    //  父组件的收藏夹数据
    Mitem: {
      type: Array,
      default () {
        return []
      }
    },
    StotalMoney: {
      type: Number
    },
    ScheckedLength: {
      type: Number
    },
    SisCheckedAll: {
      type: Boolean
    }
  },
  data () {
    return {
      items: [],
      markitem: [],
      // 总价
      totalMoney: 0,
      // 初始化全选按钮，默认不选
      isCheckedAll: false,
      // 选择商品数量
      checkedLength: 0
    }
  },
  methods: {
    //  商品数量增加和减少
    calcCount (index, x) {
      this.items = this.Sitems
      if (x === 1) {
        this.items[index].count++
      }
      if (x === -1) {
        this.items[index].count--
      }
      this.calcTotalPrice()
      this.$emit('calcCount', this.items[index].count, index)
    },
    // 删除商品
    removeHandle (index) {
      this.items = this.Sitems
      if (confirm('此操作将永久删除该商品，是否继续?')) {
        this.items.splice(index, 1)
        this.calcTotalPrice()
        this.calcChecked()
        this.$emit('removeHandle', this.items)
        alert('删除成功')
      } else {
        alert('已取消删除')
      }
    },
    // 单个移入收藏夹
    removeFav (item, index) {
      this.items = this.Sitems
      this.markitem = this.Mitem
      if (!this.markitem.includes(item)) {
        this.markitem.push(item)
      }
      this.items.splice(index, 1)
      this.calcTotalPrice()
      this.calcChecked()
      this.$emit('removeFav', this.items, this.markitem)
    },
    // 加入和取消收藏
    putFav (item, index, x) {
      this.markitem = this.Mitem
      this.items = this.Sitems
      //  如果x为1则加入收藏
      if (x === 1) {
        this.markitem.push(item)
        this.items[index].isActive = true
        // console.log(this.markitem)
      }
      // 如果x为-1则移出收藏
      if (x === -1) {
        for (let i = 0; i < this.markitem.length; i++) {
          if (this.items[index].id === this.markitem[i].id) {
            // console.log(this.markitem[i])
            this.markitem.splice(i, 1)
          }
        }
        this.items[index].isActive = false
        // console.log(this.markitem)
      }
      this.$emit('putFav', this.markitem)
    },
    // 单选
    checkedOne (item) {
      item.checked = !item.checked
      let itemisChecked = []
      this.Sitems.forEach((item, index) => {
        if (item.checked === true) {
          itemisChecked.push(item)
        }
      })
      //  如果单选框数组长度与数据长度相同，则全选标志位为true
      this.isCheckedAll = this.SisCheckedAll
      if (itemisChecked.length === this.Sitems.length) {
        this.isCheckedAll = true
      } else {
        this.isCheckedAll = false
      }
      this.$emit('Allchecked', this.isCheckedAll)
      this.calcTotalPrice()
      this.calcChecked()
    },
    // 全选
    checkedAll () {
      this.isCheckedAll = !this.isCheckedAll
      this.$emit('Allchecked', this.isCheckedAll)
      this.Sitems.forEach((item, index) => {
        item.checked = this.isCheckedAll
      })
      this.calcTotalPrice()
      this.calcChecked()
    },
    // 计算选中商品数量
    calcChecked () {
      this.items = this.Sitems
      this.checkedLength = 0
      let itemisChecked = []
      this.items.forEach((item, index) => {
        if (item.checked === true) {
          itemisChecked.push(item)
        }
      })
      this.checkedLength = itemisChecked.length
      this.$emit('calcChecked', this.checkedLength)
    },
    // 计算总价
    calcTotalPrice () {
      this.items = this.Sitems
      // 每次计算时总价先归零
      this.totalMoney = 0
      this.items.forEach(item => {
        if (item.checked) {
          this.totalMoney += item.price * item.count
        }
      })
      this.$emit('changeMoney', this.totalMoney)
    }
  },
  filters: {
    showPrice (price) {
      return '￥' + price.toFixed(2)
    }
  }
}
</script>

<style src="../assets/css/base.css" scoped>
/*@import "../assets/css/base.css";*/

</style>
