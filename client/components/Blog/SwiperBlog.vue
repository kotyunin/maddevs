<template>
  <div class="slider-content">
    <div class="swiper-container thumb-example" :class="{'box-shadow': boxShadow}">
      <swiper class="swiper gallery-top" :options="swiperOptionTop" ref="swiperTop">
        <swiper-slide v-for="(element, index) in components" :key="index">
          <prismic-image :field="element.gallery_image"/>
        </swiper-slide>
      </swiper>
    </div>
    <p v-if="sliderDescription !== ''" class="slider-description">
      {{sliderDescription}}
    </p>
    <swiper class="swiper gallery-thumbs" :options="swiperOptionThumbs" ref="swiperThumbs">
      <swiper-slide v-for="element in components" :key="element.fileName">
        <prismic-image :field="element.gallery_image.Mobile" v-if="element.gallery_image.Mobile"/>
      </swiper-slide>
    </swiper>
  </div>
</template>

<script>
import {Swiper, SwiperSlide} from 'vue-awesome-swiper';
import 'swiper/css/swiper.css';

export default {
  name: 'SwiperBlog',
  title: 'SwiperBlog',
  components: {
    Swiper,
    SwiperSlide
  },
  data() {
    return {
      swiperOptionTop: {
        loop: false,
        loopedSlides: 0, // looped slides should be the same
        spaceBetween: 10,
        grabCursor: true
      },
      swiperOptionThumbs: {
        loop: false,
        loopedSlides: 0, // looped slides should be the same
        spaceBetween: 8,
        centeredSlides: true,
        slidesPerView: 'auto',
        touchRatio: 0.1,
        slideToClickedSlide: true
      }
    };
  },
  mounted() {
    this.$nextTick(() => {
      const swiperTop = this.$refs.swiperTop.$swiper;
      const swiperThumbs = this.$refs.swiperThumbs.$swiper;
      swiperTop.controller.control = swiperThumbs;
      swiperThumbs.controller.control = swiperTop;
    });
  },
  props: {
    components: {
      type: Array,
      required: true
    },
    boxShadow: {
      type: Boolean,
      default: false
    }
  }
};
</script>

<style lang="scss">
  @import '../../assets/styles/cases/icons';
  @import '../../assets/styles/cases/mixins';

  .thumb-example {
    img {
      display: block;
    }
  }

  .box-shadow {
    box-shadow: 0 2px 7px rgba(0, 0, 0, 0.05), 0 5.47001px 41.35px rgba(0, 0, 0, 0.1);
  }

  .swiper {
    /deep/ img {
      max-width: 100%;
      height: auto;
    }

    &.gallery-thumbs {
      box-sizing: border-box;
      padding: 8px 0;
    }

    &.gallery-thumbs .swiper-slide {
      width: 73px;
      height: 44px;
      opacity: 0.2;
      cursor: pointer;
    }

    &.gallery-thumbs .swiper-slide-active {
      opacity: 1;
    }
  }

  .slider-description {
    margin: 9px 0;
    padding: 0 24px;
    text-align: center;
    @include default_text($text-color--grey-img-description, 13px, 21.58px, -0.02em, normal);
  }
</style>
