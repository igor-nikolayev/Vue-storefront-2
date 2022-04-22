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
            :wishlist-icon="isAuthenticated ? 'heart' : ''"
            :is-in-wishlist-icon="isAuthenticated ? 'heart_fill' : ''"
            @click:wishlist="addItemToWishlist(product)"
            @click:add-to-cart="addItemToCart({ product, quantity: 1 })"
          />
        </div>
      </div>
    </SfLoader>
  </SfSection>
</template>

<script>
import {
  SfSection,
  SfLoader,
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

    @include for-desktop {
      width: 25%;
    }
  }
}
 ::v-deep .sf-product-card {
   width: 100%;
   max-width: 100%;
   padding: 10px 10px 15px;
   border: 1px solid #DEE7F1;
   text-align: center;

   @include for-desktop {
     padding: 30px 35px 35px;
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

}
</style>
