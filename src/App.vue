<script setup>
  import {ref, computed, watch} from 'vue'
  import Header from './components/Header.vue'
  import Button from './components/Button.vue'
  import { calcularTotalPagar } from "./helpers/index.js"

  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0);
  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;

  const formatearDinero = (valor) => {
    const formatter = new Intl.NumberFormat("en-US", {
      style: "currency",
      currency: "USD"
    });
    
    return formatter.format(valor)
  };

  watch([cantidad, meses], () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value)
  })

  const pagoMensual = computed(() => {
    return total.value / meses.value
  })

  const handleChangeDecremento = () => {
    const valor = cantidad.value - STEP;
    if( valor < MIN ) {
      alert("Cantidad no válida");
      return;
    }

    cantidad.value = valor;
  }

  const handleChangeIncremento = () => {
    const valor = cantidad.value + STEP;
    if( valor > MAX ) {
      alert("Cantidad no válida");
      return;
    }

    cantidad.value = valor;
  }


</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header />

    <div class="flex justify-between mt-10">
      <Button
        :operador="'-'"
        @fn="handleChangeDecremento"
      />
      <Button
        :operador="'+'"
        @fn="handleChangeIncremento"
      />
    </div>

    <div class="my-5">
      <input
        type="range"
        class="w-full bg-blue-400 accent-blue-600 hover:accent-blue-400"
        :min="MIN"
        :max="MAX"
        :step="STEP"
        v-model.number="cantidad"
      />

      <p class="text-center my-10 text-5xl font-black text-blue-600">
        {{formatearDinero(cantidad)}}
      </p>

      <h2 class="text-2xl font-black text-slate-950 text-center">
        Elige un <span class="text-blue-600">Plazo </span> a pagar
      </h2>
      
      <select
        class="w-full p-2 bg-white border border-blue-600 rounded-lg text-center text-xl font-bold text-blue-950 mt-5"
        :value="meses"
        v-model.number="meses"
      >
        <option value="6">6 Meses</option>
        <option value="12">12 Meses</option>
        <option value="24">24 Meses</option>
      </select>

    </div>

    <div v-if="total > 0" class="my-5 space-y-3 bg-blue-50 p-5">
      <h2 class="text-2xl font-black text-slate-950 text-center">
          Resumen <span class="text-blue-600">de pagos</span>
      </h2>
      
      <p class="text-xl text-slate-950 text-center font-black">{{ meses }} Meses</p>
      <p class="text-xl text-slate-950 text-center font-black">Total a pagar: {{ formatearDinero (total) }} </p>
      <p class="text-xl text-slate-950 text-center font-black">Mensuales: {{ formatearDinero(pagoMensual) }} </p> 
    </div>

    <p v-else class="text-center">Añade una cantidad y un plazo a pagar</p>

  </div>
</template>

