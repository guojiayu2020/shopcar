<template>
    <!--收藏夹-->
    <div id="mark-page">
            <table>
                <!--导航栏-->
                <thead>
                <tr>
                    <th><span>商品或服务名称</span></th>
                    <th>单价</th>
                    <th>操作</th>
                </tr>
                </thead>
                <!--商品列表-->
                <tbody>
                <tr v-for="(item,index) in Mitem" :key="index">
                    <!--商品图-->
                    <td><img :src="item.img">
                        <!--商品或服务名称-->
                        <p class="textLine text1">{{item.name}}</p>
                        <p class="lightGray textLine">商品产地：{{item.supplier}}</p>
                        <p class="lightGray textLine">发货地：{{item.address}}</p>
                    </td>
                    <!--单价-->
                    <td class="specialColor text2">{{item.price| showPrice}}</td>
                    <!--操作-->
                    <td class="deleteFav specialColor text2" @click="deleteMark(item,index)">取消收藏</td>
                </tr>
                </tbody>
            </table>
        </div>
</template>

<script>
export default {
  name: 'MarkPage',
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
    }
  },
  data () {
    return {
      markitem: [],
      items: []
    }
  },
  methods: {
    //  取消收藏
    deleteMark (item, index) {
      this.markitem = this.Mitem
      this.items = this.Sitems
      //  如果购物车不含有此商品，则在购物车加入此商品，并移出收藏夹
      if (!this.items.includes(item)) {
        this.items.push(item)
        this.markitem.splice(index, 1)
        //  购物车内商品收藏标志变为false，即未收藏
        for (let i = 0; i < this.items.length; i++) {
          if (this.items[i].id === item.id) {
            this.items[i].isActive = false
            // console.log(this.items[i])
          }
        }
        this.$emit('deleteMark', this.markitem, this.items)
      } else {
        //  如果购物车含有此商品，则直接移出收藏夹
        this.markitem.splice(index, 1)
        for (let i = 0; i < this.items.length; i++) {
          if (this.items[i].id === item.id) {
            this.items[i].isActive = false
            // console.log(this.items[i])
          }
        }
        this.$emit('deleteMark', this.markitem, this.items)
      }
    }
  },
  filters: {
    showPrice (price) {
      return '￥' + price.toFixed(2)
    }
  }
}
</script>

<style src="../assets/css/base2.css" scoped>
/*@import "../assets/css/base2.css";*/
</style>
