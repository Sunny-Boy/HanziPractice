<script setup>
import HelloWorld from './components/HelloWorld.vue'

const fontFamilyOptions = [
  { label: '宋体', value: '-apple-system, "SimSun", "STSong", serif' },
  { label: '楷体', value: '-apple-system, "KaiTi", "STKaiti", serif' },
  { label: '黑体', value: '-apple-system, "SimHei", "STHeiti", sans-serif' },
  { label: '仿宋', value: '-apple-system, "FangSong", "STFangsong", serif' },
  { label: '硬笔楷体', value: '"Hard-Pen-Kaishu", "KaiTi", "STKaiti", serif' },
]
</script>

<template>
  <HelloWorld />
</template>

<style>
/* 确保字体在移动端也能正常显示 */
@media screen and (max-width: 768px) {
  * {
    -webkit-text-size-adjust: 100%;
    -moz-text-size-adjust: 100%;
    -ms-text-size-adjust: 100%;
  }
}
</style>
