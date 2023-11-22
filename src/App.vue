<script setup>
  import { ref, reactive, onMounted, watch } from 'vue';
  import { db } from './data/guitars';
  import Guitar from './components/Guitar.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  const guitars = ref([]);
  const carts   = ref([]);
  const guitar  = ref([]);

  watch(carts, () => {
    saveToLocalStorage()
  }, {
    deep: true
  })

  onMounted(() => {
    guitars.value = db,
    guitar.value  = db[3]

    const cartStorage = localStorage.getItem('cart') 
    if(cartStorage){
      carts.value = JSON.parse(cartStorage)
    }
  })

  const saveToLocalStorage = () => {
    localStorage.setItem('cart', JSON.stringify(carts.value))
  }

  const cart = (guitar) => {
    const verifyDuplicate = carts.value.findIndex(product => product.id === guitar.id)

    if (verifyDuplicate >= 0) {
        carts.value[verifyDuplicate].cant++;
    } else {
        guitar.cant = 1;
        carts.value.push(guitar);
    }
    saveToLocalStorage();
  }

  const deleteCartItem = (id) => {
    const index = carts.value.findIndex(product => product.id === id)
    if (carts.value[index].cant <= 1) return
    carts.value[index].cant--;
  }

  const addCartItem = (id) => {
    const index = carts.value.findIndex(product => product.id === id)
    if (carts.value[index].cant >= 5) return
    carts.value[index].cant++;
  }

  const deleteProducts = (id) => {
    carts.value = carts.value.filter(product => product.id !== id)
  }

  const clearCart = () => {
    carts.value = [];
  }
</script>

<template>
    <Header 
        :carts="carts"
        :guitar="guitar"
        @delete-cart-item="deleteCartItem"
        @cart="cart"
        @add-cart-item="addCartItem"
        @delete-products="deleteProducts"
        @clean-cart="clearCart"
    />
        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
            <Guitar 
                v-for="guitar in guitars"
                :key="guitar.id"
                :guitar="guitar"
                @cart="cart"
            />
            </div>
        </main>
    <Footer />
</template>