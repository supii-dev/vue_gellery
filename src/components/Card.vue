<script setup>
import { computed } from 'vue';
import { addItem } from '@/services/cartService';
import { useAccountStore } from '@/stores/account';
import { useRouter } from 'vue-router';

const router = useRouter();

const account = useAccountStore();
const props = defineProps({
  item: {
    id: Number,
    imgPath: String,
    name: String,
    price: Number,
    discountPer: Number,
  },
});

const computedItemDiscountPrice = computed(() => {
  return (
    (
      props.item.price -
      (props.item.price * props.item.discountPer) / 100
    ).toLocaleString() + '원'
  );
});

const put = async () => {
  if (!account.state.loggedIn) {
    alert('로그인해주세요');
    return;
  }
  const res = await addItem(props.item.id);
  if (res === undefined){
    alert('서버에 문제가 있습니다')
    return;
  }else if (res.status === 500) {
    alert('이미 장바구니에 담겨져 있습니다')
  }else if(confirm('장바구니에 상품을 담았습니다. 장바구니로 이동하시겠습니까?')){
    console.log('장바구니 담기 성공!')
    router.push('/cart');
  } 
};
</script>

<template>
  <div class="card shadow-sm">
    <span
      class="img"
      :style="{ backgroundImage: `url(/pic/item/${props.item.imgPath})` }"
      :aria-label="`상품사진(${props.item.name})`"
    ></span>

    <div class="card-body">
      <p class="card-text">
        <span class="me-2">{{ props.item.name }}</span>

        <span class="discount badge bg-danger"
          >{{ props.item.discountPer }}%</span
        >
      </p>
      <div class="d-flex justify-content-between align-items-center">
        <button class="btn btn-primary btn-sm" @click="put()">
          장바구니담기
        </button>
        <small class="price text-muted"
          >{{ props.item.price.toLocaleString() }}원</small
        >
        <small class="real text-danger">{{ computedItemDiscountPrice }}</small>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.card {
  .img {
    display: inline-block;
    width: 100%;
    height: 250px;
    background-size: cover;
    background-position: center;
  }
  .card-body .price {
    text-decoration: line-through;
  }
}
</style>
