<template>
  <li
    class="glide__slide"
    data-testid="hero-item"
  >
    <component
      :is="wrapper"
      class="sf-hero-item__wrapper hero-item__wrapper"
      :link="link"
    >
      <div class="hero-item__content-wrapper">
        <slot
          name="subtitle"
          v-bind="{ subtitle }"
        >
          <span
            :class="{ 'display-none': !subtitle }"
            class="sf-hero-item__subtitle hero-item__subtitle"
          >{{ subtitle }}</span>
        </slot>
        <slot
          name="title"
          v-bind="{ title }"
        >
          <span
            :class="{ 'display-none': !title }"
            class="sf-hero-item__title hero-item__title"
            v-html="title"
          />
        </slot>
        <slot
          name="description"
          v-bind="{ description }"
        >
          <span
            :class="{ 'display-none': !description }"
            class="sf-hero-item__description hero-item__description"
          >{{
            description
          }}</span>
        </slot>
        <slot
          name="call-to-action"
          v-bind="{ buttonText, link }"
        >
          <div
            v-if="buttonText"
            class="sf-hero-item__button"
          >
            <SfButton
              :link="link"
              data-testid="hero-cta-button"
              class="button-blue"
            >
              {{ buttonText }}
            </SfButton>
          </div>
        </slot>
        <slot name="withImgTag" />
      </div>

      <slot name="background">
        <img
          class="hero-item__background mobile"
          :src="image.mobile"
          alt="hero banner"
        >
      </slot>
    </component>
    <img
      class="hero-item__background desktop"
      :src="image.desktop"
      alt="hero banner"
    >
  </li>
</template>
<script>
import SfButton from '@storefront-ui/vue/src/components/atoms/SfButton/SfButton.vue';
import SfLink from '@storefront-ui/vue/src/components/atoms/SfLink/SfLink.vue';

export default {
  name: 'HeroItem',
  components: {
    SfButton,
    SfLink,
  },
  props: {
    title: {
      type: String,
      default: '',
    },
    subtitle: {
      type: String,
      default: '',
    },
    description: {
      type: String,
      default: '',
    },
    buttonText: {
      type: String,
      default: '',
    },
    background: {
      type: String,
      default: '',
    },
    image: {
      type: [Object, String],
      default: '',
    },
    link: {
      type: String,
      default: null,
    },
  },
  computed: {
    wrapper() {
      return this.link ? 'SfLink' : 'SfButton';
    },
  },
};
</script>

<style scoped lang="scss">

.hero-item {
  &__wrapper {

    @include for-desktop {
      padding: 140px 0 170px 175px;
      z-index: 1;
      background: transparent;
    }

    @include for-mobile {
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #F5F7FB;
      padding: 0;
    }

    span {
      display: block;
    }
  }

  &__content-wrapper {
    width: 100%;
    max-width: 550px;
    z-index: 1;

    @include for-mobile {
      padding: 20px;
    }
  }

  &__description {
    max-width: 475px;
    font-weight: var(--font-weight--normal);
    color: var(--c-text);
    font-size: 12px;
    line-height: 20px;
    text-transform: initial;
    margin-bottom: 20px;

    @include for-desktop {
      font-size: 18px;
      line-height: 28px;
      margin-bottom: 50px;
    }
  }

  &__subtitle {
    font-weight: var(--font-weight--bold);
    color: var(--c-text);
    font-size: 10px;
    line-height: 20px;

    @include for-desktop {
      font-size: 16px;
    }
  }

  &__title {
    font-weight: var(--font-weight--extrabold);
    margin: 5px 0 15px;
    font-size: 30px;
    line-height: 33px;
    width: 100%;

    @include for-desktop {
      line-height: 80px;
      font-size: 70px;
      margin: 10px 0 30px;
    }
  }

  &__background {
    width: 100%;
    max-width: 600px;

    margin: 0;

    &.desktop {
      display: none;
    }

    @include for-desktop {
      position: absolute;
      top: 0;
      left: 0;
      object-fit: cover;
      width: 100%;
      height: 100%;
      max-width: unset;
      z-index: 0;

      &.mobile {
        display: none;
      }

      &.desktop {
        display: block;
      }
    }
  }

}
.sf-hero-item{
  background-image: none;
}

.sf-hero-item__button {
  display: block;
}

.button-blue {
  @include for-mobile {
    margin-bottom: 20px;
    max-width: 100%;
  }
}

</style>

<style lang="scss">

.hero-item {

  &__title{

    span{
      color: var(--c-primary);
    }
  }
}
</style>
