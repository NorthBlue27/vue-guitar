<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
import { db } from "./data/guitarras";

// const state = reactive({
//   guitarras: [],
// });

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(
  carrito,
  () => {
    guardarLocalStorage();
  },
  {
    //EN JS UN OBJETO NUNCA ES IGUAL, SALVO CON DEEP, ENTRA AL OBJETO Y REVISA TODO
    deep: true,
  }
);

// CICLOS DE VIDA (DIFERENTES INSTANCIAS)
// CUANDO EL COMPONENTES ESTÁ LISTO, SE MONTA
onMounted(() => {
  // console.log("Componente montado");
  guitarras.value = db;
  guitarra.value = guitarras.value[3];
  //   state.guitarras = db;

  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  //BUSCA SI EXISTE UNA GUITARRA EN EL PRODUCTO
  const existeCarrito = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );

  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

//Funciones para el carrito
const aumentarCantidad = (id) => {
  const index = carrito.value.findIndex((producto) => producto.id === id);
  carrito.value[index].cantidad++;
};

const reducirCantidad = (id) => {
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((producto) => producto.id !== id);
};

const vaciarCarrito = () => {
  carrito.value.length = 0;
};
</script>

<template>
  <body>
    <Header
      :carrito="carrito"
      :guitarra="guitarra"
      @aumentar-cantidad="aumentarCantidad"
      @reducir-cantidad="reducirCantidad"
      @eliminar-producto="eliminarProducto"
      @vaciar-carrito="vaciarCarrito"
      @agregar-carrito="agregarCarrito"
    />
    <main class="container-xl mt-5">
      <h2 class="text-center">Nuestra Colección</h2>
      <!-- FIN GUITARRA -->
      <div class="row mt-5">
        <Guitarra
          v-for="guitarra in guitarras" :key="guitarra.id"
          :guitarra="guitarra"
          @agregar-carrito="agregarCarrito"
        />
      </div>
      <!-- FIN GUITARRA -->
    </main>
    <Footer />
  </body>
</template>

<style scoped></style>
