<template>
  <v-container class="root">
    <v-row :class="{ 'spacing-4': !isMobile }" class="mt-md-10">


            <div
              data-aos="fade-left"
              data-aos-offset="100"
              data-aos-delay="1100"
              data-aos-duration="500"
            >
              <arrow-button style="width: 100%;" color="primary" :href="href" :text="'Twitter'" @click="tweetFunc"/>
            </div>


    </v-row>
  </v-container>
</template>

<style lang="scss" scoped>
@import './services-style.scss';
</style>

<script setup>
import { onMounted } from 'vue';
import { useDisplay } from 'vuetify';
import AOS from 'aos';
import imgAPI from '@/assets/images/imgAPI';
import link from '@/assets/text/link';
import ArrowButton from './Standard';
import SingleCard from '../../Cards/Apps/SingleCard';
import TitleMain from '../../Title';

const { smAndDown: isMobile } = useDisplay();
const { sm: isTablet } = useDisplay();

onMounted(() => {
  AOS.init({
    once: true,
  });
});

const {tweetContent, href} = defineProps({
  tweetContent: {
    type: String,
    required: true,
  },
  href: {
    type: String,
    default: '#',
  },
})

function tweetFunc() {
  const tweetUrl = `https://twitter.com/intent/tweet?text=${encodeURIComponent(tweetContent)}`;
  const windowOptions = 'width=400,height=500,resizable=yes,scrollbars=yes';
  window.open(tweetUrl, '_blank', windowOptions);
}

</script>
