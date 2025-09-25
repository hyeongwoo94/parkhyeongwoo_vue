<template>
    <div>
        메인페이지
        <ThemeBtn/>
        <div class="spinner_box">
            <div class="spinner_box_date_picker">
                <!-- 연도 선택 -->
                <SpinnerWheel v-model="year" :items="years" item_txt="년"/>
                <!-- 월 선택 -->
                <SpinnerWheel v-model="month" :items="months" item_txt="월"/>
                <!-- 일 선택 -->
                <SpinnerWheel v-model="day" :items="daysInMonth" item_txt="일"/>
                <div class="spinner_select_box"></div>
            </div>
        </div>
        <p>Selected Date: {{ year }}/{{ month }}/{{ day }}</p>
    </div>
</template>


<script setup lang="ts">
import SpinnerWheel from '@/components/SpinnerWheel.vue';
import ThemeBtn from '@/components/style/ThemeBtn.vue';
import { ref, computed, watch } from "vue";





//-----------------------------------------------------------날짜선택
/* -----------------------------
  선택 가능한 년/월 범위
----------------------------- */
const currentYear = new Date().getFullYear(); // 올해 연도
const today = new Date();
const currentMonth = today.getMonth() + 1; // JS에서는 0 ~ 11이므로 +1
const currentDay = today.getDate();       // 오늘 날짜

const years = Array.from({ length: 11 }, (_, i) => currentYear - i).reverse();
const months = Array.from({ length: 12 }, (_, i) => i + 1);

/* -----------------------------
  선택 값 상태
----------------------------- */
const year = ref<number>(currentYear);   // 초기값 2025
const month = ref<number>(currentMonth);     // 초기값 1월
const day = ref<number>(currentDay);       // 초기값 1일

/* -----------------------------
  선택된 년/월에 따라 일수 계산
  - 1월 ~ 12월 각 월 마지막 일 계산
----------------------------- */
const daysInMonth = computed(() => {
  const lastDay = new Date(year.value, month.value, 0).getDate();
  return Array.from({ length: lastDay }, (_, i) => i + 1);
});

/* -----------------------------
  년/월 변경 시 day가 범위를 벗어나면 조정
----------------------------- */
watch([year, month], () => {
  if (day.value > daysInMonth.value.length) {
    day.value = daysInMonth.value.length;
  }
});
//-----------------------------------------------------------날짜선택





</script>


