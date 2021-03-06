<template>
  <div class="header-wrapper" id="header">
    <div class="overlay" v-if="headerTransparent" ref="overlay"></div>
    <header ref="header" class="header" :class="{'header-transparent': headerTransparent}">
      <div class="container" ref="headerContainer">
        <div class="row">
          <div class="header__left-nav_bar col-xl-6 col-lg-7">
            <router-link :to="`/`" class="header__logo-icon">
              <headerLogo class="header__header-logo" :isCasePage="isCasePage"/>
            </router-link>
            <nav class="header__header-routes_links">
              <router-link @click.native="goToTopPage" exact to="/" class="header__navigation-link">About</router-link>
              <router-link @click.native="goToTopPage" to="/services/" class="header__navigation-link">Services</router-link>
              <router-link @click.native="goToTopPage" to="/projects/" class="header__navigation-link">Projects</router-link>
              <router-link @click.native="goToTopPage" to="/careers/" class="header__navigation-link">Careers</router-link>
              <router-link @click.native="goToTopPage" to="/blog/" class="header__navigation-link header__navigation-link-blog">Blog</router-link>
            </nav>
          </div>
          <div class="header__right-content col-xl-6 col-lg-5">
            <div class="header__phone-wrapper">
              <span class="header__phone-flag" />
              <a class="header__header-phone" href="tel:+442039848555">+44 20 3984 8555</a>
            </div>
            <UIButtonModalTrigger
              :buttonInnerText="buttonInnerText"
              :modalWindowName="modalWindowName"
              :isRed="true"
            />
          </div>
        </div>
      </div>
    </header>
    <mobileHeader/>
  </div>
</template>

<script>
import UIButtonModalTrigger from '@/components/ui/UIButtonModalTrigger';
import mobileHeader from '@/components/Header/mobile-header';
import headerLogo from '@/components/svg/headerLogo';

export default {
  name: 'main-header',
  components: {
    UIButtonModalTrigger,
    mobileHeader,
    headerLogo
  },
  data() {
    return {
      buttonInnerText: 'Contact me',
      selectedPhone: null,
      modalWindowName: 'contact-me-modal',
      headerTransparent: false,
      scrollTop: null,
      isCasePage: false,
      caseMoreButton: null,
      caseHeader: null,
      logoText: null,
      caseFirstSection: null,
      caseRoot: null
    };
  },
  created() {
    this.setHeaderState();
  },
  mounted() {
    if(this.isCasePage) {
      this.caseHeader = document.getElementsByClassName('case_header')[0];
      this.logoText = document.getElementsByClassName('header-logo-text')[0];

      if (!this.$nuxt.$route.path.includes('/godee')) { // На данный момент верстка главныйх экранов в кейсах отличаеться, и поэтому пришлось через условия разделить логику работы хедера в кейсах 
        this.caseMoreButton = document.getElementsByClassName('case_more__button')[0];
        this.getScrollTop();
        window.addEventListener('scroll', () => this.scrollHandler());
      } else if(this.$nuxt.$route.path.includes('/godee')) {
        this.caseFirstSection = document.getElementsByClassName('case_first-section')[0];
        this.caseRoot = document.getElementsByClassName('main')[0];
        this.caseRoot.addEventListener('scroll', () => this.scrollHandlerGodeeCase());
        this.resizeHandler();
        window.addEventListener('resize', () => this.resizeHandler());
      }
    }
  },
  watch: {
    '$route'() {
      this.setHeaderState();
    }
  },
  methods: {
    setHeaderState() { 
      if (this.$nuxt.$route.path.includes('/case-studies/')) {
        this.headerTransparent = true;
        this.isCasePage = true;
      } else {
        this.headerTransparent = false;
        this.isCasePage = false;
      }
    },
    goToTopPage() {
      window.scrollTo(0, 0);
    },
    getScrollTop() {
      this.scrollTop = this.caseMoreButton.getBoundingClientRect().top - this.$refs.headerContainer.offsetHeight;
    },
    scrollHandler() {
      this.setStylesForHeader();
    },
    scrollHandlerGodeeCase() {
      this.setStylesForHeaderInGoDeeCase();
    },
    setStylesForHeader() { // От этой логики в скором времени можно будет избавиться, сейчас это костыль, так как не все хедеры в кейсах переверстаны под новый формат
      if(this.isCasePage && window.innerWidth > 991) {
        const opacity = 1.6 - (this.$refs.overlay.offsetHeight - (window.scrollY - this.caseHeader.getBoundingClientRect().height + this.caseMoreButton.getBoundingClientRect().height) - this.$refs.headerContainer.offsetHeight) / this.$refs.overlay.offsetHeight;
        const opacityTextLogo = 0.9 - (this.$refs.overlay.offsetHeight - this.caseMoreButton.getBoundingClientRect().top + this.caseMoreButton.getBoundingClientRect().height) / this.$refs.overlay.offsetHeight;
        this.$refs.overlay.style.opacity = opacity;
        this.logoText.style.opacity = opacityTextLogo;
      } else if(this.isCasePage && window.innerWidth < 991) {
        const opacity = 1.7 - (this.$refs.overlay.offsetHeight - (window.scrollY - this.caseHeader.getBoundingClientRect().height + this.caseMoreButton.getBoundingClientRect().height) - 60) / this.$refs.overlay.offsetHeight;
        this.$refs.overlay.style.opacity = opacity;
      }
    },
    setStylesForHeaderInGoDeeCase() {
      if(this.isCasePage && window.innerWidth > 991) {
        this.$refs.overlay.style.opacity = 2 - (this.$refs.overlay.offsetHeight - (this.caseRoot.scrollTop - this.caseHeader.getBoundingClientRect().height) - this.$refs.headerContainer.offsetHeight) / this.$refs.overlay.offsetHeight;
        this.logoText.style.opacity = -1 - (this.$refs.overlay.offsetHeight - this.caseFirstSection.getBoundingClientRect().top) / this.$refs.overlay.offsetHeight;
      } else if (this.isCasePage && window.innerWidth < 991) {
        this.$refs.overlay.style.opacity = 3 - (this.$refs.overlay.offsetHeight - (this.caseRoot.scrollTop - this.caseHeader.getBoundingClientRect().height) - this.$refs.headerContainer.offsetHeight) / this.$refs.overlay.offsetHeight; // Цифры 1 или 2 регулируют старт затемнения, чем больше цифра тем раньше начнеться затемнение, с тектом для логотипа работает в обратную сторону
      }
    },
    resizeHandler() {
      let scrollBarWidth = this.caseRoot.offsetWidth - this.caseRoot.clientWidth;
      if(window.innerWidth > 991) {
        this.$refs.header.style.width = `calc(100% - ${scrollBarWidth}px)` || '100%';
        this.$refs.overlay.style.width = `calc(100% - ${scrollBarWidth}px)` || '100%';
      } else {
        this.$refs.header.style.width = '100%';
        this.$refs.overlay.style.width = '100%';
      }
    }
  }
};
</script>

<style lang="scss" scoped>
  @import '../assets/styles/vars';
  @import '../assets/styles/_flagsIcons';

  .header {
    width: 100%;
    height: 40px;
    padding: 11px 0;
    position: fixed;
    z-index: 2;
    background-color: $bgcolor--black;

    &__header-logo {
      width: 34px;
      height: 58px;
      margin: 5px -33px 0 -65px;
    }

    &__left-nav_bar,
    &__right-content {
      display: flex;
      align-items: center;
    }

    &__right-content {
      justify-content: flex-end;
    }

    &__phone-wrapper,
    &__header-routes_links,
    button {
      margin-bottom: 29px;
    }

    button {
      width: 132px;
      height: 39px;
      border-radius: 4px;
      border-color: $text-color--red-opacity;
    }

    &__navigation-link,
    &__header-phone {
      color: $text-color--white;
    }

    &__navigation-link,
    &__header-phone,
    button {
      font-size: 16px;
      font-family: 'Poppins-Regular', sans-serif;
      text-decoration: none;
      letter-spacing: -0.02em;
    }

    &__navigation-link {
      margin-right: 15px;

      &::after {
        content: '↓';
        font-size: 17px;
        font-family: 'Poppins-Regular', sans-serif;
        color: transparent;
      }
    }

    .nuxt-link-active {
      color: $text-color--red;

      &::after {
        color: $text-color--red;
      }
    }

    &__phone-wrapper {
      display: flex;
      align-items: center;
      margin-right: 40px;

      @media screen and (max-width: 1366px) {
        margin-right: 49px;
      }

      @media screen and (max-width: 1280px) {
        margin-right: 33px;
      }

      @media screen and (max-width: 1024px) {
        margin-right: 20px;
      }
    }

    &__header-phone {
      display: flex;
      align-items: center;
      position: relative;
      margin-left: 7px;
      cursor: pointer;

      img {
        position: absolute;
        top: 4px;
        left: 0;
      }
    }

    &__phone-flag {
      display: block;
      width: 18px;
      height: 14px;
      @include flag-uk;
    }

    @media screen and (max-width: 991px) {
      display: none;
    }
  }

  .header-transparent {
    background-color: transparent !important;
  }

  .overlay {
    width: 100%;
    height: 40px;
    padding: 11px 0;
    position: fixed;
    z-index: 2;
    background-color: $bgcolor--black;
    opacity: 0;
    transition: opacity 0.6s;

    @media screen and (max-width: 991px) {
      height: 48px;
      padding: 0;
    }
  }

  .header-default {
    background-color: $bgcolor--black !important;

    &::before {
      opacity: 0;
    }
  }

  .mobile-menu_is-open {
    width: 100%;
    height: 100%;
    position: fixed;
    padding: initial;
    z-index: 999;
  }

</style>
