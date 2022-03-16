<script setup>

import IPInfo from "@/components/IPInfo"
import { onMounted, ref } from "vue"
import leaflet from "leaflet"
import axios from "axios"

let myMap
const queryIp = ref("")
const ipInfo = ref(null)

onMounted(() => {
  myMap = leaflet.map("mapid").setView([47.2819735, 39.7062572], 9)
  leaflet
      .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiYWxleHIwdiIsImEiOiJjbDB0aDE4Ym4wbHAwM2Rxb3N3MjAwdW1uIn0.8XuYhnenLoBoLQG98jFh1A",
          {
            attribution:
                "Map data &copy; <a href=\"https://www.openstreetmap.org/copyright\">OpenStreetMap</a> contributors, Imagery © <a href=\"https://www.mapbox.com/\">Mapbox</a>",
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
                "pk.eyJ1IjoiYWxleHIwdiIsImEiOiJjbDB0aDE4Ym4wbHAwM2Rxb3N3MjAwdW1uIn0.8XuYhnenLoBoLQG98jFh1A",
          }
      )
      .addTo(myMap)
})

const getIpInfo = async () => {
  try {
    const data = await axios.get(
        `https://geo.ipify.org/api/v1?apiKey=at_RnIrp51CVvADb6HaD6aF63IS9ISSN&ipAddress=${queryIp.value}`
    )
    const result = data.data
    ipInfo.value = {
      address: result.ip,
      state: result.location.region,
      timezone: result.location.timezone,
      isp: result.isp,
      lat: result.location.lat,
      lng: result.location.lng,
    }
    leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(myMap)
    myMap.setView([ipInfo.value.lat, ipInfo.value.lng], 13)
  } catch (err) {
    alert(err.message)
  }
}

</script>

<template>
  <div class='flex flex-col h-screen max-h-screen'>
    <div class='flex justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32'>
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
        <div class="flex">
          <input
              v-model="queryIp"
              class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none"
              placeholder="Search for any IP address or leave empty to get your ip info"
              type="text"
          />
          <i
              class="cursor-pointer
          bg-black
          text-white
          px-4
          rounded-tr-md
          rounded-br-md
          flex
          items-center
          fas fa-chevron-right"
              @click="getIpInfo"
          ></i>
        </div>
        <IPInfo v-if="ipInfo" v-bind:ipInfo="ipInfo"/>
      </div>
    </div>
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>
