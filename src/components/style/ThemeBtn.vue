<template>
    <button @click="toggleDark">Toggle Theme</button>
    <ul>
      <li v-for="theme in themes" :key="theme.value">
        <button @click="applyTheme(theme.value)">{{ theme.label }}</button>
        </li>
    </ul>
</template>

<script setup lang="ts">
import { ref } from 'vue'
interface Themes {
    label:string,
    value:string
}
// 테마 목록 정의
const themes = ref<Themes[]>([
  { label: '빨강', value: 'red' },
  { label: '노랑', value: 'yellow' },
  { label: '파랑', value: 'blue' },
  { label: '다크', value: 'dark' },
  { label: '원본', value: '' }
])

// dark toggle 상태
const isDark = ref(false)

// 다크 모드 토글
const toggleDark = () => {
  const html = document.documentElement
  isDark.value = !isDark.value
  html.setAttribute('data-theme', isDark.value ? 'dark' : '')
}

// 선택한 테마 적용
const applyTheme = (theme: string) => {
  const html = document.documentElement
  html.setAttribute('data-theme', theme)
  // 다크 toggle 상태도 함께 업데이트
  isDark.value = theme === 'dark'
}
</script>