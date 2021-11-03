<template>
  <div class="w-full ...">
    <div class="p-4 bg-blue-500 shadow-sm text-center text-white">
      APLIKASI CEK ONGKIR DENGAN API RAJAONGKIR
    </div>

    <div class="grid sm:grid-cols-12 md:grid-cols-3 gap-4 p-4">
      <div class="bg-white p-3 shadow-sm rounded">
        <h4 class="text-xl pb-1">ASAL</h4>
        <hr class="border-2">
        <div class="mt-4 pb-2">
         <label class="mb-2">PROVINSI</label>
         <select class="w-full border bg-white rounded px-3 py-2 outline-none" @change="getKotaAsal" v-model="state.provinsi_asal">
          <option class="py-1" v-for="prov in provinsi" :key="prov.id" :value="prov.province_id">{{ prov.province }}</option>
        </select>
      </div>
      <div class="mt-4 pb-2">
       <label class="mb-2">KOTA/KABUPATEN</label>
       <select class="w-full border bg-white rounded px-3 py-2 outline-none" v-model="state.kota_asal">
        <option class="py-1" v-for="kota in kota_asal" :key="kota.id" :value="kota.city_id">{{ kota.city_name }}</option>
      </select>
    </div>
  </div>
  <div class="bg-white p-3 shadow-sm rounded">
    <h4 class="text-xl pb-1">TUJUAN</h4>
    <hr class="border-2">
    <div class="mt-4 pb-2">
     <label class="mb-2">PROVINSI</label>
     <select class="w-full border bg-white rounded px-3 py-2 outline-none" @change="getKotaTujuan" v-model="state.provinsi_tujuan">
      <option class="py-1" v-for="prov in provinsi" :key="prov.id" :value="prov.province_id">{{ prov.province }}</option>
    </select>
  </div>
  <div class="mt-4 pb-2">
   <label class="mb-2">KOTA/KABUPATEN</label>
   <select class="w-full border bg-white rounded px-3 py-2 outline-none" v-model="state.kota_tujuan">
    <option class="py-1" v-for="kota in kota_tujuan" :key="kota.id" :value="kota.city_id">{{ kota.city_name }}</option>
  </select>
</div>
</div>
<div class="bg-white p-3 shadow-sm rounded">
  <h4 class="text-xl pb-1">KURIR</h4>
  <hr class="border-2">
  <div class="mt-4 pb-2">
    <label class="mb-2">KURIR</label>
    <select class="w-full border bg-white rounded px-3 py-2 outline-none" v-model="state.kurir">
      <option class="py-1" value="jne">JNE</option>
      <option class="py-1" value="tiki">TIKI</option>
      <option class="py-1" value="pos">POS</option>
    </select>
  </div>
  <div class="mt-4 pb-2">
    <label class="mb-2">BERAT <i>(Gram)</i> </label>
    <input type="text" class="p-2 bg-gray-200 rounded w-full shadow hover:bg-white" v-model="state.berat" placeholder="Masukkan Berat (Gram)">
  </div>
</div>
<div>

</div>
</div>

<div class="grid place-items-center h-48">
<button class="bg-red-400 p-3 shadow-sm rounded text-white hover:bg-red-500 focus:outline-none" @click="getOngkir">CEK ONGKOS KIRIM</button>
</div>
<div class="grid sm:grid-cols-1 md:grid-cols-1 gap-4 p-4" v-if="resultOngkir">
  <div class="bg-white p-3 shadow-sm rounded">
    <h4 class="text-xl pb-1">HASIL ONGKOS KIRIM</h4>
    <hr class="border-2">
    <div class="mt-4" v-for="(value, index) in resultOngkir" :key="index">
      {{ value.service }} - {{ value.cost[0].value }} - {{ value.cost[0].etd }} Hari
      <hr>
    </div>
  </div>
</div>

</div>
</template>

<script>

import { onMounted, reactive, ref } from 'vue'
import axios from 'axios'

export default {

  setup() {

      /**
       * state province
       */
       const provinsi = ref({})

      /**
       * state ID for province and city
       */
       const state = reactive({

        provinsi_asal: "",
        kota_asal: "",

        provinsi_tujuan: "",
        kota_tujuan: "",

        berat: "",
        kurir: ""
      })

      /**
       * state kota asal
       */
       const kota_asal    = ref({}) 

      /**
       * state kota tujuan
       */
       const kota_tujuan    = ref({}) 

      /**
       * resulCost
       */
       const resultOngkir = ref(null)

       onMounted(() => {

        axios.get('https://cek-ongkir.wesdadi.com/api/provinsi').then(response => {
          provinsi.value = response.data.data
        })
        .catch(error => {
          console.log(error.response.data)
        })

      })

      /**
       * function getCitiesOrigin
       */
       function getKotaAsal() {

        axios.get(`https://cek-ongkir.wesdadi.com/api/kota/${state.provinsi_asal}`).then(response => {
          kota_asal.value = response.data.data
        })
        .catch(error => {
          console.log(error.response.data)
        })

      }

      /**
       * function getCitiesDestination
       */
       function getKotaTujuan() {

        axios.get(`https://cek-ongkir.wesdadi.com/api/kota/${state.provinsi_tujuan}`).then(response => {
          kota_tujuan.value = response.data.data
        })
        .catch(error => {
          console.log(error.response.data)
        })

      }

      /**
       * function getCostOngkir
       */
       function getOngkir() {

        axios.post('https://cek-ongkir.wesdadi.com/api/cek-ongkir/', {

          //send data ke server laravel
          asal: state.kota_asal,
          tujuan: state.kota_tujuan,
          berat: state.berat,
          kurir: state.kurir

        }).then(response => {
          resultOngkir.value = response.data.data[0].costs
        })
        .catch(error => {
          console.log(error.response.data)
        })

      }

      return {
        provinsi, state, kota_asal, kota_tujuan, getKotaAsal, getKotaTujuan, getOngkir, resultOngkir
      }

    }

  }
  </script>

  <style>
  body {
    background: #fafafa !important;
  }
  </style>