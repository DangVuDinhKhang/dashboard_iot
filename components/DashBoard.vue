<template>
    <!-- <p>{{ co2Concentration[co2Concentration.length - 1] }}</p>
    <p>{{ outsideTemperature[co2Concentration.length - 1] }}</p>
    <p>{{ roomTemperature[co2Concentration.length - 1] }}</p>
    <p>{{ roomHumidity[co2Concentration.length - 1] }}</p> -->
    <Toast v-if="isSuccess"/>
    <Chart 
      :co2Concentration = "co2Concentration[co2Concentration.length - 1]"
      :outsideTemperature = "outsideTemperature[co2Concentration.length - 1]"
      :roomTemperature = "roomTemperature[co2Concentration.length - 1]"
      :roomHumidity = "roomHumidity[co2Concentration.length - 1]"
      :fan = "fan"
      :mist= "mist"
      :time = "time[time.length - 1]"
      @updateValue = "updateValue"
      @openSetting = "openSetting" 
    />

    <Dialog :isOpenDialog = "isOpenDialog" @saveData = "saveData" @resetValue="resetValue" />

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
const fan = ref();
const mist = ref();
const time = ref([]);
const isOpenDialog = ref(false);
const typeSetting = ref();
const isSuccess = ref(false);

const getData = (type) => {
  
  onValue(type, (snapshot) => {
    
    console.log(type._path.pieces_[2])
    switch (type._path.pieces_[2]) { 
      case 'co2_concentration': co2Concentration.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'outside_temperature': outsideTemperature.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'room_temperature': roomTemperature.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'room_humidity': roomHumidity.value.push(Math.round(parseFloat(snapshot.val()))); break;
      case 'fan': fan.value = Number(snapshot.val()); break;
      case 'mist': mist.value = Number(snapshot.val()); break;

    } 
  });
}

const getAllData = () => {
  getData(co2ConcentrationRef);
  getData(outsideTemperatureRef);
  getData(roomTemperatureRef);
  getData(roomHumidityRef);
}

const updateValue = (type, data) => {
  
  let refType = null;
  switch(type) {
    case 'fan': 
      refType = fanRef; 
      data == 0 ? data = 1 : data = 0; break;
    case 'mist': 
      refType = mistRef; 
      data == 0 ? data = 1 : data = 0;break;
    case 'co2Concentration':
      refType = co2ConcentrationManualRef; break;
    case 'roomHumidity':
      refType = roomHumidityManualRef; break;
    case 'outsideTemperature':
      refType = outsideTemperatureManualRef; break;
    case 'roomTemperature':
      refType = roomTemperatureManualRef; break;
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
  isOpenDialog.value = true;
  typeSetting.value = type;
}

const saveData = (data) => {
  updateValue(typeSetting.value, data)
}

const resetValue = () => {
  isOpenDialog.value = false;
  typeSetting.value = '';
}

onMounted(() => {

  // getData(fanRef);
  // getData(mistRef);
  // getAllData();
  // const today = new Date();
  //   time.value.push(today.toLocaleTimeString());
  setInterval(()=>{

    getAllData();
    const today = new Date();
    time.value.push(today.toLocaleTimeString());

  }, 3000)


})


</script>