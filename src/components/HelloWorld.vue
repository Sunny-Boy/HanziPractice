<script setup>
import { ref, computed } from 'vue'
import html2pdf from 'html2pdf.js'

const userName = ref('')
const dayCount = ref('')
const currentDate = ref(new Date())
const year = ref(currentDate.value.getFullYear())
const month = ref(currentDate.value.getMonth() + 1)
const day = ref(currentDate.value.getDate())
const quote = ref('汉字是中华文明的瑰宝，书法是汉字的艺术升华')
const sheetTitle = ref('每日打卡')

// 设置选项
const selectedChars = ref('一日目月')
const fontFamily = ref('硬笔楷书')
const fontFamilies = ['楷体', '宋体', '隶书', '魏碑', '硬笔楷书']
const charOpacity = ref(0.3) // 字体透明度
const repeatCount = ref(4) // 每个字重复次数
const totalCount = ref(7) // 每行总格子数
const instruction = ref('短横：起笔时要轻，由轻而重，收笔时减轻收回，略向上斜，形不宜长。') // 自定义说明文字

// 将字符串转换为字符数组
const charArray = computed(() => selectedChars.value.split(''))

// PDF导出配置
const pdfOptions = {
  margin: 10,
  filename: '汉字练习字帖.pdf',
  image: { type: 'jpeg', quality: 0.98 },
  html2canvas: { 
    scale: 2,
    useCORS: true,
    letterRendering: true
  },
  jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
}

// 导出PDF方法
const exportPDF = () => {
  const element = document.querySelector('.practice-sheet')
  html2pdf().set(pdfOptions).from(element).save()
}

// 打印方法
const printSheet = () => {
  window.print()
}
</script>

<template>
  <div class="calligraphy-container">
    <!-- 顶部标题区域 -->
    <div class="header-section">
      <h1 class="main-title">汉字练习生</h1>
      <p class="quote">{{ quote }}</p>
    </div>

    <!-- 设置区域 -->
    <div class="settings-panel">
      <div class="settings-left">
        <div class="setting-item">
          <label>页面标题：</label>
          <input 
            v-model="sheetTitle" 
            type="text" 
            placeholder="请输入页面标题"
            style="width: 200px"
          />
        </div>
        <div class="setting-item">
          <label>练习文字：</label>
          <input v-model="selectedChars" type="text" placeholder="请输入要练习的汉字" />
        </div>
        <div class="setting-item">
          <label>字体选择：</label>
          <select v-model="fontFamily">
            <option v-for="font in fontFamilies" :key="font" :value="font">{{ font }}</option>
          </select>
        </div>
        <div class="setting-item">
          <label>临摹透明度：</label>
          <input 
            v-model="charOpacity" 
            type="range" 
            min="0.1" 
            max="1" 
            step="0.1"
          />
        </div>
        <div class="setting-item">
          <label>示范字数：</label>
          <input 
            v-model="repeatCount" 
            type="number" 
            min="1" 
            max="6"
            style="width: 60px"
          />
        </div>
        <div class="setting-item full-width">
          <label>练习说明：</label>
          <input 
            v-model="instruction" 
            type="text" 
            placeholder="请输入练习说明文字"
          />
        </div>
      </div>
      <div class="settings-right">
        <button class="action-btn export-btn" @click="exportPDF">
          导出PDF
        </button>
        <button class="action-btn print-btn" @click="printSheet">
          打印字帖
        </button>
      </div>
    </div>

    <!-- 预览区域 -->
    <div class="preview-section">
      <div class="practice-sheet">
        <div class="sheet-header">{{ sheetTitle }}</div>
        <div class="sheet-info">
          <div class="info-row">
            <span>姓名：<span class="line-only">_________</span></span>
            <span class="day-count">第<span class="line-only">___</span>天</span>
            <span class="date">
              <span class="line-only">_____</span>年
              <span class="line-only">___</span>月
              <span class="line-only">___</span>日
            </span>
          </div>
        </div>
        <div class="instruction">{{ instruction }}</div>
        <div class="grid-container">
          <div v-for="char in charArray" :key="char" class="char-row">
            <div v-for="n in totalCount" :key="n" class="practice-grid">
              <div class="grid-lines">
                <div class="horizontal"></div>
                <div class="vertical"></div>
                <div class="diagonal1"></div>
                <div class="diagonal2"></div>
              </div>
              <div 
                v-if="n <= repeatCount" 
                class="practice-char" 
                :class="{ 'example': n === 1 }" 
                :style="{ 
                  fontFamily,
                  opacity: n === 1 ? 1 : charOpacity
                }"
              >{{ char }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.calligraphy-container {
  max-width: 800px;
  margin: 20px auto;
  font-family: "Microsoft YaHei", sans-serif;
}

.header-section {
  text-align: center;
  margin-bottom: 30px;
}

.main-title {
  font-size: 36px;
  color: #333;
  margin-bottom: 15px;
  font-weight: bold;
}

.quote {
  font-size: 16px;
  color: #666;
  font-style: italic;
  line-height: 1.5;
}

.practice-sheet {
  border: 2px solid #4CAF50;
  padding: 20px;
  background: white;
}

.sheet-header {
  text-align: center;
  color: #ff0000;
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.sheet-info {
  margin-bottom: 15px;
}

.info-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.line-only {
  display: inline-block;
  position: relative;
  padding: 0 2px;
}

.line-only::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 1px;
  background-color: #000;
}

.line-only:first-child {
  min-width: 80px; /* 姓名的下划线 */
}

.date .line-only:first-child {
  min-width: 40px; /* 年份的下划线 */
}

.date .line-only {
  min-width: 30px; /* 月份和日期的下划线 */
}

.instruction {
  margin: 15px 0;
  color: #333;
  font-size: 14px;
}

.grid-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.char-row {
  display: flex;
  gap: 10px;
  justify-content: space-between;
}

.practice-grid {
  width: 100px;
  height: 100px;
  position: relative;
  border: 1px solid #4CAF50;
  background: white;
}

.grid-lines {
  position: absolute;
  width: 100%;
  height: 100%;
}

.horizontal, .vertical, .diagonal1, .diagonal2 {
  position: absolute;
  background: #E8F5E9;
}

.horizontal {
  width: 100%;
  height: 1px;
  top: 50%;
  left: 0;
}

.vertical {
  width: 1px;
  height: 100%;
  left: 50%;
  top: 0;
}

.diagonal1 {
  width: 1px;
  height: 141%;
  top: -20%;
  left: 50%;
  transform: rotate(45deg);
}

.diagonal2 {
  width: 1px;
  height: 141%;
  top: -20%;
  left: 50%;
  transform: rotate(-45deg);
}

.practice-char {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 45px;
  z-index: 1;
}

.example {
  color: #ff0000;
}

.date {
  white-space: nowrap;
}

.settings-panel {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.settings-left {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  flex: 1;
}

.settings-right {
  display: flex;
  gap: 10px;
  align-items: flex-start;
}

.action-btn {
  padding: 8px 20px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.3s;
  font-weight: bold;
}

.export-btn {
  background-color: #4CAF50;
  color: white;
}

.export-btn:hover {
  background-color: #45a049;
}

.print-btn {
  background-color: #2196F3;
  color: white;
}

.print-btn:hover {
  background-color: #1e88e5;
}

@media print {
  .settings-panel,
  .header-section {
    display: none;
  }

  .practice-sheet {
    margin: 0;
    border: none;
  }

  .calligraphy-container {
    margin: 0;
    padding: 0;
  }
}

.full-width {
  width: 100%;
}

.setting-item {
  display: flex;
  align-items: center;
  gap: 10px;
}

.setting-item label {
  white-space: nowrap;
  font-weight: bold;
  color: #333;
  min-width: 80px;
}

.setting-item input,
.setting-item select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.setting-item input[type="text"] {
  flex: 1;
  min-width: 200px;
}

.setting-item input[type="range"] {
  width: 100px;
}

.setting-item input[type="number"] {
  width: 60px;
  text-align: center;
}
</style>
