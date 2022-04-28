<template>
  <SfSection
    :title-heading="title"
    :subtitle-heading="subtitle"
    class="section product-list"
  >
    <SfLoader
      :class="{ loading }"
      :loading="loading"
    >
      <div class="product-list__wrapper">
        <div
          v-for="(product, i) in mappedProducts"
          :key="i"
          class="product-list__item"
        >
          <ProductCard
            image-tag="nuxt-img"
            :title="productGetters.getName(product)"
            :image-width="imageSizes.productCard.width"
            :image-height="imageSizes.productCard.height"
            :image="
              getMagentoImage(productGetters.getProductThumbnailImage(product))
            "
            :regular-price="$fc(productGetters.getPrice(product).regular)"
            :special-price="
              productGetters.getPrice(product).special &&
                $fc(productGetters.getPrice(product).special)
            "
            :link="
              localePath(
                `/p/${productGetters.getProductSku(
                  product
                )}${productGetters.getSlug(product, product.categories[0])}`
              )
            "
            :max-rating="5"
            :score-rating="productGetters.getAverageRating(product)"
            :reviews-count="productGetters.getTotalReviews(product)"
            :is-in-wishlist="isInWishlist({ product })"
            :is-added-to-cart="isInCart({ product })"
            :show-add-to-cart-button="true"
            :wishlist-icon="'heart'"
            :is-in-wishlist-icon="'heart_fill'"
            @click:wishlist="addItemToWishlist(product)"
            @click:add-to-cart="addItemToCart({ product, quantity: 1 })"
          />
        </div>
      </div>
    </SfLoader>

    <SfButton
      :link="link"
      class="sf-button--pure button-blue product-list__button"

      v-on="$listeners"
    >
      {{ linkText }}
    </SfButton>
  </SfSection>
</template>

<script>
import {
  SfSection,
  SfLoader,
  SfButton,
} from '@storefront-ui/vue';

import { computed, defineComponent } from '@nuxtjs/composition-api';
import { productGetters } from '~/getters';
import { useAddToCart } from '~/helpers/cart/addToCart';
import { useImage, useWishlist, useUser } from '~/composables';
import ProductCard from '~/components/Custom/HomeProduct/ProductCard';

export default defineComponent({
  name: 'ProductsCarousel',
  components: {
    ProductCard,
    SfSection,
    SfLoader,
    SfButton,
  },
  props: {
    title: {
      type: String,
      required: false,
      default: '',
    },
    subtitle: {
      type: String,
      required: false,
      default: '',
    },
    products: {
      type: Array,
      required: false,
      default: () => [],
    },
    link: {
      type: String,
      required: false,
      default: '',
    },
    linkText: {
      type: String,
      required: false,
      default: '',
    },
    loading: Boolean,
  },
  setup(props) {
    const { isAuthenticated } = useUser();
    const { isInWishlist, addItem, removeItem } = useWishlist();
    const { addItemToCart, isInCart } = useAddToCart();

    const mappedProducts = computed(() => props.products.map((product) => ({
      // @ts-ignore
      ...product,
      isInWishlist: isInWishlist({ product }),
    })));

    const addItemToWishlist = async (product) => {
      await (isInWishlist({ product })
        ? removeItem({ product })
        : addItem({ product }));
    };

    const { getMagentoImage, imageSizes } = useImage();

    return {
      addItemToCart,
      addItemToWishlist,
      isAuthenticated,
      isInCart,
      isInWishlist,
      mappedProducts,
      productGetters,
      getMagentoImage,
      imageSizes,
    };
  },
});
</script>

<style lang="scss" scoped>
::v-deep {
  .sf-section__content {
    margin-top: 20px;

    @include for-desktop {
      margin-top: 60px;
    }
  }

  .sf-heading__title {
    font-weight: var(--font-weight--bold);

    @include for-mobile {
      font-size: 25px;
      line-height: 30px;
    }
  }

  .sf-heading__description{
    margin-top: 10px;
    font-size: 14px;
    line-height: 24px;

    @include for-desktop {
      font-size: 18px;
      line-height: 28px;
    }
  }
}

.product-list {
  &__wrapper {
    display: flex;
    flex-wrap: wrap;
  }

  &__item {
    width: 50%;
    position: relative;

    @include for-desktop {
      width: 25%;
    }
  }

  &__button {
    width: 290px;
    display: block;
    margin: 25px auto 0;

    &:hover {
      color: #fff;
    }

    @include for-desktop {
      width: 265px;
      margin-top: 50px;
    }

  }
}
</style>
