<script setup>
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from './InfoBlock.vue'
import {inject, computed, ref} from "vue";
import axios from "axios";
import {BASE_URL} from "@/config/constants";

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
})

const isCreatingOrder = ref(false);
const orderId = ref(null);
const buttonDisabled = computed(() => isCreatingOrder.value ? true : props.totalPrice ? false : true);

const {cart, closeDrawer} = inject("cart");

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const {data} = await axios.post(`https://6955cda9158a6f45.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: props.totalPrice
    })

    cart.value = [];
    orderId.value = data.id;
    return data;
  } catch (err) {
    console.log(err);
  } finally {
    isCreatingOrder.value = false;
  }
}
</script>

<template>
  <div class="fixed top-0 left-0 w-full h-full bg-black z-10 opacity-70" @click="closeDrawer"></div>
  <div class="flex flex-col bg-white w-96 h-full fixed right-0 top-0 z-20 py-8 px-7">
    <DrawerHead/>

    <div v-if="!totalPrice || orderId" class="h-full flex flex-col justify-center">
      <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Корзина пустая"
        description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
        :image-url="`${BASE_URL}/package-icon.png`"
      />
      <InfoBlock
        v-if="orderId"
        title="Заказ оформлен!"
        :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
        :image-url="`${BASE_URL}/order-success-icon.png`"
      />
    </div>

    <div v-else class="h-full flex flex-col">
      <CartItemList/>

      <div class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span>Итого</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} &#8381;</b>
        </div>

        <div class="flex gap-2">
          <span>Налог 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} &#8381;</b>
        </div>

        <button
          :disabled="buttonDisabled"
          class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-300 disabled:cursor-not-allowed transition cursor-pointer"
          @click="createOrder">
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
