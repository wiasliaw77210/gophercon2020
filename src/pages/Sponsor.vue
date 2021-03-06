<template>
  <div id="sponsor" class="container">
    <LogoTop v-if="$store.state.app.device !== 'mobile'"/>
    <div class="background-image">
      <div class="image-wrapper">
        <img
          src="https://gophercon.golang.tw/2020/img/subpage-bg.png"
          srcset="https://gophercon.golang.tw/2020/img/subpage-bg@2x.png 2x, https://gophercon.golang.tw/2020/img/subpage-bg@3x.png 3x"
          class="Group-9"
        />
        <img
          src="https://gophercon.golang.tw/2020/img/graphic-sponsor-1.png"
          srcset="https://gophercon.golang.tw/2020/img/graphic-sponsor-1@2x.png 2x, https://gophercon.golang.tw/2020/img/graphic-sponsor-1@3x.png 3x"
          class="sponsor-1"
        />
        <img
          src="https://gophercon.golang.tw/2020/img/graphic-sponsor-2.png"
          srcset="https://gophercon.golang.tw/2020/img/graphic-sponsor-2@2x.png 2x, https://gophercon.golang.tw/2020/img/graphic-sponsor-2@3x.png 3x"
          class="sponsor-2"
        />
      </div>
    </div>
    <div class="content">
      <div class="sponsor-wrapper">
        <div v-for="order in sponsorOrderText" :key="order" class="sponsor-card">
          <h2 class="sponsor-level">
            <span class="sponsor-text">{{ sponsorLevelText[order] }}</span>
          </h2>
          <!-- image list -->
          <div class="sponsor-card">
            <div
              class="sponsor-card-content"
              style="display: flex; justify-content: flex-start; align-items: center;"
            >
              <div
                v-for="sponsor in filterSponsor(order)"
                :key="sponsor.slug"
                v-on:click="handleClick(order, sponsor)"
                class="sponsor-img-container"
              >
                <img
                  :alt="sponsor.name"
                  :id="`img-${sponsor.name}`"
                  :src="`https://gophercon.golang.tw/2020/img/sponsors/${sponsor.image}`"
                />
              </div>
            </div>
          </div>
          <!-- display card -->
          <div
            :id="`${order}`"
            class="sponsor-card-content"
            style="background-color: #f0f2f4;padding: 16px;"
          >
            <a :href="state.url" target="_blank" rel="noopener">
              <span>{{ state.name }}</span>
            </a>
            <div class="sponsor-text-container layout-flex-md-50" v-if="state.description">
              <p class="sponsor-text" v-html="state.description"></p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import { Getter } from 'vuex-class';
import _ from 'lodash';
import { markdown } from 'markdown';

import { DeviceType } from '@/store/types/app';

import sponsorData from '@/../public/json/sponsor.json';

import LogoTop from '../components/LogoTop.vue';
import Sponsor from '../components/Team/Sponsor.vue';

interface SponsorData {
  name: string;
  slug: string;
  image: string;
  description: string;
  url?: string;
  readmore: boolean;
}
import { Route } from 'vue-router';

@Component({
  components: {
    LogoTop,
    Sponsor
  }
})
export default class SponsorPage extends Vue {
  private sponsorOrderText = [
    'level-1',
    'level-2',
    'level-3',
    'co-organizer',
    'thank',
  ];

  private sponsorLevelText = {
    holder: '主辦單位',
    'co-holder': '共同主辦',
    'co-organizer': '協辦單位',
    'level-1': '鑽石級',
    'level-2': '金級',
    'level-3': '銀級',
    thank: '特別感謝',
    media: '媒體夥伴'
  };

  private sponsorColorText: Record<string, string> = {
    'level-1': '#b3f7ff',
    'level-2': '#ffb976',
    'level-3': '#dddfe2',
    'co-organizer': '#61c3ff',
    thank: '#bbfad7',
  };

  private sponsorList: object = {};

  private state: SponsorData = {
    name: '',
    slug: '',
    image: '',
    description: '',
    readmore: false,
  };

  public mounted() {
    this.processSponsor();
  }

  private processSponsor(): void {
    this.sponsorList = sponsorData;
  }

  private markdownParser(rawContent: string): string {
    return markdown.toHTML(rawContent);
  }

  private filterSponsor(level: string): any {
    return sponsorData.filter(sponsor => sponsor.level === level);
  }

  private handleClick(order: string, sponsor: any): void {
    // displat sponsor content
    this.sponsorOrderText.forEach(text => {
      const contentDisplayer = document.querySelector<HTMLDivElement>(`#${text}`);
      if (contentDisplayer && text !== order) {
        contentDisplayer.style.display = 'none';
      } else if (contentDisplayer && text === order) {
        contentDisplayer.style.display = 'block';
        this.state = sponsor;
      }
    });
    // display border-top
    (this.sponsorList as Array<any>).forEach(s => {
      const sponsorImg = document.querySelector<HTMLImageElement>(`#img-${s.name}`);
      if (sponsor.name !== s.name && sponsorImg) {
        sponsorImg.style.borderTop = 'transparent';
      } else if (sponsor.name === s.name && sponsorImg) {
        sponsorImg.style.borderTop = `3px solid ${this.sponsorColorText[order]}`;
        sponsorImg.style.borderRadius = '5px';
      }
    });
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/scss/mixin.scss';
@import '@/assets/scss/variables.scss';

$logo-margin-bottom: 20px;

#sponsor {
  min-width: 375px;
  width: 100%;

  @include for-size(xs) {
    padding-top: calc(
      #{$navbar-height-mobile} + 36px + 84px + #{$logo-margin-bottom}
    );
  }
  @include for-size(sm) {
    padding-top: calc(
      #{$navbar-height-mobile} + 36px + 100px + #{$logo-margin-bottom}
    );
  }
  @include for-size(md) {
    padding-top: calc(
      #{$navbar-height-mobile} + 36px + 153px + #{$logo-margin-bottom}
    );
  }
  @include for-size(lg) {
    padding-top: calc(#{$navbar-height} + 28px + 153px + 20px);
  }
  @include for-size(xl) {
    padding-top: calc(#{$navbar-height} - 32px + 153px + 20px);
  }

  .background-image {
    position: fixed;
    background-color: #fff;
    height: calc(100vw / 16 * 9);
    width: 100vw;
    top: 0;
    left: 0;
    z-index: 0;
    transform-origin: 0 0;
    margin-top: $navbar-height-mobile;
    z-index: -10;

    @include for-size(lg) {
      margin-top: $navbar-height;
    }

    .image-wrapper {
      position: relative;
    }

    .Group-9 {
      &-container {
        position: relative;
        width: 100%;
        height: 100%;
      }

      width: 100%;
      height: auto;
    }

    .sponsor-1 {
      position: absolute;
      left: 0;
      bottom: 0;
    }

    .sponsor-2 {
      position: absolute;
      left: 5%;
      bottom: 45%;
    }
  }

  .content {
    margin-left: auto;
    margin-right: auto;
    padding-bottom: 40px;

    @include for-size(md) {
      width: 688px;
    }
    @include for-size(lg) {
      width: 716px;
    }
    @include for-size(xl) {
      width: 716px;
    }
  }
}

.card-container.individual-sponsor {
  font-family: SourceHanSansTW;
  margin-bottom: 40px;

  @include for-size(xs) {
    padding: 0 16px;
  }
  @include for-size(sm) {
    padding: 0 16px;
  }
  @include for-size(md) {
    padding: 0 68px;
  }
  @include for-size(lg) {
    padding: 0 82px;
  }
  @include for-size(xl) {
    padding: 0 82px;
  }

  h2.subtitle {
    margin-bottom: 24px;
    text-align: center;
    font-size: 24px;
    font-weight: 900;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: normal;
    color: #44484f;
  }

  p {
    margin-bottom: 20px;
    font-size: 18px;
    font-weight: normal;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: normal;
    color: #343942;
  }
}

.sponsor-wrapper {
  background-color: #f7f8f9;
  padding-top: 40px;
  padding-bottom: 40px;

  @include for-size(xs) {
    margin-left: 16px;
    margin-right: 16px;
    padding-left: 24px;
    padding-right: 24px;
  }
  @include for-size(sm) {
    margin-left: 16px;
    margin-right: 16px;
    padding-left: 24px;
    padding-right: 24px;
  }
  @include for-size(md) {
    margin-left: 40px;
    margin-right: 40px;
    padding-left: 68px;
    padding-right: 68px;
  }
  @include for-size(lg) {
    margin-left: 0px;
    margin-right: 0px;
    padding-left: 82px;
    padding-right: 82px;
  }
  @include for-size(xl) {
    padding-left: 82px;
    padding-right: 82px;
  }

  .sponsor-card {
    margin-top: 16px;
  }

  .sponsor-card-content {
    display: none;
  }

  h2.sponsor-name {
    margin-bottom: 24px;
  }

  span {
    font-weight: 900;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: normal;
    color: #7bd6eb;
    &.sponsor-level {
      font-size: 14px;
    }
  }

  .sponsor-img-container {
    cursor: pointer;
    img {
      width: 100px;
      max-width: 100px;
      margin-right: 4px;
    }
  }

  p.sponsor-text {
    font-size: 14px;
    font-weight: normal;
    font-stretch: normal;
    font-style: normal;
    line-height: 1.71;
    letter-spacing: normal;
    color: #333943;
  }
}
</style>
