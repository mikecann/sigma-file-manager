<!-- SPDX-License-Identifier: GPL-3.0-or-later
License: GNU GPLv3 or later. See the license file in the project root for more information.
Copyright © 2021 - present Aleksey Hoffman. All rights reserved.
-->

<template>
  <div id="home-route" class="main-content-container">
    <overlay-scrollbars
      id="content-area--home-route"
      class="content-area fade-mask--bottom"
      ref='contentAreaHomeRoute'
      :show-background="homeBannerValue"
      :options="{
        className: 'os-theme-minimal-light',
        scrollbars: {
          autoHide: 'move'
        },
        callbacks : {
          onScroll: handleContentAreaScroll
        }
      }"
      :style="{
        '--fade-mask-bottom': '6%'
      }"
    >
      <home-banner :setHomeBannerIsOffscreen="setHomeBannerIsOffscreen"/>

      <!-- content-area -->
      <div id="home-route__content-area--main" class="content-area__main">
        <div class="text--title-1" v-show="!homeBannerValue">
          {{$localize.get('page_home_title')}}
        </div>

        <!-- section:user-dirs -->
        <div class="content-area__section mt-0">
          <div class="text--sub-title-1 mb-3 mt-0">
            {{$localize.get('text_user_directories')}}
          </div>

          <user-dirs-section/>
        </div>

        <!-- section:drives -->
        <div class="content-area__section">
          <div class="text--sub-title-1 mb-3">
            {{locationsTitle}}
          </div>

          <div class="content-area--home-route__section">
            <device-section/>
          </div>
        </div>
      </div>
    </overlay-scrollbars>
  </div>
</template>

<script>
import {mapFields} from 'vuex-map-fields'

export default {
  name: 'home',
  activated () {
    this.$store.dispatch('routeOnActivated', this.$route.name)
    const video = document.querySelector('#media-banner__video')
    const videoGlow = document.querySelector('#media-banner__glow')
    if (video) { video.play() }
    if (videoGlow) { videoGlow.play() }
  },
  mounted () {
    this.$store.dispatch('routeOnMounted', this.$route.name)
  },
  computed: {
    ...mapFields({
      homeBannerValue: 'storageData.settings.homeBanner.value',
      homeBannerIsOffscreen: 'homeBannerIsOffscreen',
    }),
    locationsTitle () {
      if (process.platform === 'win32') {
        return this.$localize.get('text_drives')
      }
      else {
        return this.$localize.get('locations')
      }
    }
  },
  methods: {
    handleContentAreaScroll () {
      this.setHomeBannerIsOffscreen()
    },
    setHomeBannerIsOffscreen () {
      const container = this.$refs.contentAreaHomeRoute
      const scrollContainer = container.$el.querySelector('.os-viewport')
      const containerScrollTop = scrollContainer.scrollTop
      const clientHeight = scrollContainer.clientHeight
      const bannerHeightPerc = 0.5
      const offscreenOffset = 72
      const bannerSize = clientHeight * bannerHeightPerc
      const bannerIsAlmostOffScreen = bannerSize - containerScrollTop <= offscreenOffset
      this.homeBannerIsOffscreen = !bannerIsAlmostOffScreen
    }
  }
}
</script>

<style>
.action-toolbar__icon[home-banner-value] {
  color: var(--color-4) !important;
}

#home-route
  .content-area:not([show-background]) {
    padding: 0px;
    height: calc(
      100vh
      - var(--window-toolbar-height)
      - var(--action-toolbar-height)
    );
    overflow-y: overlay;
    user-select: none;
  }

#home-route
  .content-area[show-background] {
    padding: 0px;
    height: 100vh;
    margin-top: calc(-1 * var(--header-height));
    overflow-y: overlay;
    user-select: none;
  }

#home-route
  .content-area
    .content-area__main {
      padding: 8px 24px;
      user-select: none;
      background-color: rgba(29, 31, 37, 0);
    }

#home-route
  .content-area[show-background]
    .content-area__main {
      padding: 16px 24px;
      user-select: none;
      background-color: rgba(29, 31, 37, 0);
    }

#home-route
  .content-area__section {
    margin-top: 8px;
    margin-bottom: 16px;
  }
</style>
