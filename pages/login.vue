<template>
    <div class="main mt-5 flex flex-col h-screen justify-center items-center w-50%">
        <div class="text-center">
            <img src="https://yu.ctu.edu.vn/images/upload/article/2020/03/0305-logo-ctu.png" alt="logo ctu" width="100" height="100">
        </div>
        <p v-if="!isMushroomHouse" class="text-3xl font-bold text-center mt-5" style="color: #003995">THIẾT BỊ ĐIỀU HÒA</p>
        <p v-if="!isMushroomHouse" class="text-3xl font-bold mb-7 text-center" style="color: #003995">DƯỠNG KHÍ PHÒNG LẠNH</p>
        <p v-if="isMushroomHouse" class="text-3xl font-bold text-center mt-5" style="color: #003995">THIẾT BỊ ĐIỀU KHIỂN VÀ GIÁM SÁT</p>
        <p v-if="isMushroomHouse" class="text-3xl font-bold mb-7 text-center" style="color: #003995">NHIỆT ĐỘ VÀ ĐỘ ẨM TRONG NHÀ NẤM</p>
        
        <p class="text-2xl font-bold mb-3 text-center" style="color: #0e71ea">ĐĂNG NHẬP</p>
        
        <input type="text" id="username" class="block w-100 rounded-md border-0 py-1.5 pl-7 pr-20 text-gray-900 ring-1 ring-inset ring-gray-300" placeholder="Tài khoản">
        <input type="password" id="password" class="block w-100 rounded-md border-0 py-1.5 pl-7 pr-20 text-gray-900 ring-1 ring-inset ring-gray-300 mt-5" placeholder="Mật khẩu">
        <RouterLink v-if="!isMushroomHouse" to="/"><button class="bg-blue-600 p-2 rounded text-white font-bold mt-5 hover:bg-blue-500">ĐĂNG NHẬP</button></RouterLink>
        <RouterLink v-if="isMushroomHouse" to="/?page=mushroom_house"><button class="bg-blue-600 p-2 rounded text-white font-bold mt-5 hover:bg-blue-500">ĐĂNG NHẬP</button></RouterLink>
      
        
    </div>
</template>
<style>
body {
  background-color: rgb(205, 190, 167);
}
</style>
<script setup>

const isMushroomHouse = ref(false);

onBeforeMount(() => {
  const route = useRoute();
  if (route.query.page === 'mushroom_house') {
    isMushroomHouse.value = true
  }
})
</script>