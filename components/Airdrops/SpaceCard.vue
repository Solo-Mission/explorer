<template>
  <a
    v-ripple
    draggable="false"
    class="profile-card"
    :class="{ deco: withDeco }"
    :href="`/spaces/space?id=${id}`"
  >
    <v-card class="profile-card" :class="first ? 'first' : ''">
      <div class="inner">
        <div class="card-image-container">
          <img :src="thumbnail" :alt="name" class="card-image">
        </div>
        <div class="properties">
          <h5 class="title">
            {{ name }}
            <v-icon v-if="isVerified" class="verified">mdi-check-decagram</v-icon>
          </h5>
          <v-card-actions class="action">
            <div>
              <p>
                <v-icon class="icon-secondary use-text-secondary-color">mdi-account-group-outline</v-icon>
                <strong>
                  {{ followersCount }}
                  &nbsp;
                  Followers
                </strong>
              </p>
              <p>
                <v-icon class="icon-primary use-text-primary-color">mdi-flag-outline</v-icon>
                <strong>
                  {{ activeCampaignCount }}
                  &nbsp;
                  Active Campaigns
                </strong>
              </p>
            </div>
            <!-- Remove any conditional rendering for 'change' and 'volume' if they're not required -->
          </v-card-actions>
          <!-- Display the token symbol if provided -->
          <h2 v-if="tokenSymbol">
            <i>{{ tokenSymbol }}</i>
          </h2>
        </div>
      </div>
      <v-btn
        @click.prevent="onButtonClick"
        block
        :class="{ 'btn-following': isFollowing, 'btn-not-following': !isFollowing }"
        variant="outlined"
        :color="currentTheme === 'dark' ? 'secondary' : 'primary'"
      >
        Follow
      </v-btn>
    </v-card>
  </a>
</template>

<style scoped lang="scss">
@import './space-card';
.btn-following {
  background-color: black; // 这是当 isFollowing 为 true 时的按钮颜色
  color: white; // 文字颜色
}

.btn-not-following {
  background-color: blue; // 这是当 isFollowing 为 false 时的按钮颜色
  color: black; // 文字颜色
}

.profile-card{
  transition: transform 0.3s ease; /* 平滑动画效果 */
  cursor: pointer; /* 鼠标悬停时的手型图标 */
}

.profile-card:hover {
  transform: translateY(-10px); /* 鼠标悬停时向上移动 */
}

.card-image-container {
  width: 100px;  /* 容器宽度，确保与高度相等 */
  height: 100px; /* 容器高度，确保与宽度相等 */
  border-radius: 50%; /* 使容器呈圆形 */
  overflow: hidden; /* 隐藏溢出的部分 */
  display: flex;
  justify-content: center; /* 水平居中图片 */
  align-items: center; /* 垂直居中图片 */
}

.card-image {
  min-width: 100%; /* 确保图片至少填满容器的宽度 */
  min-height: 100%; /* 确保图片至少填满容器的高度 */
  object-fit: cover; /* 裁剪超出容器部分的图片 */
}
</style>

<script setup>

import { useRouter } from 'vue-router';
const router = useRouter();


const emit = defineEmits(['follow-click']);
const onButtonClick = () => {
  emit('follow-click', id, isFollowing); // 通知父组件，传递id
};


const {
  id, status, name, isVerified,
  thumbnail, followersCount,
  tokenSymbol, href, alias,
  activeCampaignCount, isFollowing
} = defineProps({
  id: {
    type: Number,
    required: true
  },
  status: {
    type: String,
    default: null
  },
  name: {
    type: String,
    required: true,
  },
  isVerified: {
    type: Boolean,
    default: true
  },
  alias: {
    type: String,
    required: true,
  },
  thumbnail: {
    type: String,
    required: true,
  },
  followersCount: {
    type: Number,
    required: true,
  },
  tokenSymbol: {
    type: String,
    default: null
  },
  href: {
    type: String,
    default: '#'
  },
  activeCampaignCount: {
    type: Number,
    required: true,
  },
  isFollowing: {
    type: Boolean,
    required: true,
  },

});
</script>
