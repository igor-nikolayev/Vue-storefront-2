<template>
  <div
    class="sf-product-card"
    :class="{ 'has-colors': colors.length }"
    data-testid="product-card"
  >
    <div class="sf-product-card__image-wrapper">
      <slot
        name="image"
        v-bind="{
          image,
          title,
          link,
          imageHeight,
          imageWidth,
          imageTag,
          nuxtImgConfig,
        }"
      >
        <SfButton
          :link="link"
          class="sf-button--pure sf-product-card__link"
          data-testid="product-link"
          aria-label="Go To Product"
          v-on="$listeners"
        >
          <template v-if="Array.isArray(image)">
            <SfImage
              v-for="(picture, key) in image.slice(0, 2)"
              :key="key"
              class="sf-product-card__picture"
              :src="picture"
              :alt="title"
              :width="imageWidth"
              :height="imageHeight"
              :image-tag="imageTag"
              :nuxt-img-config="nuxtImgConfig"
            />
          </template>
          <SfImage
            v-else
            class="sf-product-card__image"
            :src="image"
            :alt="title"
            :width="imageWidth"
            :height="imageHeight"
            :image-tag="imageTag"
            :nuxt-img-config="nuxtImgConfig"
          />
        </SfButton>
      </slot>
      <slot
        name="colors"
        v-bind="{ colors }"
      >
        <SfColorPicker
          :class="{ 'display-none': colors.length === 0 }"
          class="sf-product-card__colors smartphone-only"
          label="Choose color"
          :is-open="openColorPicker"
          @click:toggle="toggleColorPicker"
        >
          <SfColor
            v-for="(color, i) in colors"
            :key="color.value"
            :color="color.color"
            :selected="color.selected"
            class="sf-product-card__color"
            :class="{ 'display-none': i > 3 && showBadge }"
            @click="handleSelectedColor(i)"
          />
          <SfBadge
            v-if="showBadge"
            class="sf-product-card__colors-badge color-secondary"
          >
            {{ `+${colors.length - 4}` }}
          </SfBadge>
        </SfColorPicker>
        <SfColorPicker
          :class="{ 'display-none': colors.length === 0 }"
          class="sf-product-card__colors desktop-only"
          label="Choose color"
          :is-open="true"
        >
          <SfColor
            v-for="(color, i) in colors"
            :key="color.value"
            :color="color.color"
            :selected="color.selected"
            class="sf-product-card__color"
            :class="{ 'display-none': i > 3 && showBadge }"
            @click="handleSelectedColor(i)"
          />
          <SfBadge
            v-if="showBadge"
            class="sf-product-card__colors-badge color-secondary"
          >
            {{ `+${colors.length - 4}` }}
          </SfBadge>
        </SfColorPicker>
      </slot>
      <slot
        name="badge"
        v-bind="{ badgeLabel, badgeColor }"
      >
        <SfBadge
          class="sf-product-card__badge"
          :class="[badgeColorClass, { 'display-none': !badgeLabel }]"
        >
          {{ badgeLabel }}
        </SfBadge>
      </slot>
    </div>

    <slot
      name="title"
      v-bind="{ title, link }"
    >
      <SfButton
        :link="link"
        class="sf-button--pure sf-product-card__link"
        data-testid="product-link"
        v-on="$listeners"
      >
        <span class="sf-product-card__title">
          {{ title }}
        </span>
      </SfButton>
    </slot>

    <slot
      name="reviews"
      v-bind="{ maxRating, scoreRating }"
    >
      <div
        :class="{ 'display-none': !scoreRating }"
        class="sf-product-card__reviews"
      >
        <SfRating
          v-if="typeof scoreRating === 'number'"
          class="sf-product-card__rating"
          :max="maxRating"
          :score="scoreRating"
        />
        <SfButton
          :class="{ 'display-none': !reviewsCount }"
          :aria-label="`Read ${reviewsCount} reviews about ${title}`"
          class="sf-button--pure sf-product-card__reviews-count"
          data-testid="product-review-button"
          @click="$emit('click:reviews')"
        >
          {{ reviewsCount }} Reviews
        </SfButton>
      </div>
    </slot>
    <slot
      name="price"
      v-bind="{ specialPrice, regularPrice }"
    >
      <SfPrice
        :class="{ 'display-none': !regularPrice }"
        class="sf-product-card__price"
        :regular="regularPrice"
        :special="specialPrice"
      />
    </slot>

    <div class="product-card__actions">
      <slot
        name="add-to-cart"
        v-bind="{
          isAddedToCart,
          showAddedToCartBadge,
          isAddingToCart,
          title,
        }"
      >
        <SfCircleIcon
          class="product-card__add-button button-blue"
          :class="{ 'has-colors': colors.length }"
          :aria-label="`Add to Cart ${title}`"
          :has-badge="showAddedToCartBadge"
          :disabled="addToCartDisabled"
          data-testid="product-add-icon"
          @click="onAddToCart"
        >
          <span>Buy Now</span>
        </SfCircleIcon>
      </slot>

      <SfButton
        :aria-label="`${ariaLabel} ${title}`"
        data-testid="product-wishlist-button"
        class="product-card__add-to-wish"
        @click="toggleIsInWishlist"
      >
        <slot
          name="wishlist-icon"
          v-bind="{ currentWishlistIcon }"
        >
          <SfIcon
            v-if="currentWishlistIcon !== false"
            :icon="currentWishlistIcon"
            size="25px"
            data-test="sf-wishlist-icon"
          />
        </slot>
      </SfButton>
    </div>
  </div>
</template>
<script>
import { colorsValues as SF_COLORS } from '@storefront-ui/shared/variables/colors';
import SfIcon from '@storefront-ui/vue/src/components/atoms/SfIcon/SfIcon.vue';
import SfPrice from '@storefront-ui/vue/src/components/atoms/SfPrice/SfPrice.vue';
import SfRating from '@storefront-ui/vue/src/components/atoms/SfRating/SfRating.vue';
import SfImage from '@storefront-ui/vue/src/components/atoms/SfImage/SfImage.vue';
import SfCircleIcon from '@storefront-ui/vue/src/components/atoms/SfCircleIcon/SfCircleIcon.vue';
import SfBadge from '@storefront-ui/vue/src/components/atoms/SfBadge/SfBadge.vue';
import SfButton from '@storefront-ui/vue/src/components/atoms/SfButton/SfButton.vue';
import SfColorPicker from '@storefront-ui/vue/src/components/molecules/SfColorPicker/SfColorPicker.vue';
import SfColor from '@storefront-ui/vue/src/components/atoms/SfColor/SfColor.vue';

export default {
  name: 'ProductCard',
  components: {
    SfPrice,
    SfRating,
    SfIcon,
    SfImage,
    SfCircleIcon,
    SfBadge,
    SfButton,
    SfColorPicker,
    SfColor,
  },
  props: {
    image: {
      type: [Array, Object, String],
      default: '',
    },
    imageWidth: {
      type: [Number, String],
      default: null,
    },
    imageHeight: {
      type: [Number, String],
      default: null,
    },
    badgeLabel: {
      type: String,
      default: '',
    },
    badgeColor: {
      type: String,
      default: '',
    },
    colors: {
      type: Array,
      default: () => [],
    },
    title: {
      type: String,
      default: '',
    },
    link: {
      type: [String, Object],
      default: null,
    },
    /**
     * Link element tag
     * @deprecated will be removed in 1.0.0 use slot to replace content
     */
    linkTag: {
      type: String,
      default: undefined,
    },
    scoreRating: {
      type: [Number, Boolean],
      default: false,
    },
    reviewsCount: {
      type: [Number, Boolean],
      default: false,
    },
    maxRating: {
      type: [Number, String],
      default: 5,
    },
    regularPrice: {
      type: [Number, String],
      default: null,
    },
    specialPrice: {
      type: [Number, String],
      default: null,
    },
    wishlistIcon: {
      type: [String, Array, Boolean],
      default: 'heart',
    },
    isInWishlistIcon: {
      type: [String, Array],
      default: 'heart_fill',
    },
    isInWishlist: {
      type: Boolean,
      default: false,
    },
    showAddToCartButton: {
      type: Boolean,
      default: false,
    },
    isAddedToCart: {
      type: Boolean,
      default: false,
    },
    addToCartDisabled: {
      type: Boolean,
      default: false,
    },
    imageTag: {
      type: String,
      default: '',
    },
    nuxtImgConfig: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      isAddingToCart: false,
      openColorPicker: false,
    };
  },
  computed: {
    isSFColors() {
      return SF_COLORS.includes(this.badgeColor.trim());
    },
    badgeColorClass() {
      return this.isSFColors ? `${this.badgeColor.trim()}` : '';
    },
    currentWishlistIcon() {
      return this.isInWishlist ? this.isInWishlistIcon : this.wishlistIcon;
    },
    showAddedToCartBadge() {
      return !this.isAddingToCart && this.isAddedToCart;
    },
    ariaLabel() {
      return this.isInWishlist ? 'Remove from wishlist' : 'Add to wishlist';
    },
    wishlistIconClasses() {
      const defaultClass = 'sf-button--pure sf-product-card__wishlist-icon';
      return `${defaultClass} ${this.isInWishlist ? 'on-wishlist' : ''}`;
    },
    showBadge() {
      return this.colors.length > 5;
    },
  },
  methods: {
    toggleIsInWishlist() {
      this.$emit('click:wishlist', !this.isInWishlist);
    },
    onAddToCart(event) {
      event.preventDefault();
      this.isAddingToCart = true;
      setTimeout(() => {
        this.isAddingToCart = false;
      }, 1000);
      this.$emit('click:add-to-cart');
    },
    handleSelectedColor(colorIndex) {
      if (this.colors.length > 0) {
        this.colors.map((color, i) => {
          if (colorIndex === i) {
            this.$emit('click:colors', color);
            this.openColorPicker = false;
          }
        });
      }
    },
    toggleColorPicker() {
      this.openColorPicker = !this.openColorPicker;
    },
  },
};
</script>
<style lang="scss">
@import "~@storefront-ui/shared/styles/components/organisms/SfProductCard.scss";

.product-card {
  &__actions {
    display: none;
    position: absolute;
    width: 100%;
    margin-top: 45px;

    @include for-mobile {
      display: none;
    }
  }

  &__add-button {
    border-radius: 0;
    margin-right: 15px;
    width: calc( 100% - 141px);
    max-width: 250px;
  }

  &__add-to-wish {
    background: transparent;
    border: 1px solid var(--c-primary);
    max-width: 65px;

    .sf-icon {
      --icon-color: var(--c-primary);
    }
  }
}

.sf-product-card {
  width: 100%;
  max-width: 100%;
  padding: 10px 10px 15px;
  border: 1px solid #DEE7F1;
  text-align: center;

  @include for-desktop {
    padding: 30px 30px 35px;
  }

  &__title {
    color: var(--c-text);
    font-size: 12px;
    line-height: 15px;
    text-align: center;
    margin: 10px 0;

    @include for-desktop {
      font-size: 16px;
      line-height: 20px;
      margin: 30px 0 15px;
    }
  }

  &__price {
    display: flex;
    justify-content: center;
    margin: 0;
  }

  .sf-price__regular {
    color: var(--c-text);
    font-weight: var(--font-weight--semibold);
    font-size: 12px;
    line-height: 15px;
    text-align: center;

    @include for-desktop {
      line-height: 20px;
      font-size: 16px;
    }
  }
  .sf-product-card__reviews {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 5px;

    .sf-icon:not(.sf-rating__icon--negative) {
      fill: #FFD953;

      path {
        fill: #FFD953;
      }
    }

    .sf-rating__icon--negative {
      fill: #CDCDCD;
      path {
        fill: #CDCDCD;
      }
    }

    .sf-icon {
      height: 12px;
      width: 12px;

      @include for-desktop {
        height: 15px;
        width: 17px;
      }
    }

    @include for-desktop {
      flex-direction: row;
      justify-content: center;
      margin-bottom: 20px;
    }
  }

  .sf-product-card__reviews-count {
    margin: 5px 0 0 ;
    color: #929298;
    font-size: 10px;
    line-height: 12px;
    font-weight: var(--font-weight--normal);

    @include for-desktop {
      margin-left: 15px;
      font-size: 12px;
      line-height: 15px;
    }
  }
}

.sf-product-card {
  &:hover {
    .product-card__actions {
      display: flex;
    }

    padding-bottom: 140px;
    position: absolute;
    box-shadow: 10px 10px 30px rgba(174, 174, 192, 0.3), -10px -10px 30px rgba(255, 255, 255, 0.4);
  }
}
</style>
