<script setup>
import { getItems, removeItem, removeAll } from '@/services/cartService';
import { reactive, onMounted } from 'vue';

const state = reactive({
  items: [],
});

const load = async () => {
  const res = await getItems();
  if (res === undefined || res.status !== 200) {
    return;
  }
  state.items = res.data;
};

const remove = async (cartId) => {
  if (confirm('삭제하시겠습니까?')) {
    const res = await removeItem(cartId);
    if (res === undefined || res.status !== 200) {
      return;
    }
    load();
  }
};

const clear = async () => {
  if(confirm('삭제하시겠습니까?')){
    const res = await removeAll();
    if (res === undefined || res.status !== 200) {
      return;
    }
    state.items=[];
  }
};

onMounted(() => {
  load();
});
</script>

<template>
  <div class="cart">
    <div class="container">
      <template v-if="state.items.length">
        <ul class="items">
          <li v-for="i in state.items" :key="i.id">
            <img :alt="`상품사진(${i.name})`" :src="`/pic/item/${i.imgPath}`" />
            <b class="name">{{ i.name }}</b>
            <span class="price">
              {{
                (i.price - (i.price * i.discountPer) / 100).toLocaleString()
              }}원
            </span>
            <span class="remove float-end" @click="remove(i.id)" title="삭제"
              >&times;</span
            >
          </li>
        </ul>
        <div>
          <button @click="clear" class="btn btn-danger mb-3">
            장바구니 비우기
          </button>
        </div>
        <div class="act">
          <router-link to="/order" class="btn btn-primary"
            >주문하기</router-link
          >
        </div>
      </template>
      <div class="text-center py-5" v-else>장바구니가 비어있습니다.</div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.cart {
  .items {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  li {
    border: 1px solid #eee;
    margin-top: 25px;
    margin-bottom: 25px;
  }
  img {
    width: 150px;
    height: 150px;
  }
  .name {
    margin-left: 25px;
  }
  .price {
    margin-left: 25px;
  }
  .remove {
    cursor: pointer;
    font-size: 30px;
    padding: 5px 15px;
  }
}
.act .btn {
  width: 300px;
  display: block;
  margin: 0 auto;
  padding: 30px 50px;
  font-size: 20px;
}
</style>
