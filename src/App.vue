<script setup>
//funciones
//logica
//si dice setup es compisition api
//sin el setup es options api
//reactive es un objeto, es recomendado para arreglos
//para acceder al valor de uno de sus atributos es objeto.atributo y para cambiarlo es objeto.atributo=valornuevo
//ref es un dato simple
//para acceder al valor en ref es el dato.value, para cambiar su atrbuto seria dato.value=valornuevo
//onMounted es un componente que 
//una directiva(v-) sirve para añadir cosas e js en html como atrubutos
//los props srven para comunicar componentes
//se puede utilizar v-bind
import {ref, reactive, onMounted, watch} from 'vue'
import {db} from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
//const state=reactive({
  //  guitarras: []
//})
//console.log(state.guitarras)
const guitarras=ref([])
const carrito=ref([])
const guitarra=ref({})

watch(carrito,()=>{guardarLocalStorage()},{
    deep:true
} )

onMounted(()=>{
    guitarras.value=db;
    guitarra.value=db[3];
    //state.guitarras=db
    const carritoStorage=localStorage.getItem('carrito')
    if(carritoStorage){
        carrito.value=JSON.parse(carritoStorage)
    }
})


//console.log(guitarras.value)

const guardarLocalStorage=()=>{
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito=(guitarra)=>{
    const existeCarrito=carrito.value.findIndex(producto=>producto.id===guitarra.id)
    if(existeCarrito>=0){
            carrito.value[existeCarrito].cantidad++
            alert('se agrego articulo al carrito');
        }
    else{
        guitarra.cantidad=1;
        carrito.value.push(guitarra);
        alert('se agrego articulo al carrito');
    }

}
const decrementarCantidad=(id)=>{
    const index=carrito.value.findIndex(producto=>producto.id===id)
    if(carrito.value[index].cantidad<=1)return
    carrito.value[index].cantidad--
}
const incrementarCantidad=(id)=>{
    const index=carrito.value.findIndex(producto=>producto.id===id)
    carrito.value[index].cantidad++
}

const eliminarProducto=(id)=>{
    carrito.value=carrito.value.filter(producto=>producto.id!==id)
}

const vaciarCarrito=()=>{
    alert('El carrito ha sido vaciado')
    //carrito.value.pop()
    carrito.value=[]
}

</script>

<template>

    <Header 
        :carrito="carrito"
        :guitarra="guitarra"
        @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra
                v-for="guitarra in guitarras"
                v-bind:guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>
    <Footer />

    
</template>


