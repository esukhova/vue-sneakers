<script setup>
import {computed} from 'vue';
import {BASE_URL} from "@/config/constants.js";

const props = defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickFavorite: Function,
})

const fullImageUrl = computed(()=> {
  return `${BASE_URL}${props.imageUrl}`
})
</script>

<template>
  <div
    class="flex flex-col justify-end bg-white relative border border-slate-100 rounded-3xl p-8 transition hover:-translate-y-2 hover:shadow-xl">
    <img
      v-if="onClickFavorite"
      @click='onClickFavorite' :src="!isFavorite ? `${BASE_URL}/like-1.svg` :  `${BASE_URL}/like-2.svg`" alt="Like"
      class="absolute top-0 left-8 cursor-pointer"
    />

    <img :src="fullImageUrl" alt="Sneakers"/>
    <p class="mt-2">{{ title }}</p>
    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>
      <img
        v-if="onClickAdd"
        @click='onClickAdd' :src="!isAdded ? `${BASE_URL}/plus.svg` :  `${BASE_URL}/checked.svg`" alt="Plus"
        class="cursor-pointer"
      />
    </div>
  </div>
</template>
