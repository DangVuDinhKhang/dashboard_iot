<template>
    <Toast v-if="isSuccess" :type = "'info'"/>
    <Toast v-if="isCo2ConcentrationHigherThanCo2ConcentrationManual" :type = "'warning'"/>
    <Chart 
      v-if="!isMushroomHouse"
      :co2Concentration = "co2Concentration[co2Concentration.length - 1]"
      :outsideTemperature = "outsideTemperature[outsideTemperature.length - 1]"
      :roomTemperature = "roomTemperature[roomTemperature.length - 1]"
      :roomHumidity = "roomHumidity[roomHumidity.length - 1]"
      :fan = "fan"
      :mist= "mist"
      :time = "time[time.length - 1]"
      @updateValue = "updateValue"
      @openSetting = "openSetting" 
    />
    <Chart 
      v-if = "isMushroomHouse"
      :temperature = "temperature[temperature.length - 1]"
      :humidity = "humidity[humidity.length - 1]"
      :light = "light[light.length - 1]"
      :heater = "heater"
      :mistMushroom= "mistMushroom"
      :time = "time[time.length - 1]"
      :isMushroomHouse = "isMushroomHouse"
      @updateValue = "updateValue"
      @openSetting = "openSetting" 
    />

    <Dialog :isOpenDialog = "isOpenDialog" :typeSetting = "typeSetting" @saveData = "saveData" @resetValue="resetValue" />

</template>
<style>
body {
  background-color: rgb(205, 190, 167);
  color: #fff;
}
</style>
<script setup>
import { initializeApp } from 'firebase/app';
import { getDatabase, ref as fRef, onValue, set  } from "firebase/database"; 
import { getAnalytics } from "firebase/analytics";

const firebaseConfig = {
  apiKey: "AIzaSyAgzP_84CmYH4Oe403ySujF5gZpIZfi0eA",
  authDomain: "test1-2aeb4.firebaseapp.com",
  databaseURL: "https://test1-2aeb4-default-rtdb.firebaseio.com",
  projectId: "test1-2aeb4",
  storageBucket: "test1-2aeb4.appspot.com",
  messagingSenderId: "405318153874",
  appId: "1:405318153874:web:982c4d2f9014409e02584b",
  measurementId: "G-VTLBLY5WEJ"
};

// const mushroomHousefirebaseConfig = {
//   apiKey: "AIzaSyDRFjfVBP1WvPGSy-JziArnaVIQpl5XVpc",
//   authDomain: "iot-project-4fcde.firebaseapp.com",
//   databaseURL: "https://iot-project-4fcde-default-rtdb.asia-southeast1.firebasedatabase.app",
//   projectId: "iot-project-4fcde",
//   storageBucket: "iot-project-4fcde.appspot.com",
//   messagingSenderId: "587330592346",
//   appId: "1:587330592346:web:1d9aae9d6abe55f0d3c263",
//   measurementId: "G-F7JHMENLHE"
// };

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const co2ConcentrationRef = fRef(db, 'Tb/sensor/co2_concentration');
const outsideTemperatureRef = fRef(db, 'Tb/sensor/outside_temperature');
const roomTemperatureRef = fRef(db, 'Tb/sensor/room_temperature');
const roomHumidityRef = fRef(db, 'Tb/sensor/room_humidity');
const co2ConcentrationManualRef = fRef(db, 'Tb/state/co2_concentration_manual');
const outsideTemperatureManualRef = fRef(db, 'Tb/state/outside_temperature_manual');
const roomTemperatureManualRef = fRef(db, 'Tb/state/room_temperature_manual');
const roomHumidityManualRef = fRef(db, 'Tb/state/room_humidity_manual');
const fanRef = fRef(db, 'Tb/state/fan');
const mistRef = fRef(db, 'Tb/state/mist');

const co2Concentration = ref([]);
const outsideTemperature = ref([]);
const roomTemperature = ref([]);
const roomHumidity = ref([]);
const co2ConcentrationManual = ref();
const fan = ref();
const mist = ref();
const time = ref([]);
const isOpenDialog = ref(false);
const typeSetting = ref();
const isSuccess = ref(false);
const isCo2ConcentrationHigherThanCo2ConcentrationManual = ref(false);

const isMushroomHouse = ref(false);

// const appMushroom = initializeApp(mushroomHousefirebaseConfig, "mushroom_house");
// const dbMushroom = getDatabase(appMushroom);
const temperatureRef = fRef(db, 'mushroom_tb/sensor/temperature');
const humidityRef = fRef(db, 'mushroom_tb/sensor/humidity');
const lightRef = fRef(db, 'mushroom_tb/sensor/light');
const temperatureManualRef = fRef(db, 'mushroom_tb/state/temperature_manual');
const humidityManualRef = fRef(db, 'mushroom_tb/state/humidity_manual');
const lightManualRef = fRef(db, 'mushroom_tb/state/light_manual');
const heaterRef = fRef(db, 'mushroom_tb/state/heater');
const mistRefMushroom = fRef(db, 'mushroom_tb/state/mist');

const temperature = ref([]);
const humidity = ref([]);
const light = ref([]);
const heater = ref();
const mistMushroom = ref();

const getData = (type) => {
  onValue(type, (snapshot) => {
    switch (type._path.pieces_[2]) { 
      case 'co2_concentration': co2Concentration.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'outside_temperature': outsideTemperature.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'room_temperature': roomTemperature.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'room_humidity': roomHumidity.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'co2_concentration_manual': co2ConcentrationManual.value = Math.round(parseFloat(snapshot.val())); break;
      case 'fan': fan.value = Number(snapshot.val()); break;
      case 'mist': mist.value = Number(snapshot.val()); break;
      default: break;
    } 
  });
}

const getDataForMushroom = (type) => {
  onValue(type, (snapshot) => {
    console.log(snapshot)
    switch (type._path.pieces_[2]) { 
      case 'temperature': temperature.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'humidity': humidity.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'light': light.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'heater': heater.value = Number(snapshot.val()); break;
      case 'mist': mistMushroom.value = Number(snapshot.val()); break;
      default: break;
    } 
  });
}

const getAllData = () => {
  getData(co2ConcentrationRef);
  getData(outsideTemperatureRef);
  getData(roomTemperatureRef);
  getData(roomHumidityRef);
  getData(co2ConcentrationManualRef);
  
  if (co2Concentration.value[co2Concentration.value.length - 1] > co2ConcentrationManual.value) {
    isCo2ConcentrationHigherThanCo2ConcentrationManual.value = true;
  } else {
    isCo2ConcentrationHigherThanCo2ConcentrationManual.value = false;
  }
}

const getAllDataForMushroom = () => {
  getDataForMushroom(temperatureRef);
  getDataForMushroom(humidityRef);
  getDataForMushroom(lightRef);
}

const updateValue = (type, data) => {
  
  let refType = null;
  switch(type) {
    case 'fan': 
      refType = fanRef; 
      data == 0 ? data = 1 : data = 0; break;
    case 'mist': 
      refType = mistRef; 
      data == 0 ? data = 1 : data = 0; break;
    case 'heater':
      refType = heaterRef;
      data == 0 ? data = 1 : data = 0; break;
    case 'mistMushroom':
      refType = mistRefMushroom; 
      data == 0 ? data = 1 : data = 0; break;
    case 'co2Concentration':
      refType = co2ConcentrationManualRef; break;
    case 'roomHumidity':
      refType = roomHumidityManualRef; break;
    case 'outsideTemperature':
      refType = outsideTemperatureManualRef; break;
    case 'roomTemperature':
      refType = roomTemperatureManualRef; break;
    case 'light':
      refType = lightManualRef; break;
    case 'humidity':
      refType = humidityManualRef; break;
    case 'temperature':
      refType = temperatureManualRef; break;
  }

  set(refType, data.toString())
    .then(() => {
        getData(refType);
        resetValue();
        isSuccess.value = true;
        setTimeout(() => {
          isSuccess.value = false;
        }, 3000)
        console.log('Successfully');
    })
    .catch((error) => {
        console.error('Fail:', error) 
    });
}

const openSetting = (type) => {
  typeSetting.value = type;
  isOpenDialog.value = true;
}

const saveData = (data) => {
  updateValue(typeSetting.value, data)
}

const resetValue = () => {
  isOpenDialog.value = false;
  typeSetting.value = '';
}

onBeforeMount(() => {
  const route = useRoute();
  if (route.query.page === 'mushroom_house') {
    isMushroomHouse.value = true
  }
})

onMounted(() => {

  getData(fanRef);
  getData(mistRef);
  getDataForMushroom(heaterRef);
  getDataForMushroom(mistRefMushroom);
  setInterval(()=>{
    if (!isMushroomHouse.value) {
      getAllData();
    } else {
      getAllDataForMushroom();
    }
    const today = new Date();
    time.value.push(today.toLocaleTimeString());

  }, 3000)


})


</script>