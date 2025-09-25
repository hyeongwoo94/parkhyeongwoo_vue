<template>
  <div class="spinner_container">
    <div class="spinner_wheel" ref="wheel" @scroll="onScroll">
      <div 
        v-for="(item, index) in paddedItems" 
        :key="item === null ? 'pad-' + index : item"
        :class="['spinner_item', { 
          selected: item === modelValue,
          null: item === null 
          }]"
        @click="selectItem(item)" 
      >
        {{ item }} {{ item_txt }}
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, watch, onMounted, nextTick } from 'vue';
import './SpinnerWheel.scss'

/* -----------------------------
  Props
----------------------------- */
interface Props<T> {
  items: T[];
  modelValue: T | null;
  item_txt:string
}

const props = defineProps<Props<number | string>>();
const emit = defineEmits<{ (e: 'update:modelValue', value: number | string): void }>();

/* -----------------------------
  Refs & 상태
----------------------------- */
const wheel = ref<HTMLElement | null>(null);
const itemHeight = 35;             
const visibleCount = 7;            
const centerIndex = Math.floor(visibleCount / 2);
let scrolling = false;
let scrollTimeout: number | null = null;

/* -----------------------------
  paddedItems
  - 위/아래 padding 추가
----------------------------- */
const paddedItems = computed(() => {
  const pad = Array(centerIndex).fill(null);
  return [...pad, ...props.items, ...pad];
});

/* -----------------------------
  scroll 이벤트
  - 가운데 아이템 자동 선택
----------------------------- */
const onScroll = () => {
  if (!wheel.value) return;
  if (scrollTimeout) clearTimeout(scrollTimeout);

  scrollTimeout = window.setTimeout(() => {
    const scrollTop = wheel.value!.scrollTop;
    const index = Math.round(scrollTop / itemHeight) + centerIndex;
    const value = paddedItems.value[index];

    if (value !== undefined && value !== null && value !== props.modelValue) {
      scrolling = true;
      emit('update:modelValue', value);
      scrolling = false;
    }
  }, 50);
};

/* -----------------------------
  클릭 이벤트
  - 클릭한 아이템을 가운데로 이동
----------------------------- */
const selectItem = (item: number | string | null) => {
  if (item === null) return;
  emit('update:modelValue', item);

  nextTick(() => {
    if (!wheel.value) return;
    const index = paddedItems.value.indexOf(item);
    if (index >= 0) wheel.value.scrollTop = (index - centerIndex) * itemHeight;
  });
};

/* -----------------------------
  초기값 중앙 스크롤
----------------------------- */
onMounted(() => {
  nextTick(() => {
    if (!wheel.value || props.modelValue === null) return;
    const index = paddedItems.value.indexOf(props.modelValue);
    if (index >= 0) wheel.value.scrollTop = (index - centerIndex) * itemHeight;
  });
});

/* -----------------------------
  부모에서 modelValue 변경 시
  - scroll 중이 아니면 가운데 맞춤
----------------------------- */
watch(() => props.modelValue, (newVal) => {
  if (!wheel.value || scrolling) return;
  const index = paddedItems.value.indexOf(newVal!);
  if (index >= 0) {
    requestAnimationFrame(() => {
      wheel.value!.scrollTop = (index - centerIndex) * itemHeight;
    });
  }
});

</script>
