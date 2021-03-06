<template>
  <div>
    <nuxt />

    <no-ssr>
      <PopupNoticeOverlay
        :is-show="isShowPopupNoticeOverlay"
        :is-ios-in-app="isIOSInApp"
        @cancel="closePopupNoticeOverlay"
      />
    </no-ssr>
  </div>
</template>

<script>
import popupNoticeOverlayMixin from '~/mixins/popup-notice-overlay';

export default {
  mixins: [popupNoticeOverlayMixin],
  head() {
    return {
      htmlAttrs: {
        'likecoin-embed': undefined,
      },
    };
  },
};
</script>

<style lang="scss">
@import "~assets/css/embed";

$social-media-button-size: normalized(32);

html[likecoin-embed] {
  background-color: transparent !important;

  font-size: $full-width * 1px;

  // Show button border when cursor fall into root element
  &:hover {
    .embed-cta-button-wrapper::before {
      opacity: 1;
    }
  }

  @media screen and (max-width: $full-width * 1px) {
    font-size: 100vw;
  }

  body {
    background-color: transparent !important;
  }

  .likecoin-embed {
    position: relative;

    width: normalized();
    margin-top: normalized($avatar-vertical-offset);

    user-select: none;

    &__badge {
      border-radius: $badge-border-radius;
      background-image: linear-gradient(77deg, $like-light-blue, $like-gradient-1);

      &__content {
        position: relative;

        display: flex;
        align-items: center;

        min-height: normalized($avatar-size - $avatar-vertical-offset * 2);
      }
    }
  }

  .text-content {
    position: relative;

    flex-grow: 1;

    letter-spacing: 0;

    &__subtitle {
      color: $like-gray-5;

      font-size: normalized(16);
      font-weight: 500;
      line-height: normalized(16.5);
    }

    &__title {
      margin-top: normalized(2);

      color: black;

      font-size: normalized(18);
      font-weight: 600;
      line-height: normalized(18.5);
    }
  }

  .embed-cta-button-wrapper {
    position: relative;
    right: 0;

    display: flex;
    flex-direction: column;
    flex-shrink: 0;

    margin-right: normalized(-$button-width / 2);
    padding: normalized($button-border-width);

    &:before {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;

      content: "";
      transition: 0.4s cubic-bezier(.4, 0, .2, 1);
      transition-property: background, opacity;

      opacity: 0;
      border-radius: normalized(25);
      background: linear-gradient(67deg, $like-light-blue, $like-gradient-1);
      box-shadow: 0 normalized(2) normalized($button-shadow-width) 0 rgba(0, 0, 0, 0.25);
    }
  }

  .social-media-connect {
    margin-top: normalized(8);

    > :global(div) {
      justify-content: flex-end;

      min-height: $social-media-button-size;
      margin-left: normalized(216);

      :global(ul) {
        justify-content: flex-end;

        margin: normalized(-4) normalized(-6);

        :global(li) {
          padding: normalized(4) normalized(6);
        }
      }
    }

    :global(.social-media-connect__button) {
      display: block;

      margin: 0;

      :global(svg) {
        width: $social-media-button-size !important;
        height: $social-media-button-size !important;
      }
    }
  }

  footer {
    margin: 0;
    padding: 0;

    border: none;
  }

  button {
    padding: 0;

    border-color:transparent;
    outline-style:none;
    box-shadow:none;
  }
}

#embed-cta-button {
  position: relative;

  display: flex;
  align-items: center;
  flex-direction: column;
  flex-grow: 1;

  width: 100%;
  min-width: 5.4em;
  height: auto;
  min-height: 2.1em;
  margin: 0;

  transition-property: background, color;
  text-align: center;
  text-decoration: none;
  letter-spacing: 0;

  color: white;
  border-radius: 1em;
  background-color: $like-green;
  box-shadow: 0 0.1em 0.3em 0 rgba(0, 0, 0, 0.25);

  font-size: normalized(20);
  line-height: 1em;

  &::before {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;

    content: '';
    transition: opacity 0.2s ease;

    opacity: 0;
    border-radius: inherit;
    background-color: rgba(white, 0.2);
  }

  &:hover {
    &::before {
      opacity: 1;
    }
  }

  &:active {
    box-sizing: border-box;

    color: $like-green;
    border: solid 0.2em $like-green;
    background-color: $like-white;
  }

  .button-content {
    &-wrapper {
      display: flex;
      align-items: center;
      flex-grow: 1;
      justify-content: center;

      padding: 0 0.6em;

      border-radius: inherit;
    }
  }
}
</style>
