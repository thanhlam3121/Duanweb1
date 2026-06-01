<template>
  <div class="home-view-container">
    <div class="container my-5">
      <h2 class="text-center section-title">🍔 Khám Phá Món Ăn Nơi Đây</h2>
      <div class="food-grid other-foods">
        <div
          v-for="food in dsMonDangHienThi"
          :key="food.name"
          class="food-card" >
          <router-link :to="`/food/${food.name}`" class="food-card-link">
            <img :src="food.image" :alt="food.name" />
            <h3>{{ food.name }}</h3>
            <p>{{ food.description }}</p>
            <p><strong>{{ food.price.toLocaleString('vi-VN') }}₫</strong></p>
          </router-link>
          <button class="add-button" @click="handleAddToCart(food)">Mua ngay</button>
        </div>
      </div>

      <div class="text-center load-more-section">
        <button
          v-if="soMonDangHienThi < allFoods.length"
          @click="hienThiThemMon"
          class="load-more-button">
          Xem thêm {{ allFoods.length - soMonDangHienThi }} sản phẩm
        </button>
        <p v-else class="all-foods-loaded">Đã hiển thị tất cả món ăn.</p>
      </div>
    </div>

    <div v-if="showMessage" class="toast-notification" :class="{ 'show': showMessage }">
      {{ messageText }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { foodItems } from '@/router/foodItems';
import { useCartStore } from '@/stores/cart';

const cartStore = useCartStore();


const showMessage = ref(false);
const messageText = ref('');
let timeoutId: number | undefined;


const allFoods = computed(() => foodItems);


const handleAddToCart = (item: typeof foodItems[0]) => {
  cartStore.addToCart(item);
  console.log('Món ăn đã được thêm vào giỏ hàng:', item.name);

  messageText.value = `${item.name} đã được thêm vào giỏ hàng!`;
  showMessage.value = true;
  if (timeoutId) {
    clearTimeout(timeoutId);
  }
  timeoutId = setTimeout(() => {
    showMessage.value = false;
    messageText.value = '';
  }, 3000);
};

// === Phần hiển thị món ăn và "Xem thêm" ===


const soLuongBanDau = 8; 

const buocXemThem = 8; 

const soMonDangHienThi = ref(soLuongBanDau);

const dsMonDangHienThi = computed(() => {
  return allFoods.value.slice(0, soMonDangHienThi.value);
});


const hienThiThemMon = () => {
  soMonDangHienThi.value = Math.min(
    soMonDangHienThi.value + buocXemThem,
    allFoods.value.length
  );
};
</script>





<style scoped>

.home-view-container {
  padding-bottom: 50px; 
  padding-top: 20px;
}

.section-title {
  font-size: 2.2em;
  font-weight: bold;
  color: #ff5722;
  margin-bottom: 30px;
  position: relative;
}

.section-title::after {
  content: '';
  position: absolute;
  left: 50%;
  bottom: -10px;
  transform: translateX(-50%);
  width: 80px;
  height: 4px;
  background-color: #ff5722;
  border-radius: 2px;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.food-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 25px;
  justify-content: center;
  transition: gap 0.3s ease;
}

.food-card {
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  padding: 15px;
  background: #ffffff;
  text-align: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.food-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.food-card-link {
  text-decoration: none;
  color: inherit;
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  margin-bottom: 10px;
}

.food-card img {
  width: 100%;
  max-height: 160px;
  object-fit: cover;
  border-radius: 8px;
  margin-bottom: 12px;
}

.food-card h3 {
  font-size: 1.3em;
  margin: 10px 0 8px;
  color: #ff5722;
  font-weight: 600;
}

.food-card p {
  color: #666;
  font-size: 0.95em;
  flex-grow: 1;
  margin-bottom: 10px;
}

.food-card strong {
  display: block;
  margin-bottom: 15px;
  color: #d32f2f;
  font-size: 1.1em;
  font-weight: bold;
}

.buy-button, .add-button {
  background-color: #ff5722;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  font-size: 1em;
  transition: background-color 0.3s ease, transform 0.2s ease;
  width: 100%;
}

.buy-button:hover, .add-button:hover {
  background-color: #e64a19;
  transform: scale(1.02);
}

/* Nút "Xem thêm sản phẩm" */
.load-more-section {
    margin-top: 50px;
    margin-bottom: 50px;
}

.load-more-button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px 25px;
  border-radius: 25px;
  cursor: pointer;
  font-size: 1.1em;
  font-weight: bold;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.load-more-button:hover {
  background-color: #0056b3;
  transform: translateY(-2px);
}

.all-foods-loaded {
  font-size: 1.1em;
  color: #555;
  font-style: italic;
}

/* Toast Notification */
.toast-notification {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%) translateY(-100%);
  background-color: rgba(40, 167, 69, 0.9);
  color: white;
  padding: 15px 30px;
  border-radius: 10px;
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.3);
  z-index: 1000;
  opacity: 0;
  visibility: hidden;
  transition: transform 0.4s ease-out, opacity 0.4s ease-out, visibility 0.4s ease-out;
  font-size: 1.3em;
  min-width: 300px;
  text-align: center;
}

.toast-notification.show {
  opacity: 1;
  visibility: visible;
  transform: translateX(-50%) translateY(0);
}
</style>
