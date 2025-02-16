<template>
    <div class="main" :class="{ 'light-theme': theme, 'dark-theme': !theme }">
      <el-switch
        v-model="theme"
        inline-prompt
        style="--el-switch-on-color: #13ce66; --el-switch-off-color: #97a0ff"
        :active-icon="Sunny"
        :inactive-icon="Moon"
        @change="themeChange"
      />
      <button class="fullscreen-btn" @click="toggleFullScreen">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="36" height="36">
        <path :fill="theme ? 'var(--theme-text-color)' : '#fff'" d="M3 3h3V1H1v6h2V3zm18 0h-3V1h6v6h-2V3zm-9 3h3V4h-3v2zm0 4h3v-2h-3v2zm0 4h3v-2h-3v2zm0 4h3v-2h-3v2zm-4-4h3v-2H8v2zm0-4h3V4H8v2zm0-4h3V1H8v2zm0 8h3v-2H8v2zm0 4h3v-2H8v2zm0 4h3v-2H8v2zm0 4h3v-2H8v2z"/>
      </svg>
    </button>
      <div class="clock-container">
        <div class="clock">
          <h1>{{ time }}</h1>
        </div>
        <div class="date">
          <span>{{ formattedDate }}</span>
        </div>
      </div>
  
      <a href="https://github.com/RADEKWRLD" target="_blank" class="github-icon">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="36" height="36">
          <path :fill="theme ? 'var(--theme-text-color)' : '#fff'" d="M12 0C5.372 0 0 5.372 0 12c0 5.298 3.438 9.799 8.205 11.387.6.111.826-.261.826-.579 0-.288-.011-1.05-.016-2.065-3.338.727-4.043-1.56-4.043-1.56-.544-1.381-1.33-1.749-1.33-1.749-1.088-.743.083-.728.083-.728 1.204.085 1.835 1.236 1.835 1.236 1.07 1.83 2.809 1.303 3.495.997.107-.776.418-1.303.76-1.604-2.665-.303-5.466-1.335-5.466-5.93 0-1.308.47-2.377 1.243-3.215-.124-.303-.537-1.527.116-3.181 0 0 1.007-.32 3.299 1.235.957-.266 1.978-.4 2.995-.405 1.017.005 2.038.14 2.995.405 2.292-1.554 3.298-1.235 3.298-1.235.654 1.654.24 2.878.116 3.181.773.838 1.243 1.907 1.243 3.215 0 4.605-2.801 5.624-5.475 5.923.429.371.81.965.81 1.951 0 .322.226.697.834.579C20.563 21.799 24 17.298 24 12c0-6.628-5.372-12-12-12z"/>
        </svg>
      </a>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onUnmounted } from 'vue';
  import { Sunny, Moon } from '@element-plus/icons-vue';
  
  const theme = ref(true); // true 为白天模式，false 为夜晚模式
  const time = ref('');
  const formattedDate = ref('');
  
  // 更新显示时间
  function updateTime() {
    const currentTime = new Date();
    time.value = currentTime.toLocaleTimeString();
    formattedDate.value = formatDate(currentTime);
  }
  
  // 格式化日期
  function formatDate(date) {
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    return `${year}年${month}月${day}日`;
  }
  
  // 主题切换逻辑
  function themeChange(val) {
    const htmlElement = document.documentElement;
    htmlElement.className = val ? 'light-theme' : 'dark-theme';
    localStorage.setItem('theme', val ? 'light' : 'dark');  // 只在切换时更新存储
  }
  
  // 初始化主题
  function initTheme() {
    const savedTheme = localStorage.getItem('theme');
    theme.value = savedTheme ? savedTheme === 'light' : isDayTime();
    themeChange(theme.value);
  }
  
  // 判断当前是否是白天
  function isDayTime() {
    const currentHour = new Date().getHours();
    return currentHour >= 6 && currentHour < 18;
  }
  
//网页全屏
  function toggleFullScreen() {
  const doc = document.documentElement;
  if (!document.fullscreenElement) {
    doc.requestFullscreen().catch(err => {
      console.error(`Error attempting to enable full-screen mode: ${err.message} (${err.name})`);
    });
  } else {
    if (document.exitFullscreen) {
      document.exitFullscreen();
    }
  }
}

  // 启动时挂载组件
  onMounted(() => {
    updateTime();
    setInterval(updateTime, 1000); // 每秒更新一次时间
    initTheme(); // 初始化主题
  });
  
  // 在组件卸载时清理定时器
  onUnmounted(() => {
    clearInterval(updateTime); // 清理定时器
  });
  </script>
  
  <style lang="css" scoped>
  .clock-container {
    margin-top: -5rem;
    display: flex;
    height: 100vh;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }
  
  .clock h1 {
    font-size: 10rem;
    margin-bottom: 1rem;
  }
  
  .date span {
    font-size: 1.2rem;
  }
  
  .el-switch {
    position: fixed;
    right: 2rem; 
    bottom: 2rem; /* 向下补偿缩放 */
    z-index: 1000;
    transform: scale(1.5);
  }
  
  .main {
    position: relative;
    width: 100%;
    height: 100vh;
  }
  
  .github-icon {
    position: fixed;
    top: 2rem;
    left: 2rem;
    z-index: 1000;
    transition: fill 0.3s ease;
  }
  
 

  
  @media (max-width: 740px) {
    .main {
      transform: rotate(90deg);
      transform-origin: top left;
      width: 100vh;  /* 旋转后调整宽高 */
      height: 100vw;
      margin-left: 100%;  /* 确保旋转后内容不会被裁剪 */
    }
  
    .clock-container {
      margin-top: 0;
      flex-direction: column;
      justify-content:  center;
      height: 100%;
    }
  
    .clock h1 {
      margin-top: 1.6rem;
      font-size: 6rem;  /* 缩小时钟字体 */
    }
  
    .date span {
      font-size: 1rem;
    }
  
    .el-switch {
      right: 1rem;
      bottom: 1rem;
      transform: scale(1.2);
    }
  
    .github-icon {
      top: 1rem;
      left: 1rem;
    }
  }

  .light-theme {
    --theme-background: #fff;
    --theme-text-color: #000;
  }
  
  .dark-theme {
    --theme-background: #1b1b1b;
    --theme-text-color: #fff;
  }

    
  body {
    margin: 0;
    padding: 0;
    background-color: var(--theme-background);
    color: var(--theme-text-color);
    transition: background-color 0.3s ease;
  }

  .fullscreen-btn {
  position: fixed;
  right: 2rem;
  bottom: 8rem; /* 调整位置，避免与主题切换按钮重叠 */
  z-index: 1000;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  transform: scale(1.5);
}
  </style>
