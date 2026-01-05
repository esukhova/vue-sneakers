<script setup>
    import {computed} from "vue";
    import {BASE_URL} from "@/config/constants.js";

    const props = defineProps({
        id: Number,
        imageUrl: String,
        title: String,
        price: Number,
    })

    const emit = defineEmits(['onClickRemove'])

    const fullImageUrl = computed(()=> {
      return `${BASE_URL}${props.imageUrl}`
    })
</script>

<template>
<div class="flex items-center border border-slate-100 p-4 rounded-xl gap-4">
    <img class="w-16 h-16" :src="fullImageUrl" :alt="title">
    <div class="flex flex-1 flex-col">
        <p>{{ title }}</p>

        <div class="flex justify-between mt-2">
            <b class="flex-1">{{ price }} руб.</b>
            <img @click = "emit('onClickRemove')" class="opacity-40 hover:opacity-100 transition cursor-pointer" src="/close.svg" alt="Close">
        </div>
    </div>
</div>
</template>
