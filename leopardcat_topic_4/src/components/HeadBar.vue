<template>
  <div
    :class="{
      'head-bar-container': true,
      'fold': webFoldFlag,
    }"
  >
    <div class="logo-container">
      <div class="logo-wrapper" @click="sendGA_Logo('聯logo')">
        <a href="https://udn.com/upf/newmedia/2019_data/leopardcat/">
          <i class="udn-icon udn-icon-logo"></i>
        </a>
      </div>
    </div>
    <div 
      class="links-wrapper"
    >
      <div v-for="(item, index) in outLinkList" :key="index"
        class="link-button"
        @click="sendGA(item.name)"
      >
        <a v-if="index === 4" :href="item.link" style="color: #464646; font-weight: bold;" target="_blank">{{item.name}}</a>
        <a v-if="index !== 4" :href="item.link" target="_blank">{{item.name}}</a>
      </div>
    </div>
    <slot name="anchor"></slot>
    <div
      class="links-mob-toggle-wrapper"
      @click="burgerClick"
    >
      <div class="links-mob-toggle">
        <input type="checkbox"/>
        <span></span>
        <span></span>
        <span></span>
      </div>
    </div>
    <div
      class="links-mob-container"
      :style="{
        transform: 'translateX(' + headBarTraslation + 'vw)',
      }">
      <div class="link-mob-button-wrapper">
        <div v-for="(item, index) in outLinkList" :key="index"
          class="link-mob-button"
          @click="sendGA(item.name)"
        >

          <a :href="item.link" target="_blank">{{item.name}}</a>
          <hr>
        </div>
      </div>
      <div class="share-wrapper">
        <div>分享</div>
        <div class="share-btn" @click="shareFacebook(projectHref)">
          <a>
            <img src="../assets/share_fb.svg" alt="">
          </a>
        </div>
        <div class="share-btn" @click="shareLine">
          <a>
            <img src="../assets/share_line.svg" alt="">
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'

export default {
  name: 'HeadBar',
  props: {
    outLinkList: {
      type: Array,
    },
    projectHref: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      burgerFlag: false,
      headBarTraslation: 100,
      webFoldFlag:  true,
      lastPageYOffset: 0,
    };
  },
  methods: {
    sendGA(target) {
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "headBar",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [" + target + "] [page click]"
      })
    },
    sendGA_Logo(target) {
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "headBar",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [" + target + "] [logo click]"
      })
    },
    shareFacebook() {
      window.FB.ui({
        method: 'share_open_graph',
        action_type: 'og.shares',
        action_properties: JSON.stringify({
          object: {
            'og:url': 'https://udn.com/upf/newmedia/2019_data/leopardcat/topic4/',
            'og:title': '【誰殺了石虎之四】設動物生態廊道 給石虎一條安全回家的路',
            'og:description': '專家預測未來100年內，石虎可能走上滅絕之路，從此消失在台灣山林。如何拯救石虎？專家提出動物生態廊道、增加警告標誌、架設動物防護網、夜間放慢車速等4大招，結合官方與民間力量，保護石虎安全回家。',
            'og:image': 'https://udn.com/upf/newmedia/2019_data/leopardcat/topic4/fb_meta.jpg',
          },
        })
      })

      window.ga('newmedia.send', {
        hitType: 'event',
        eventCategory: 'share',
        eventAction: 'click',
        eventLabel: `[${Utils.detectPlatform()}] [${document.querySelector('title').innerHTML}] [特製fb icon] [fb share]`,
      });
    },
    shareLine() {
      if (Utils.detectMob()) {
        // 手機
        window.open(`https://line.me/R/msg/text/?"${document.querySelector('title').innerHTML}%0D%0A%0D%0A${document.querySelector('meta[property="og:description"]').content}%0D%0A%0D%0A${this.projectHref}`);
      } else {
        window.open(`https://lineit.line.me/share/ui?url=${this.projectHref}`);
      }
      window.ga('newmedia.send', {
        hitType: 'event',
        eventCategory: 'share',
        eventAction: 'click',
        eventLabel: '[' + Utils.detectPlatform() + '] [' + document.querySelector('title').innerHTML + '] [特製line icon] [line share]'
      });
    },
    handleScroll() {
      if (window.pageYOffset === 0 && this.headBarTraslation === 100) {
        this.webFoldFlag = true;
      } else if (this.lastPageYOffset > pageYOffset && !this.burgerFlag) {
        this.webFoldFlag = true;
      } else {  
        this.webFoldFlag = false;
      }
      this.lastPageYOffset = window.pageYOffset;
    },
    burgerClick() {
      if (this.burgerFlag) {
        this.headBarTraslation = 100;
        this.burgerFlag = !this.burgerFlag;
      } else {
        this.headBarTraslation = 0;
        this.burgerFlag = !this.burgerFlag;
      }
    },
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);
  },
  destroyed() {
    window.removeEventListener('scroll', this.handleScroll);
  },
};
</script>

<style lang="scss" scoped>
  .head-bar-container {
    position: fixed;
    z-index: 9998;
    width: 100%;
    height: 58px;
    background: #ffffff;
    display: flex;
    justify-content: space-between;
    transition: transform .333s ease-in-out;

    a {
      text-decoration: none;
    }
  }

  .fold {
    transform: translateY(-58px);
    transition: transform .333s ease-in-out;
  }

  .logo-container {
    position: relative;
    height: 100%;
    width: 58px;
    .logo-wrapper {
      height: 100%;
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 36px;
      i {
        color: #000000;
        transform: rotate(0deg);
        &:hover {
          transition: transform 288ms ease-in;
          transform: rotate(16deg);
        }
      }
    }
  }
  .links-wrapper {
    @media screen and (min-width: 1024px) {
      display: flex;
    }
    display: none;
    position: relative;
    height: 100%;
    width: 100%;
    justify-content: flex-end;
    .link-button {
      height: 100%;
      float: left;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 15px;
      font-size: 20px;
      a {
        color: #acacac;
        transition: 288ms ease-in;
        &:hover {
          color: #464646;
        }
      }
    }
  }
  .links-mob-toggle-wrapper {
    @media screen and (min-width: 1024px){
      display: none;
    }
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: flex-end;
    padding: 10px;

    .links-mob-toggle {
      position: relative;
      z-index: 1;     
      -webkit-user-select: none;
      user-select: none;

      input {
        display: block;
        width: 40px;
        height: 32px;
        position: absolute;
        top: -7px;
        left: -5px;
        cursor: pointer;
        opacity: 0; /* hide this */
        z-index: 2; /* and place it over the hamburger */
        -webkit-touch-callout: none;
      }

      span {
        position: relative;
        z-index: 1;
        display: block;
        width: 30px;
        height: 4px;
        margin-bottom: 5px; 
        background: #000000;
        transform-origin: 4px 0px;
        transition: transform 0.5s cubic-bezier(0.77,0.2,0.05,1.0),
                    background 0.5s cubic-bezier(0.77,0.2,0.05,1.0),
                    opacity 0.55s ease;
      }

      span:first-child {
        transform-origin: 0% 0%;
      }

      span:nth-last-child(2) {
        transform-origin: 0% 50%;
      }
      input:checked ~ span {
        opacity: 1;
        margin-bottom: 0;
        margin-right: 5px;
        transform: rotate(45deg) translate(-0%, -381%);
        background: #000000;
      }
      input:checked ~ span:nth-last-child(2) {
        transform: rotate(-45deg) translate(-0%, 381%);
      }
      input:checked ~ span:nth-last-child(3) {
        opacity: 0;
        transform: rotate(0deg) scale(0.2, 0.2);
      }
      input:checked ~ ul {
        transform: none;
      }
    }
  }
  .links-mob-container {
    @media screen and (min-width: 1024px){
      display: none;
    }
    position: absolute;
    top: 58px;
    right: 0;
    width: 100vw;
    height: calc(100vh - 58px);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    padding: 5%;
    box-sizing: border-box;
    background-color: #fff;
    transition: transform .666s ease-in-out;

    .link-mob-button {
      font-size: 24px;
      text-align: center;
      a {
        color: #acacac;
        transition: 288ms ease-in;
      }
    }
    .share-wrapper {
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 10%;

      .share-btn {
        margin: 10px;
      }
    }
  }

</style>

