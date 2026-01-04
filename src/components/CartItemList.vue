<script setup>
import { inject } from "vue";
import CartItem from './CartItem.vue'

const { cart, removeFromCart } = inject("cart");

</script>

<template>
    <TransitionGroup tag="div" name="list" class="flex flex-col gap-5 flex-1">
        <CartItem
            v-for="item in cart"
            :key="item.id"
            :id="item.id"
            :title="item.title"
            :price="item.price"
            :image-url="item.imageUrl"
            @on-click-remove="removeFromCart(item)"
        />
    </TransitionGroup>
</template>

<style scoped>
.list-move,
.list-enter-active,
.list-leave-active {
    transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
    opacity: 0;
    transform: translateY(-30px);
}

.list-leave-active {
    position: absolute;
}
</style>