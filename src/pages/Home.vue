<script setup>
import CardList from "@/components/CardList.vue";
import axios from "axios";
import debounce from "lodash.debounce";
import {onMounted, reactive, ref, watch, inject} from "vue";

const { cart, addToCart, removeFromCart } = inject("cart");

const items = ref([]);

const onChangeSelect = (event) => {
    filters.sortBy = event.target.value;
}

const onChangeSearchInput = debounce((event) => {
    filters.searchQuery = event.target.value;
},300);

const addToFavorite = async (item) => {
    try {

        if (!item.isFavorite) {
            const obj = {
                item_id: item.id
            }

            item.isFavorite = true;

            const {data} = await axios.post("https://6955cda9158a6f45.mokky.dev/favorites", obj);
            item.favoriteId = data.id;
        } else {
            item.isFavorite = false;
            await axios.delete(`https://6955cda9158a6f45.mokky.dev/favorites/${item.favoriteId}`);
            item.favoriteId = null;
        }
    } catch (err) {
        console.log(err);
    }

}

const onClickAddPlus = (item) => {
    if (!item.isAdded) {
        addToCart(item);
    } else {
        removeFromCart(item);
    }

    console.log(cart.value);
}

const filters = reactive({
    sortBy: "title",
    searchQuery: "",
});

const fetchFavorites = async () => {
    try {
        const {data: favorites} = await axios.get("https://6955cda9158a6f45.mokky.dev/favorites");
        items.value = items.value.map(item => {
            const favorite = favorites.find(favorite => favorite.item_id === item.id);

            if (!favorite) {
                return item;
            }

            if (favorite) {
                return {
                    ...item,
                    isFavorite: true,
                    favoriteId: favorite.id,
                }
            }
        })
    } catch (err) {
        console.log(err);
    }
}

const fetchItems = async () => {
    const params = {
        sortBy: filters.sortBy,
    };

    if (filters.searchQuery) {
        params.title = `*${filters.searchQuery}*`;
    }

    try {
        const {data} = await axios.get(
            "https://6955cda9158a6f45.mokky.dev/items", {
                params
            });
        items.value = data.map(obj => ({
            ...obj,
            isFavorite: false,
            favoriteId: null,
            isAdded: false
        }));
    } catch (err) {
        console.log(err);
    }
}

watch(filters, fetchItems);

watch(cart, () => {
    if (cart.value.length === 0) {
        items.value = items.value.map((item) => ({
            ...item,
            isAdded: false
        }))
    }
})

onMounted(async () => {
    const localCart = localStorage.getItem('cart');
    cart.value = localCart ? JSON.parse(localCart) : [];

    await fetchItems();
    await fetchFavorites();

    items.value = items.value.map((item) => ({
        ...item,
        isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
    }))
});

</script>

<template>
    <div class="flex justify-between items-center mb-20">
        <h2 class="text-3xl font-bold">Все кроссовки</h2>
        <div class="flex gap-4">
            <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none" name="" id="">
                <option value="title">По названию</option>
                <option value="price">По цене (дешевые)</option>
                <option value="-price">По цене (дорогие)</option>
            </select>
            <div class="relative">
                <img class="absolute left-3 top-3" src="/search.svg" alt="Search">
                <input type="text" placeholder="Поиск..."
                       class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
                       @input="onChangeSearchInput"/>
            </div>
        </div>
    </div>
    <CardList :items="items" @add-to-favorite="addToFavorite" @on-click-add-plus="onClickAddPlus"/>
</template>

<style scoped>

</style>
