<template>
    <div class="toast fixed shadow-2xl w-[60%] lg:w-[17%] h-[70px] mr-2 rounded-md bg-green-500 p-5 absolute top-[2%] left-[20%] lg:left-[83%] z-20 text-md lg:text-xl text-center">Save Data Successfully</div>
</template>

<style>
@keyframes shake {
  0% { transform: translateX(0); }
  10%, 90% { transform: translateX(-10px); }
  20%, 80% { transform: translateX(10px); }
  30%, 50%, 70% { transform: translateX(-10px); }
  40%, 60% { transform: translateX(10px); }
  100% { transform: translateX(0); }
}

.toast {
  animation: shake 3s cubic-bezier(.36,.07,.19,.97) infinite;
}
</style>
          