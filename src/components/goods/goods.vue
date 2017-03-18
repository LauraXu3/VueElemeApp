<template>
    <div class="goods">
      <div class="menu-wrapper">
        <ul>
          <li v-for="(item,index) in goods" class="menu-item" @click="foodScroll($event)" :data-index="index">
            <span class="text"><span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}</span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" @scroll="menuChange">
        <ul>
          <li v-for="item in goods" class="food-list" >
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li class="food-item" v-for="food in item.foods">
                <div class="icon">
                  <img :src="food.icon" width="57" height="57">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
</template>
<style lang="scss" rel="stylesheet/scss">
  @import "../../common/scss/mixin";
  .goods {
    display: flex;
    position: absolute;
    top: 174px;
    bottom: 46px;
    width: 100%;
    overflow: hidden;
    .menu-wrapper {
      flex: 0 0 80px;
      width: 80px;
      background: #f3f5f7;
      overflow: auto;
      &::-webkit-scrollbar {
        display: none;
      }
      .menu-item {
        padding: 0 12px;
        display: table;
        width: 56px;
        height: 54px;
        line-height: 14px;
        &.current-menu-item {
          background-color: #fff;
          .text {
            font-weight: 700;
            @include border-none();
          }
        }
        .icon{
          display: inline-block;
          width: 16px;
          height: 16px;
          vertical-align: top;
          margin-right: 6px;
          background-size: 16px 16px;
          background-repeat: no-repeat;
          &.decrease{
            @include bg-image('decrease_3')
          }
          &.discount{
            @include bg-image('discount_3')
          }
          &.guarantee{
            @include bg-image('guarantee_3')
          }
          &.invoice{
            @include bg-image('invoice_3')
          }
          &.special{
            @include bg-image('special_3')
          }
        }
        .text {
          display: table-cell;
          width: 56px;
          vertical-align: middle;
          @include border-1px(rgba(7,17,27,0.1));
          font-size: 12px;
        }
      }
    }
    .foods-wrapper {
      flex:1;
      overflow: auto;
      &::-webkit-scrollbar {
        display: none;
      }
      .title {
        padding-left: 14px;
        height: 26px;
        line-height: 26px;
        border-left: 2px solid #d9dde1;
        font-size: 12px;
        color: rgb(147, 153, 159);
        background-color: #f3f5f7;
      }
      .food-item {
        display: flex;
        padding: 18px;
        @include border-1px(rgba(7,17,27,0.1));
        &:last-child {
        @include border-none();
        }
        .icon {
          flex: 0 0 57px;
          margin-right: 10px;
        }
        .content {
          flex: 1;
          .name {
            margin: 2px 0 8px 0;
            height: 14px;
            line-height: 14px;
            font-size: 14px;
            color: rgb(7, 17, 27);
          }
          .desc, .extra{
            line-height: 10px;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
          .desc {
            margin-bottom: 8px;
          }
          .extra {
            .count {
              margin-right: 12px;
            }
          }
          .price {
            font-weight: 700;
            line-height: 24px;
            .now {
              margin-right: 8px;
              font-size: 14px;
              color: rgb(240, 20, 20);
            }
            .old {
              text-decoration: line-through;
              font-size: 10px;
              color: rgb(147, 153, 159);
            }
          }
        }
      }
    }
  }
</style>
<script type="text-ecmascript-6">
import shopcart from 'components/shopcart/shopcart.vue'

const ERR_OK = 0;
    export default{
      props: {
        seller: {
          type: Object
        }
      },
      data() {
        return {
          goods: [],
        }
      },
      components: {
        shopcart: shopcart
      },
      methods: {
        foodScroll (event) {
          this.changeClass(event.currentTarget);
          let index = parseInt(event.currentTarget.dataset.index)+1;
          let node = document.querySelector('.food-list:nth-child('+index+')');
          let foodsWrapper = document.getElementsByClassName('foods-wrapper')[0];
          foodsWrapper.scrollTop = node.offsetTop;
        },
        menuChange () {
          let foodsWrapper = document.getElementsByClassName('foods-wrapper')[0];
          let foodList = document.getElementsByClassName('food-list');
          let menuItem = document.getElementsByClassName('menu-item');

          let len = foodList.length;
          let index;
          for(let i=0;i<len;i++){
            if(foodList[i].offsetTop + foodList[i].offsetHeight > foodsWrapper.scrollTop +50){
              index = i;
              break;
            }
          };
          if(index != this.lastTab) {
            this.changeClass(menuItem[index]);
            this.lastTab = index;
          }
        },
        changeClass (node) {
          var currentNode = document.getElementsByClassName('current-menu-item')[0];
          currentNode && currentNode.setAttribute('class',"menu-item")
          node.setAttribute("class", "menu-item current-menu-item");
        },

      },
      created() {
        this.$http.get('/api/goods').then((response) => {
          response = response.data;
          if (response.errno === ERR_OK) {
            this.goods = response.data;
          }
        });
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
        this.lastTab = 0
      }
    };
</script>
