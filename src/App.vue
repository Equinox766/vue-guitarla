<script setup>
  import { ref, reactive, onMounted } from 'vue';
  import { db } from './data/guitars';
  import Guitar from './components/Guitar.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  const guitars = ref([]);
  const carts   = ref([]);
  const guitar  = ref([]);

  onMounted(() => {
    guitars.value = db,
    guitar.value  = db[3]
  })

  const cart = (guitar) => {
    const verifyDuplicate = carts.value.findIndex(product => product.id === guitar.id)

    if (verifyDuplicate >= 0) {
        carts.value[verifyDuplicate].cant++;
    } else {
        guitar.cant = 1;
        carts.value.push(guitar);
    }
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
</script>

<template>
    <Header 
        :carts="carts"
        :guitar="guitar"
        @delete-cart-item="deleteCartItem"
        @cart="cart"
        @add-cart-item="addCartItem"
    />
        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
            <Guitar 
                v-for="guitar in guitars"
                :guitar="guitar"
                @cart="cart"
            />
            </div>
        </main>
    <Footer />
</template>