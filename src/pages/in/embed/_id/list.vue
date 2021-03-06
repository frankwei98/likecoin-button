<template>
  <div class="likee-list-page">
    <div class="lc-container">
      <like-form>
        <template
          v-if="shouldShowBackButton"
          slot="header-left"
        >
          <a
            @click="$router.go(-1)"
            href="#"
          >
            {{ $t('general.back') }}
          </a>
        </template>

        <template slot="header-right">
          <a
            href="https://like.co/"
            rel="noopener noreferrer"
            target="_blank"
          >
            {{ $t('LikeButton.button.aboutLikeCoin') }}
          </a>
        </template>

        <span class="likee-list-page__content">
          {{ $t('Embed.label.numLikesForArticle', {
            numOfLikees: likees.length,
            numOfLikes,
          }) }}
          <span v-if="title">— "{{ title }}"</span>
        </span>

        <div
          :class="['likee-list-page__list', { expand: isShowAll }]"
          :style="{ maxHeight: `${Math.ceil(likees.length / 2) * 89}px` }"
        >

          <user-avatar
            v-for="(likee, index) in likees"
            :key="index"
            :user="likee"
          />

          <transition name="lc-transition-default">
            <div
              v-if="!isShowAll"
              class="overflow-overlay"
            />
          </transition>
        </div>

        <div
          class="likee-list-page__show-more-btn-wrapper"
        >
          <button
            v-if="!isShowAll"
            @click="isShowAll = true"
          >
            {{ $t('Embed.button.showMore') }}
          </button>
        </div>
      </like-form>
    </div>
  </div>
</template>


<script>
import Vue from 'vue'; // eslint-disable-line import/no-extraneous-dependencies

import LikeForm from '~/components/LikeForm';
import UserAvatar from '~/components/UserAvatar';

import {
  apiGetUserMinById,
  apiGetLikeButtonLikerList,
  apiGetLikeButtonTotalCount,
  apiGetPageTitle,
} from '@/util/api/api';
import { checkValidDomainNotIP, handleQueryStringInUrl } from '@/util/url';

export default {
  name: 'embed-id-list',
  layout: 'narrowWithHeader',
  components: {
    LikeForm,
    UserAvatar,
  },
  async asyncData({ params, query }) {
    const referrer = handleQueryStringInUrl(query.referrer);
    const promises = [
      apiGetLikeButtonLikerList(params.id, referrer),
      apiGetLikeButtonTotalCount(params.id, referrer),
    ];
    if (referrer) {
      const url = encodeURI(referrer);
      /* Try to get html to fetch title below */
      if (checkValidDomainNotIP(url)) {
        promises.push(apiGetPageTitle(referrer));
      }
    }
    const [
      { data: likees },
      { data: totalData },
      title,
    ] = await Promise.all(promises);
    return {
      title,
      isShowAll: likees.length <= 8,
      numOfLikes: totalData.total,
      likees: likees.map(id => ({ id })),
    };
  },
  data() {
    return {
      isShowAll: false,
    };
  },
  computed: {
    urlReferrer() {
      const { query } = this.$route;
      let { referrer = '' } = query;
      if (referrer) {
        referrer = handleQueryStringInUrl(referrer);
      }
      return referrer;
    },
    referrer() {
      return this.urlReferrer;
    },
    shouldShowBackButton() {
      return this.$route.query.show_back === '1';
    },
  },
  created() {
    if (process.client) {
      this.fetchList();
    }
  },
  methods: {
    async fetchList() {
      this.likees.forEach(async (r) => {
        try {
          const { data } = await apiGetUserMinById(r.id);
          Vue.set(r, 'avatar', data.avatar);
          Vue.set(r, 'displayName', data.displayName);
          Vue.set(r, 'isPreRegCivicLiker', data.isPreRegCivicLiker);
          Vue.set(r, 'isSubscribedCivicLiker', data.isSubscribedCivicLiker);
          Vue.set(r, 'civicLikerSince', data.civicLikerSince);
        } catch (err) {
          console.error(err);
        }
      });
    },
  },
};
</script>


<style lang="scss" scoped>
@import "~assets/css/variables";
@import "~assets/css/mixin";

$user-avatar-image-size: 48px;

.likee-list-page {
  .user-avatar {
    min-height: 72px;
    padding: 16px 8px;

    border-bottom: 1px solid #e6e6e6;
  }

  &__content {
    color: $like-dark-brown-2;

    font-size: 20px;
    font-weight: 300;
  }

  &__list {
    position: relative;

    display: flex;
    overflow: hidden;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-between;

    margin-top: 8px;

    transition: max-height 0.25s ease-in-out;

    &:not(.expand) {
      max-height: 272px !important;
    }

    > * {
      width: calc(50% - 16px);
    }

    .overflow-overlay {
      position: absolute;
      bottom: 0;
      left: 0;

      width: 100%;
      height: calc(100% - 226px);

      background-image: linear-gradient(to bottom, rgba(247, 247, 247, 0), $like-gray-1);
    }
  }

  &__show-more-btn-wrapper {
    margin-top: 12px;

    text-align: center;

    button {
      padding: 8px 10px;

      cursor: pointer;
      transition: background-color 0.2s ease-in-out;
      text-decoration: underline;

      color: $like-green;
      border: none;
      border-radius: 2px;
      outline:none;
      background-color: transparent;

      font-size: 14px;

      &:hover {
        background-color: $like-gray-3;
      }

      &:active {
        background-color: $gray-9b;
      }
    }
  }
}
</style>
