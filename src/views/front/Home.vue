<template>
  <div class="main-content home-page">
    <!-- 顶部轮播图 -->
    <div class="home-banner">
      <el-carousel :interval="4000" arrow="hover" height="220px">
        <el-carousel-item v-for="(img, index) in swiperList" :key="index">
          <div class="home-banner-slide" :style="{ backgroundImage: 'url(' + img + ')' }">
            <div class="home-banner-overlay">
              <div class="home-banner-title">欢迎来到校园论坛</div>
              <div class="home-banner-desc">
                在这里，你可以浏览与学校生活、学习交流、社团活动等相关的最新帖子内容，无论是学习干货还是休闲分享，都能在这里找到灵感与共鸣。
              </div>
            </div>
          </div>
        </el-carousel-item>
      </el-carousel>
    </div>

    <div class="home-layout">

      <div class="home-left card">
        <div class="category-item" :class="{ 'category-item-active': item.name === current }"
             v-for="item in categoryList" :key="item.id" @click="selectCategory(item.name)">{{ item.name }}</div>
      </div>

      <div class="home-center">

        <blog-list :categoryName="current" ref="blogListRef" />

        <Footer />

      </div>

      <div class="home-right">
        <div class="card home-welcome-card">
          <div class="home-welcome-title">欢迎来到校园论坛</div>
          <div class="home-welcome-subtitle">在这里，记录、分享、交流每一天的校园生活</div>
          <a href="/front/person" class="home-welcome-btn">立即发帖</a>
        </div>

        <div class="card home-rank-card">
          <div class="home-card-header">
            <div class="home-card-title">热门文章榜</div>
            <div class="home-card-action" @click="refreshTop">
              <i class="el-icon-refresh"></i>
              换一换
            </div>
          </div>
          <div>
            <div v-for="item in showList" :key="item.id" class="home-rank-item line1">
              <a :href="'/front/blogDetail?blogId=' + item.id" target="_blank">
                <span class="home-rank-index">
                  <span style="color: orangered" v-if="item.index === 1">{{ item.index }}</span>
                  <span style="color: goldenrod" v-else-if="item.index === 2">{{ item.index }}</span>
                  <span style="color: dodgerblue" v-else-if="item.index === 3">{{ item.index }}</span>
                  <span style="color: #666" v-else>{{ item.index }}</span>
                </span>
                <span class="home-rank-title">{{ item.title }}</span>
              </a>
            </div>
          </div>
        </div>

        <div class="home-activity-list">
          <div v-for="item in topActivityList" :key="item.id" class="home-activity-item">
            <a :href="'/front/activityDetail?activityId=' + item.id" target="_blank">
              <img :src="item.cover" alt="" class="home-activity-cover">
            </a>
          </div>
        </div>

        <div class="home-contact"></div>

      </div>



    </div>
  </div>
</template>

<script>

import Footer from "@/components/Footer";
import BlogList from "@/components/BlogList";
export default {
  components: {
    BlogList,
    Footer
  },
  data() {
    return {
      current: '全部博客',  //当前选中的分类名称
      categoryList: [],

      topList: [],
      showList: [],
      lastIndex: 0,
      topActivityList: [],

      swiperList: [
        require('@/assets/imgs/swiper/01.png'),
        require('@/assets/imgs/swiper/02.png'),
        require('@/assets/imgs/swiper/03.png'),
        require('@/assets/imgs/swiper/04.png'),
        require('@/assets/imgs/swiper/05.png'),
      ]
    }
  },
  mounted() {
    this.load()

    this.refreshTop()

    this.loadTopActivity()

  },
  // methods：本页面所有的点击事件或者其他函数定义区
  methods: {
    loadTopActivity() {
      this.$request.get('/activity/selectTop').then(res => {
        this.topActivityList = res.data || []
      })
    },
    refreshTop() {
      this.$request.get('/blog/selectTop').then(res => {
        this.topList = res.data || []
        let i = 1
        this.topList.forEach(item => item.index = i++)

        // 0  5  0
        if (this.lastIndex === 20) {
          this.lastIndex = 0
        }
        this.showList = this.topList.slice(this.lastIndex, this.lastIndex+5)  // 0-5   5- 10  // 0-5
        this.lastIndex += 5  // 5  10  5
      })
    },
    selectCategory(categoryName) {
      this.current = categoryName
    },
    load() {
      // 请求分类的数据
      this.$request.get('/category/selectAll').then(res => {
        this.categoryList = res.data || []
        this.categoryList.unshift({ name: '全部博客' })
      })
    },
  }
}
</script>

<style>
/* 页面整体与主题色 */
.home-page {
  --primary-color: #5b6bff;
  --primary-color-light: #eef0ff;
  --primary-gradient: linear-gradient(135deg, #5b6bff, #7d5bff);
  --text-main: #1f2430;
  --text-sub: #6b7280;
  --card-radius: 16px;
  --card-shadow: 0 14px 40px rgba(15, 35, 95, 0.08);

  background: #f5f6fb;
  min-height: calc(100vh - 120px);
  padding: 20px 0 30px;
  box-sizing: border-box;
}

.home-banner {
  width: 100%;
  margin: 0 0 10px;
  padding: 0;
  overflow: hidden;
  background: transparent;
  box-shadow: none;
  border-radius: 0;
}

.home-banner .el-carousel__container {
  border-radius: var(--card-radius);
  overflow: hidden;
}

.home-banner-slide {
  width: 100%;
  height: 220px;
  background-size: cover;
  background-position: center;
  position: relative;
}

.home-banner-overlay {
  position: absolute;
  left: 40px;
  bottom: 40px;
  max-width: 520px;
  color: #fff;
  text-shadow: 0 3px 12px rgba(0, 0, 0, 0.45);
}

.home-banner-title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 10px;
}

.home-banner-desc {
  font-size: 13px;
  line-height: 1.7;
  opacity: 0.9;
}

.home-layout {
  display: flex;
  align-items: flex-start;
  column-gap: 16px;
}

.home-left {
  width: 180px;
}

.home-center {
  flex: 1;
  margin: 0 12px;
}

.home-right {
  width: 280px;
}

/* 卡片基础样式，仅在本页生效，避免影响其它地方 */
.home-page .card {
  background: #ffffff;
  border-radius: var(--card-radius);
  box-shadow: var(--card-shadow);
  border: none;
  padding: 18px 20px;
  box-sizing: border-box;
}

/* 左侧分类导航 */
.category-item {
  padding: 10px 14px;
  font-size: 14px;
  color: var(--text-sub);
  cursor: pointer;
  border-radius: 10px;
  margin-bottom: 6px;
  transition: all 0.2s ease;
  text-align: left;
}

.category-item:hover {
  background: var(--primary-color-light);
  color: var(--primary-color);
}

.category-item-active {
  background-image: var(--primary-gradient);
  color: #ffffff;
  box-shadow: 0 10px 24px rgba(91, 107, 255, 0.4);
}

/* 右侧欢迎卡片 */
.home-welcome-card {
  margin-bottom: 14px;
  background-image: var(--primary-gradient);
  color: #ffffff;
  position: relative;
  overflow: hidden;
}

.home-welcome-title {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 6px;
}

.home-welcome-subtitle {
  font-size: 13px;
  opacity: 0.9;
  margin-bottom: 14px;
}

.home-welcome-btn {
  display: inline-block;
  padding: 6px 16px;
  border-radius: 999px;
  background: #ffffff;
  color: var(--primary-color);
  font-size: 13px;
  text-decoration: none;
  font-weight: 500;
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.12);
}

.home-welcome-btn:hover {
  opacity: 0.95;
}

/* 右侧榜单卡片 */
.home-rank-card {
  margin-bottom: 14px;
}

.home-card-header {
  display: flex;
  align-items: center;
  padding-bottom: 10px;
  border-bottom: 1px solid #edf0f7;
}

.home-card-title {
  flex: 1;
  font-size: 16px;
  font-weight: 600;
  color: var(--text-main);
}

.home-card-action {
  font-size: 12px;
  color: var(--primary-color);
  cursor: pointer;
  display: flex;
  align-items: center;
  column-gap: 4px;
}

.home-card-action:hover {
  opacity: 0.8;
}

.home-rank-item {
  margin: 14px 0;
  display: flex;
  align-items: center;
}

.home-rank-item a {
  display: flex;
  align-items: center;
  color: inherit;
}

.home-rank-index {
  width: 20px;
  display: inline-block;
  text-align: right;
  margin-right: 10px;
  font-weight: 600;
}

.home-rank-title {
  color: #4b5563;
  font-size: 13px;
}

/* 活动推荐 */
.home-activity-list {
  margin-bottom: 14px;
}

.home-activity-item {
  margin-bottom: 10px;
}

.home-activity-cover {
  width: 100%;
  border-radius: 12px;
  display: block;
}

/* 底部联系方式 */
.home-contact {
  line-height: 26px;
  color: #9ca3af;
  padding: 0 6px;
  font-size: 12px;
}
</style>
