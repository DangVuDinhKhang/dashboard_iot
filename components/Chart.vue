<template>  
    <div class="flex mt-5 flex-wrap" :class="isMushroomHouse ? 'justify-center' : ''">
      <div 
        class="co2-cycle w-1 lg:w-1/4 lg:mx-10 mx-14 mb-8 md:mb-0 flex justify-center relative shadow-2xl hover:opacity-80 cursor-pointer" 
        :class="!isMushroomHouse ? 'flex-1': ''"
        @click="!isMushroomHouse ? updateManualValue('co2Concentration') : updateManualValue('light')"
      >
      
        <div v-if="!isMushroomHouse" class="absolute top-[41%] text-5xl font-bold">{{ co2Concentration[co2Concentration.length - 1] }}</div>
        <div v-if="isMushroomHouse" class="absolute top-[41%] text-5xl font-bold">{{ light[light.length - 1] }}</div>
        <div class="absolute top-[75%] text-xl font-bold">PPM</div>
        <Doughnut v-if="!isMushroomHouse" :data="co2ConcentrationHalfCycleChartData" :options="co2ConcentrationHalfCycleChartOptions" />
        <Doughnut v-if="isMushroomHouse" :data="lightHalfCycleChartData" :options="lightHalfCycleChartOptions" />
        <div class="absolute bottom-[0%] left-[25%] text-sm">0</div>
        <div v-if="!isMushroomHouse" class="absolute bottom-[0%] left-[67%] text-sm">100</div>
        <div v-if="isMushroomHouse" class="absolute bottom-[0%] left-[67%] text-sm">2000</div>
        <img v-if="!isMushroomHouse" src="../public/assets/icon/co2.png" alt="co2 icon" class="absolute co2-img top-[-10%] left-[-10%] text-4xl">
        <img v-if="isMushroomHouse" src="../public/assets/icon/Light.png" alt="co2 icon" class="absolute co2-img top-[-10%] left-[-10%] text-4xl">
      </div>
      <div 
        class="humidity-cycle w-1 lg:w-1/4 lg:mx-10 mx-14 mb-8 md:mb-0 flex justify-center relative shadow-2xl hover:opacity-80 cursor-pointer"
        :class="!isMushroomHouse ? 'flex-1' : ''"
        @click="!isMushroomHouse ? updateManualValue('roomHumidity') : updateManualValue('humidity')" 
      >
        <div v-if="!isMushroomHouse" class="absolute top-[43%] text-5xl font-bold">{{ roomHumidity[roomHumidity.length - 1] }}</div>
        <div v-if="isMushroomHouse" class="absolute top-[43%] text-5xl font-bold">{{ humidity[humidity.length - 1] }}</div>
        <div class="absolute top-[75%] text-xl font-bold">%</div>
        <Doughnut :data="placeHolderChartData" :options="placeHolderChartOptions" class="absolute z-20 mt-4"/>
        <Doughnut :data="edgeChartData" :options="edgeChartOptions" class="absolute z-10 mt-4"/>
        <Doughnut v-if="!isMushroomHouse" :data="roomHumidityHalfCycleChartData" :options="roomHumidityHalfCycleChartOptions" />
        <Doughnut v-if="isMushroomHouse" :data="humidityHalfCycleChartData" :options="humidityHalfCycleChartOptions" />
        <div class="absolute bottom-[0%] left-[25%] text-sm">0</div>
        <div class="absolute bottom-[0%] left-[67%] text-sm">100</div>
        <img src="../public/assets/icon/PhunSuong_OFF.png" alt="humidity icon" class="absolute humidity-img top-[-10%] left-[-10%] text-4xl">
      </div>
      <div 
        class="temperature-cycle w-1 lg:w-1/4 mb-8 md:mb-0 lg:mx-10 mx-14 flex justify-center relative shadow-2xl pb-1 hover:opacity-80 cursor-pointer" 
        :class="!isMushroomHouse ? 'flex-1' : ''"
        @click="!isMushroomHouse ? updateManualValue('outsideTemperature') : updateManualValue('temperature')"
      >
        <div v-if="!isMushroomHouse" class="absolute top-[41%] text-5xl font-bold">{{ outsideTemperature[outsideTemperature.length - 1] }}</div>
        <div v-if="isMushroomHouse" class="absolute top-[41%] text-5xl font-bold">{{ temperature[temperature.length - 1] }}</div>
        <div class="absolute top-[75%] text-xl font-bold">&ordm;C</div>
        <Doughnut v-if="!isMushroomHouse" :data="outsideTemperatureHalfCycleChartData" :options="outsideTemperatureHalfCycleChartOptions" />
        <Doughnut v-if="isMushroomHouse" :data="temperatureHalfCycleChartData" :options="temperatureHalfCycleChartOptions" />
        <img v-if="!isMushroomHouse" src="../public/assets/icon/temperature.png" alt="temperature icon" class="absolute outside-temperature-img top-[-20%] left-[-20%] text-4xl">
        <img v-if="isMushroomHouse" src="../public/assets/icon/temperature.png" alt="temperature icon" class="absolute outside-temperature-img top-[-20%] left-[-15%] text-4xl">
      </div>
      <div v-if="!isMushroomHouse" class="temperature-cycle room w-1 lg:w-1/4 mb-8 md:mb-0 lg:mx-10 mx-14 flex-1 flex justify-center relative shadow-2xl pb-1 hover:opacity-80 cursor-pointer" @click="$emit('openSetting', 'roomTemperature')">
        <div class="absolute top-[41%] text-5xl font-bold">{{ roomTemperature[roomTemperature.length - 1] }}</div>
        <div class="absolute top-[75%] text-xl font-bold">&ordm;C</div>
        <Doughnut :data="roomTemperatureHalfCycleChartData" :options="roomTemperatureHalfCycleChartOptions" />
        <img src="../public/assets/icon/temperature.png" alt="temperature icon" class="absolute room-temperature-img top-[-20%] left-[-20%] text-4xl">
      </div>      
    </div>
    <div class="flex flex-col sm:flex-row mt-5">
      <Line 
        :data="temperatureChartData" :options="temperatureChartOptions" 
        class="outside-temperature max-w-[95%] lg:max-w-[49%] ml-3 order-last sm:order-first"
      />
      <div v-if="!isMushroomHouse" class="flex order-first sm:order-last content-center">
        <div  
          class="w-[40%] h-[70%] lg:my-20 mx-auto lg:mx-20 shadow-lg shadow hover:shadow-2xl px-10 cursor-pointer rounded-3xl bg-[#fff7d6]" 
          @click="$emit('updateValue', 'fan', fan)"
        >
          <div class="text-md lg:text-4xl mt-7 text-center mb-2 text-black">Fan</div>
          <img class="fan-img" :src="fan_img" alt="fan" > 
          <div class="text-md lg:text-4xl text-center mt-2 lg:mt-5 text-black">Status: {{ fan ? 'ON' : 'OFF' }}</div>
        </div>
        <div 
          class="w-[40%] h-[70%] lg:my-20 mx-auto shadow-lg shadow hover:shadow-2xl px-10 cursor-pointer rounded-3xl bg-[#b1aab6]" 
          @click="$emit('updateValue', 'mist', mist)"
        >
          <div class="text-md lg:text-4xl mt-7 text-center mb-2 text-black">Mist</div>
          <img class="mist-img" :src="mist_img" alt="mist" >
          <div class="text-md lg:text-4xl text-center mt-2 lg:mt-5 text-black">Status: {{ mist ? 'ON' : 'OFF' }}</div> 
        </div>
      </div>
      <div v-if="isMushroomHouse" class="flex order-first sm:order-last content-center">
        <div  
          class="w-[40%] h-[70%] lg:my-20 mx-auto lg:mx-20 shadow-lg shadow hover:shadow-2xl px-10 cursor-pointer rounded-3xl bg-[#fff7d6]" 
          @click="$emit('updateValue', 'heater', heater)"
        >
          <div class="text-md lg:text-4xl mt-7 text-center mb-2 text-black">Heater</div>
          <img class="heater-img" :src="heater_img" alt="heater" > 
          <div class="text-md lg:text-4xl text-center mt-2 lg:mt-5 text-black">Status: {{ heater ? 'ON' : 'OFF' }}</div>
        </div>
        <div 
          class="w-[40%] h-[70%] lg:my-20 mx-auto shadow-lg shadow hover:shadow-2xl px-10 cursor-pointer rounded-3xl bg-[#b1aab6]" 
          @click="$emit('updateValue', 'mistMushroom', mistMushroom)"
        >
          <div class="text-md lg:text-4xl mt-7 text-center mb-2 text-black">Mist</div>
          <img class="mist-mushroom-img" :src="mistMushroom_img" alt="mist" >
          <div class="text-md lg:text-4xl text-center mt-2 lg:mt-5 text-black">Status: {{ mistMushroom ? 'ON' : 'OFF' }}</div> 
        </div>
      </div>
    </div>
    
    <div class="flex flex-col sm:flex-row mt-5" v-if="!isMushroomHouse">
      <Line :data="co2ConcentrationChartData" :options="co2ConcentrationChartOptions" class="co2 max-w-[49%] max-w-[95%] lg:max-w-[49%] ml-3"/>
      <Line :data="roomHumidityChartData" :options="roomHumidityChartOptions" class="room-humidity max-w-[49%] max-w-[95%] lg:max-w-[49%] ml-3"/>
    </div>  
    <div class="flex flex-col sm:flex-row mt-5" v-if="isMushroomHouse">
      <Line :data="lightChartData" :options="lightChartOptions" class="light max-w-[49%] max-w-[95%] lg:max-w-[49%] ml-3"/>
      <Line :data="humidityChartData" :options="humidityChartOptions" class="humidity max-w-[49%] max-w-[95%] lg:max-w-[49%] ml-3"/>
    </div> 
</template>

<style>
.mushroom {
  max-width: 75%;
  
}

.co2, .outside-temperature, .room-temperature, .room-humidity, .humidity, .light {
  /* max-width: 49%; */
  height: 500px !important;
  max-height: 500px;
}

.co2-cycle {
  height: 200px !important;
  max-height: 200px;
  background-color: rgb(215, 229, 63);
  border-radius: 20px;
}

.temperature-cycle {
  height: 200px !important;
  max-height: 200px;
  background-color: rgb(230, 189, 181);
  border-radius: 20px;
}

.humidity-cycle {
  height: 200px !important;
  max-height: 200px;
  background-color: rgb(218, 212, 201);
  border-radius: 20px;
}

.room {
  background-color: rgb(181, 237, 211);
}


.co2-img, .humidity-img {
  width: 80px;
  height: 80px;
  z-index: 1;
}

.outside-temperature-img, .room-temperature-img {
  width: 120px;
  z-index: 1;
}

.fan-img, .mist-img, .heater-img, .mist-mushroom-img {
  width: 200px !important;
}

.mist-img, .mist-mushroom-img {
  border: 4px solid #323030;
  border-radius: 20px;
  padding: 2px
}

@media only screen and (max-width: 700px) {
  .co2, .outside-temperature, .room-temperature, .room-humidity {
    height: 300px !important;
    max-height: 300px;
  }

  .outside-temperature {
    margin-top: 20px !important;
  }

  .co2-cycle, .humidity-cycle, .temperature-cycle {
    width: 270px !important;
    max-width: 270px !important;
  }
}



</style>
  
<script setup>
import { Line, Doughnut } from 'vue-chartjs';
import { Chart as ChartJS, registerables } from 'chart.js/auto';
import ChartDataLabels from 'chartjs-plugin-datalabels';

const props = defineProps({
  co2Concentration: Number,
  outsideTemperature: Number,
  roomTemperature: Number,
  roomHumidity: Number,
  time: String,
  fan: Number,
  mist: Number,
  temperature: Number,
  humidity: Number,
  light: Number,
  heater: Number,
  mistMushroom: Number,
  isMushroomHouse: Boolean
});

const co2Concentration = ref([]);
const outsideTemperature = ref([]);
const roomTemperature = ref([]);
const roomHumidity = ref([]);
const time = ref([]);
const fan = ref();
const mist = ref();
const fan_img = ref('');
const mist_img = ref('');

const edge = ref([100]);
const placeHolder = ref([100]);
const fontSize = ref(12);

const temperature = ref([]);
const humidity = ref([]);
const light = ref([]);
const heater = ref();
const mistMushroom = ref();
const heater_img = ref('');
const mistMushroom_img = ref('');

const isMushroomHouse = ref(false);

ChartJS.register(...registerables, ChartDataLabels);

const co2ConcentrationChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});
const co2ConcentrationChartOptions = ref({
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      max: 5000
    }
  },
  plugins: {
    legend: {
      position: 'top'
    } 
  },
});

const temperatureChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});
const temperatureChartOptions = ref({
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      max: 100
    }
  },
  plugins: {
    legend: {
      position: 'top',
    } 
  }
});

const roomHumidityChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});
const roomHumidityChartOptions = ref({
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      max: 100
    }
  },
  plugins: {
    legend: {
      position: 'top'
    } 
  },
});

const humidityChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});
const humidityChartOptions = ref({
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      max: 100
    }
  },
  plugins: {
    legend: {
      position: 'top'
    } 
  },
});

const lightChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});
const lightChartOptions = ref({
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      max: 2000
    }
  },
  plugins: {
    legend: {
      position: 'top'
    } 
  },
});

const co2ConcentrationHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const co2ConcentrationHalfCycleChartOptions = ref({
  responsive: true,
});

const roomHumidityHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const roomHumidityHalfCycleChartOptions = ref({
  responsive: true,
});
const outsideTemperatureHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const outsideTemperatureHalfCycleChartOptions = ref({
  responsive: true,
});
const roomTemperatureHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const roomTemperatureHalfCycleChartOptions = ref({
  responsive: true,
});

const temperatureHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const temperatureHalfCycleChartOptions = ref({
  responsive: true,
});

const humidityHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const humidityHalfCycleChartOptions = ref({
  responsive: true,
});

const lightHalfCycleChartData = ref({
  labels: [],
  datasets: [{ data: [] }]
});

const lightHalfCycleChartOptions = ref({
  responsive: true,
});

const edgeChartData = ref()
const edgeChartOptions = ref();
const placeHolderChartData = ref()
const placeHolderChartOptions = ref();


const onValueChange = () => {

  deleteOldValue(co2Concentration);
  if (props.co2Concentration !== undefined) {
    co2Concentration.value.push(props.co2Concentration);
  }

  deleteOldValue(outsideTemperature);
  if (props.outsideTemperature !== undefined) {
    outsideTemperature.value.push(props.outsideTemperature);
  }

  deleteOldValue(roomTemperature);
  if (props.roomTemperature !== undefined) {
    roomTemperature.value.push(props.roomTemperature);
  }

  deleteOldValue(roomHumidity);
  if (props.roomHumidity !== undefined) {
    roomHumidity.value.push(props.roomHumidity);
  }

  deleteOldValue(time);
  if (time.value[time.value.length - 1] !== props.time) {
    time.value.push(props.time);
  }

}

const onMushroomValueChange = () => {

  deleteOldValue(temperature);
  if (props.temperature !== undefined) {
    temperature.value.push(props.temperature);
  }

  deleteOldValue(light);
  if (props.light !== undefined) {
    light.value.push(props.light);
  }

  deleteOldValue(humidity);
  if (props.humidity !== undefined) {
    humidity.value.push(props.humidity);
  }

  deleteOldValue(time);
  if (time.value[time.value.length - 1] !== props.time) {
    time.value.push(props.time);
  }
}

const deleteOldValue = (type) => {
  if (type.value.length > 19) {
    type.value.splice(0, 1)
  }
}

const updateChart = (type, typeChart, title, color) => {
  typeChart.value = {
    labels: time.value,
    datasets: [
      {
        label: title, 
        data: type.value.slice(),
        backgroundColor: color,
        borderColor: color,
        datalabels: {
          color: '#fff',
          align: 'top',
          anchor: 'start',
          font: {
            size: fontSize.value
          }
        },
        tension: 0.1
      },
    ],
  }
}

const updateTemperatureChart = () => {
  temperatureChartData.value = {
    labels: time.value,
    datasets: [
      {
        label: 'Outside Temperature', 
        data: outsideTemperature.value.slice(),
        backgroundColor: '#fd1d1d',
        borderColor: '#fd1d1d',
        datalabels: {
          color: '#fff',
          align: 'top',
          font: {
            size: fontSize.value
          }
        },
        tension: 0.1
      },
      {
        label: 'Room Temperature', 
        data: roomTemperature.value.slice(),
        backgroundColor: '#62cc4c',
        borderColor: '#62cc4c',
        datalabels: {
          color: '#fff',
          align: 'bottom',
          font: {
            size: fontSize.value
          }
        },
        tension: 0.1
      }
    ],
  }
}

const updateHalfCycleChart = (type, typeChart, typeChartOption, title, color) => {

  let rotation = -120
  let circumference = 240;
  let cutout = 78;
  let display = true;
  if (title == 'Humidity') {
    rotation = -120;
    circumference = 240;
    cutout = 52;
  } else if (title == 'Edge') {
    cutout = 88;
    display = false;
  } else if (title == 'PlaceHolder') {
    cutout = 93;
    display = false;
  } else if (title.includes('Temperature')) {
    rotation = -90;
    circumference = 360; 
    cutout = 75
  }

  typeChart.value = {
    labels: [title],
    datasets: [
      {
        label: '',
        data: [type.value[type.value.length - 1], 100 - type.value[type.value.length - 1]],
        backgroundColor: [
          color,
          "#e5e7eb"
        ],
        borderColor: [
          color,
          '#e5e7eb'
        ],
        borderWidth: 0,
      },      
    ],
  }

  typeChartOption.value = {
    rotation: rotation,
    circumference: circumference,
    cutout: cutout,
    plugins: {
      tooltip: {
        enabled: false
      },
      datalabels: {
        display: false
      },
      legend: {
        display: display,
      }

    },
  }
}

const emit = defineEmits(['openSetting'])

const updateFanAndMistImg = () => {
  fan.value == 1 ? fan_img.value = './assets/icon/Fan_ON.png' : fan_img.value = './assets/icon/Fan_OFF.png';
  mist.value == 1 ? mist_img.value = './assets/icon/PhunSuong_ON.png' : mist_img.value = './assets/icon/PhunSuong_OFF.png'
  heater.value == 1 ? heater_img.value = './assets/icon/heater_ON.png' : heater_img.value = './assets/icon/heater_OFF.png';
  mistMushroom.value == 1 ? mistMushroom_img.value = './assets/icon/PhunSuong_ON.png' : mistMushroom_img.value = './assets/icon/PhunSuong_OFF.png'
}

const updateManualValue = (type) => {
  emit('openSetting', type);
}

watchEffect(() => {
  if (!isMushroomHouse.value) {
    onValueChange();
    
    updateChart(co2Concentration, co2ConcentrationChartData, 'CO2 Concentration', '#9ea1a7');
    updateChart(roomHumidity, roomHumidityChartData, 'Room Humidity', '#05c8ff');
    updateTemperatureChart();

    updateHalfCycleChart(co2Concentration, co2ConcentrationHalfCycleChartData, co2ConcentrationHalfCycleChartOptions, 'CO2 Concentration', '#9ea1a7');
    updateHalfCycleChart(roomHumidity, roomHumidityHalfCycleChartData, roomHumidityHalfCycleChartOptions, 'Humidity', '#05c8ff');
    updateHalfCycleChart(outsideTemperature, outsideTemperatureHalfCycleChartData, outsideTemperatureHalfCycleChartOptions, 'Outside Temperature', '#ff0000');
    updateHalfCycleChart(roomTemperature, roomTemperatureHalfCycleChartData, roomTemperatureHalfCycleChartOptions, 'Room Temperature', '#62cc4c');

    updateHalfCycleChart(edge, edgeChartData, edgeChartOptions, 'Edge', 'rgb(224, 201, 163)');
    updateHalfCycleChart(placeHolder, placeHolderChartData, placeHolderChartOptions, 'PlaceHolder', '#05c8ff');
  } else {
    onMushroomValueChange();

    updateChart(light, lightChartData, 'light', '#f7ff00');
    updateChart(humidity, humidityChartData, 'humidity', '#05c8ff');
    updateChart(temperature, temperatureChartData, 'temperature', '#fd1d1d');

    updateHalfCycleChart(humidity, humidityHalfCycleChartData, humidityHalfCycleChartOptions, 'Humidity', '#05c8ff');
    updateHalfCycleChart(temperature, temperatureHalfCycleChartData, temperatureHalfCycleChartOptions, 'Temperature', '#ff0000');
    updateHalfCycleChart(light, lightHalfCycleChartData, lightHalfCycleChartOptions, 'Light', '#62cc4c');

    updateHalfCycleChart(edge, edgeChartData, edgeChartOptions, 'Edge', 'rgb(224, 201, 163)');
    updateHalfCycleChart(placeHolder, placeHolderChartData, placeHolderChartOptions, 'PlaceHolder', '#05c8ff');
  }

  
});

watchEffect(() => {
  fan.value = props.fan;
  mist.value = props.mist;
  heater.value = props.heater;
  mistMushroom.value = props.mistMushroom;
  updateFanAndMistImg();
})

onBeforeMount(() => {
  isMushroomHouse.value = props.isMushroomHouse;
})

onMounted(() => {
  if (window.innerWidth < 1024) {
    fontSize.value = 9;
  }
  
})

</script>

