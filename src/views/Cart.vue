<template>
  <Loading v-if="$store.state.Loading === 1"/>
  <div v-else-if="$store.state.Loading === 0" style="height: 612px">
    <v-card
        height="100%"
    >
      <v-app-bar
          absolute
          color="#FF7043"
          dark
          scroll-target="#scrolling-techniques-6"
      >
        <v-icon>mdi-cart</v-icon>

        <v-spacer></v-spacer>

        <v-toolbar-title>购物车</v-toolbar-title>

        <v-spacer></v-spacer>

        <v-checkbox
            v-model="collapseOnScroll"
            label="全选"
            @click="allCheck"
            hide-details
        ></v-checkbox>
      </v-app-bar>
      <v-sheet
          id="scrolling-techniques-6"
          class="overflow-y-auto"
          max-height="612px"
      >
<!--        <v-img :src=require('@/assets/img/bg-cart.jpg') style="position:relative;z-index:9999;width: 100%;height: 70%">-->
<!--        </v-img>-->
        <v-container
            fluid
            style="margin-top:55px; height: auto;">
            <v-row >
              <v-col
                  v-for="(item,index) in $store.state.goods"
                  :key="index"
                  cols="12"
                  style="overflow-y: hidden;white-space: nowrap;"
              >
                  <v-card
                      rounded="rounded lg"
                      height="110px"
                  >
                    <v-checkbox
                        v-model="checkbox[index].selected"
                        @click="check(index)"
                        style="position: absolute;margin:40px 15px"
                    ></v-checkbox>
                      <v-img
                          :aspect-ratio="1"
                          width="90px"
                          :src="$store.state.cardItems[item.type][item.id]"
                          style="position: absolute;margin-left: 50px;margin-top: 10px"
                      ></v-img>
                    <div style="width: 60%;height: 100%;position: absolute;margin-left: 150px;margin-top: -21px">
                      <v-card-text>
                        <v-card-title>{{$store.state.cardItems[item.type][item.id].title}}</v-card-title>

                        <v-chip-group
                            v-if="item.type == 1 "
                            column
                        >
                          <v-chip
                              color="deep-purple accent-3"
                              text-color="white"
                              style="margin-left:80px"
                          >
                            Sweet
                            <v-icon >
                              mdi-cupcake
                            </v-icon>
                          </v-chip>
                        </v-chip-group>
                        <v-chip-group
                            v-else-if="item.type == 2 "
                            column
                        >
                          <v-chip
                              color="orange accent-3"
                              text-color="white"
                              style="margin-left:80px"
                          >
                            Drink
                            <v-icon >
                              mdi-beer-outline
                            </v-icon>
                          </v-chip>
                        </v-chip-group>
                        <v-chip-group
                            v-else
                            column
                        >
                          <v-chip
                              color="green"
                              text-color="white"
                              style="margin-left:80px"
                          >
                            Fruit
                            <v-icon >
                              mdi-fruit-watermelon
                            </v-icon>
                          </v-chip>
                        </v-chip-group>
                      </v-card-text>

                    </div>
                  </v-card>
              </v-col>
            </v-row>
        </v-container>
      </v-sheet>

    </v-card>
    <v-fab-transition>
      <v-btn
          absolute
          :key="activeFab.icon"
          :color="activeFab.color"
          class="white--text"
          fab
          large
          @click="remove"
          style="margin-left: 285px;margin-top: -100px"
      >
        <v-icon>{{ activeFab.icon }}</v-icon>
      </v-btn>
    </v-fab-transition>
    <v-snackbar
        elevation="18"
        top
        rounded="rounded lg"
        v-model="snackbar"
        color="blue"
        timeout="700"
        text
        style="text-align:center"
    >
      <v-icon color="blue">mdi-lightbulb-on-outline</v-icon>
      请选择要删除的商品
    </v-snackbar>
  </div>
</template>

<script>

import Loading from "@/components/Loading";
import store from "@/store";

export default {
  name: "Cart",
  components: {Loading},
  data(){
    return{
      collapseOnScroll: false,
      goods:store.state.goods,
      snackbar:false,
      checkbox:[],
      activeFab:{ color: 'error', icon: 'mdi-delete' }
    }
  },
  methods:{
    remove(){
      if(this.activeFab.icon === 'mdi-plus'){
        store.commit('setBottomValue',0)
      }
      const length = this.checkbox.length
      for(let i=length-1;i >=0; i--){
          if(this.checkbox[i].selected === true){
            const obj = {}
            obj.type = this.checkbox[i].type
            obj.id= this.checkbox[i].id
            store.commit('removeGoods',obj)
            console.log(obj)
            if (i === 0) {
              this.checkbox.shift() //删除并返回数组的第一个元素
            } else if (i === length - 1) {
              this.checkbox.pop()  //删除并返回数组的最后一个元素
            } else {
              this.checkbox.splice(i, 1) //删除下标为i的元素
            }
          }
      }
      if(length === this.checkbox.length){
        this.snackbar = true
      }
      console.log(this.checkbox)
      this.tranButton()
    },
    box(){
      for(let good of this.goods){
        const obj = {}
        obj.type = good.type
        obj.id= good.id
        obj.selected = false
        this.checkbox.push(obj)
      }
      // console.log(this.checkbox)
    },
    allCheck(){
      if(this.collapseOnScroll){
        for(let i=0;i < this.checkbox.length; i++){
          if(this.checkbox[i].selected === false){
            this.checkbox[i].selected = true
          }
        }
      }
      else{
        for(let i=0;i < this.checkbox.length; i++){
          if(this.checkbox[i].selected === true){
            this.checkbox[i].selected = false
          }
        }
      }
    },
    check(index){
      console.log(this.checkbox)
      if(this.checkbox[index].selected) {
        for (let i = 0; i < this.checkbox.length; i++) {
          if (this.checkbox[i].selected === false) {
            return;
          }
        }
        this.collapseOnScroll = true
      }
      else {
        for (let i = 0; i < this.checkbox.length; i++) {
          if (this.checkbox[i].selected === false) {
            this.collapseOnScroll = false
          }
        }
      }
    },
    tranButton(){
      if(this.checkbox.length === 0){
        this.activeFab = {color: '#03A9F4',icon:'mdi-plus'}
        this.collapseOnScroll = false
      }
    },
  },
  updated() {

  },
  beforeMount() {
    this.box()
  },
  mounted() {
    this.tranButton()
    setTimeout(function() {
      store.commit('endLoading', 0)
    }, 300)
  },
  destroyed() {
    // console.log(this.goods.length())

    store.commit('beginLoading', 0)
  },
}
</script>

<style scoped>

</style>
