<template>
<TransitionRoot as="template" :show="openDialog" class="z-30">
    <Dialog as="div" class="relative z-10" @close="$emit('resetValue'); needToUpdateValue = 0">
      <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100" leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
        <div @click="resetValue" class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
      </TransitionChild>
      <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
        <div class="flex min-h-full items-center justify-center p-4 text-center sm:items-center sm:p-0">
          <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95" enter-to="opacity-100 translate-y-0 sm:scale-100" leave="ease-in duration-200" leave-from="opacity-100 translate-y-0 sm:scale-100" leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">
            <DialogPanel class="relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all lg:h-[300px] sm:my-8 sm:w-full sm:max-w-lg">
              <div class="bg-white px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
                <div class="sm:flex sm:items-start">
                  <div class="mt-3 lg:mt-7 lg:ml-20 text-center sm:ml-4 sm:mt-0 sm:text-left">
                    <DialogTitle as="h3" class="font-semibold leading-6 text-gray-900 text-3xl">Setting</DialogTitle>
                    <div class="mt-10">
                      <input type="number" id="username" v-model="needToUpdateValue" class="block w-100 rounded-md border-0 py-2 pl-2 pr-2 text-gray-900 ring-1 ring-inset ring-gray-300 focus:outline-none focus:ring" placeholder="" min="0">
                      <div class="slider">
                        <input v-if="typeSetting == 'co2Concentration'" type = "range" min="0" max="5000" v-model="needToUpdateValue"/>
                        <input v-else type = "range" min="0" max="100" v-model="needToUpdateValue"/>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <div class="bg-gray-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
                <button type="button" class="inline-flex w-full justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-500 sm:ml-3 sm:w-auto" @click="saveData">Save</button>
                <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto" @click="resetValue" ref="cancelButtonRef">Cancel</button>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>
<style>
.slider {
  width: 100%;
  margin-top: 10%; 
}

input[type="range"] {
  -webkit-appearance: none !important;
  width: 100%;
  height: 17px;
  background-color: #A3CD99;
  border: 1px solid #97c68b;
  border-radius: 10px;
  margin: auto;
  transition: all 0.3s ease; 
}
input[type="range"]:hover {
  background-color: #b2d5aa; 
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none !important;
  width: 20px;
  height: 20px;
  background-color: #579E81;
  border-radius: 30px;
  box-shadow: 0px 0px 3px #3c6d59;
  transition: all 0.5s ease; 
}
input[type="range"]::-webkit-slider-thumb:hover {
  background-color: #457d66; 
}
input[type="range"]::-webkit-slider-thumb:active {
  box-shadow: 0px 0px 1px #3c6d59; 
}
</style>

<script setup>
import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'

const props = defineProps({
  isOpenDialog: Boolean,
  typeSetting: String
});
const openDialog = ref(true);  
const typeSetting = ref(''); 
const needToUpdateValue = ref(0);

const emit = defineEmits(['resetValue', 'saveData']);

const resetValue = () => {
    needToUpdateValue.value = 0;
    emit('resetValue');
}

const saveData = () => {
    emit('saveData', needToUpdateValue.value); 
    needToUpdateValue.value = 0;
}

watchEffect(() => {
    openDialog.value = props.isOpenDialog;
    typeSetting.value = props.typeSetting;
    console.log(props.typeSetting)
})
</script>