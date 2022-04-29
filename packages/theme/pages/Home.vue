<template>
  <div id="home">
    <Hero class="hero">
      <HeroItem
        v-for="(hero, i) in heroes"
        :key="i"
        :title="hero.title"
        :subtitle="hero.subtitle"
        :description="hero.description"
        :button-text="hero.buttonText"
        :background="hero.background"
        :image="hero.image"
        :class="hero.className"
        :link="localePath(getAgnosticCatLink({slug: '/women.html'}))"
      />
    </Hero>

    <HomeAdvantagesList />

    <LazyHydrate when-visible>
      <ProductsCarousel
        :products="newProducts"
        :loading="newProductsLoading"
        :title="$t('New Arrival')"
        :subtitle="$t('-10% off new collection')"
        :link="localePath(getAgnosticCatLink({slug: '/women.html'}))"
        :link-text="'All Goods'"
      />
    </LazyHydrate>

    <LazyHydrate when-visible>
      <GridBanner
        :banner-grid="3"
        class="banner-grid"
      >
        <template
          v-for="item in banners"
          #[item.slot]
        >
          <SfBanner
            :key="item.slot"
            :title="item.title"
            :subtitle="item.subtitle"
            :description="item.description"
            :button-text="item.buttonText"
            :image="item.image"
            :class="item.class"
          />
        </template>
      </GridBanner>
    </LazyHydrate>

    <LazyHydrate when-visible>
      <SfCallToAction
        :title="$t('Subscribe to Newsletters')"
        :button-text="$t('Subscribe')"
        :description="
          $t(
            'Be aware of upcoming sales and events. Receive gifts and special offers!'
          )
        "
        image="https://cdn.shopify.com/s/files/1/0407/1902/4288/files/newsletter_1240x202.jpg?v=1616496568"
        class="call-to-action"
      />
    </LazyHydrate>
    <LazyHydrate when-visible>
      <InstagramFeed />
    </LazyHydrate>

    <LazyHydrate when-visible>
      <MobileStoreBanner />
    </LazyHydrate>
  </div>
</template>
<script type="module">
import {
  SfBanner,
  SfCallToAction,
} from '@storefront-ui/vue';

import {
  defineComponent,
  ref,
  useContext,
  onMounted,
} from '@nuxtjs/composition-api';
import LazyHydrate from 'vue-lazy-hydration';
import { useCache, CacheTagPrefix } from '@vue-storefront/cache';
import { addBasePath } from '@vue-storefront/core';
import {
  useUiHelpers,
  useProduct,
} from '~/composables';
import { productGetters } from '~/getters';
import MobileStoreBanner from '~/components/MobileStoreBanner.vue';
import InstagramFeed from '~/components/InstagramFeed.vue';
import ProductsCarousel from '~/components/Custom/HomeProduct/HomeProductList.vue';
import Hero from '~/components/Custom/Hero/Hero';
import HomeAdvantagesList from '~/components/Custom/HomeAdvantages/HomeAdvantagesList';
import GridBanner from '~/components/Custom/GridBanner/GridBanner';

export default defineComponent({
  name: 'HomePage',
  components: {
    InstagramFeed,
    LazyHydrate,
    MobileStoreBanner,
    ProductsCarousel,
    SfBanner,
    GridBanner,
    SfCallToAction,
    HomeAdvantagesList,
    Hero,
  },
  // eslint-disable-next-line @typescript-eslint/explicit-module-boundary-types
  setup() {
    const { addTags } = useCache();
    const { app } = useContext();
    const { getAgnosticCatLink } = useUiHelpers();
    const year = new Date().getFullYear();
    const { getProductList, loading: newProductsLoading } = useProduct();
    const newProducts = ref([]);

    const heroes = ref([
      {
        title: '<span>Sale 30% </span> </br> Off everything',
        subtitle: `spring / summer ${year}`,
        description: 'Lorem ipsum dolor sit amet consectetuer adipiscing elit aenean commodo ligula eget dolor.',
        buttonText: app.i18n.t('Shop now'),
        background: '#eceff1',
        image: {
          mobile: addBasePath('/homepage/hero-bg-mobile.jpg'),
          desktop: addBasePath('/homepage/hero-bg.jpg'),
        },
        link: '/c/women/women-clothing-shirts',
      },
    ]);
    const banners = ref([
      {
        slot: 'banner-A',
        title: 'Up to 30% off in everything',
        image: {
          mobile:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerB_328x343.jpg',
          desktop:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerF_332x840.jpg',
        },
        class: 'sf-banner--slim',
      },
      {
        slot: 'banner-B',
        title: 'many products for mens',
        image: {
          mobile:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerB_328x343.jpg',
          desktop:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerF_332x840.jpg',
        },
        class: 'sf-banner--slim',
      },
      {
        slot: 'banner-C',
        title: 'beautiful women dress collection',
        image: {
          mobile:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerB_328x343.jpg',
          desktop:
            'https://cdn.shopify.com/s/files/1/0407/1902/4288/files/bannerF_332x840.jpg',
        },
        class: 'sf-banner--slim',
      },
    ]);

    onMounted(async () => {
      const productsData = await getProductList({
        pageSize: 8,
        currentPage: 1,
        sort: {
          position: 'DESC',
        },
      });

      addTags([{ prefix: CacheTagPrefix.View, value: 'home' }]);
      newProducts.value = productGetters.getFiltered(productsData?.items, { master: true });
    });

    // @ts-ignore
    return {
      banners,
      heroes,
      newProducts,
      newProductsLoading,
      productGetters,
      getAgnosticCatLink,
    };
  },
});
</script>

<style lang="scss" scoped>
.article-meta h4 a {
  color: #111111;
  font-weight: 600;
  font-size: 20px;
}

.article-meta {
  margin-top: 10px;
}

.article-item__meta-item:not(:last-child)::after {
  display: inline-block;
  content: '';
  width: 5px;
  height: 5px;
  margin: -1px 10px 0 10px;
  border-radius: 100%;
  background: rgba(0, 0, 0, 0.4);
  vertical-align: middle;
}

#home {
  box-sizing: border-box;
  padding: 0 var(--spacer-sm);
  @include for-desktop {
    max-width: 1560px;
    padding: 0;
    margin: 0 auto;
  }
}

.hero {
  margin: var(--spacer-xl) auto var(--spacer-lg);
  --hero-item-background-position: center;

  ::v-deep .sf-link:hover {
    color: var(--c-white);
  }

  @include for-desktop {
    margin: var(--spacer-xl) auto var(--spacer-2xl);
  }

  @include for-mobile {
    min-height: 580px;
  }

  &__arrow {
    --button-height: 2.75rem;
    --button-width: 2.75rem;
    --button-padding: 0 var(--spacer-xs);
    --button-background: transparent;
    --button-color: var(--c-dark);
    display: flex;
    align-content: center;
    justify-content: center;

    &:hover {
      --button-background: transparent;
      --button-box-shadow-opacity: 0;
    }
  }

  .sf-hero-item {
    &:nth-child(even) {
      --hero-item-background-position: left;
      @include for-mobile {
        --hero-item-background-position: 30%;
        --hero-item-v-slot-text-align: right;
        --hero-item-subtitle-width: 100%;
        --hero-item-title-width: 100%;
        --hero-item-wrapper-padding: var(--spacer-sm) var(--spacer-sm)
          var(--spacer-sm) var(--spacer-2xl);
      }
    }
  }
}

::v-deep .sf-hero__controls {
  --hero-controls-display: none;
}

.banner-grid {
  --banner-container-width: 50%;
  margin: var(--spacer-xl) 0;

  ::v-deep .sf-link:hover {
    color: var(--c-white);
  }

  @include for-desktop {
    margin: var(--spacer-2xl) 0;
    ::v-deep .sf-link {
      --button-width: auto;
    }
  }
}

.banner {
  &__tshirt {
    background-position: left;
  }

  &-central {
    @include for-desktop {
      --banner-container-flex: 0 0 70%;
    }
  }
}

.similar-products {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: var(--spacer-2xs);
  --heading-padding: 0;
  border-bottom: 1px var(--c-light) solid;
  @include for-desktop {
    border-bottom: 0;
    justify-content: center;
    padding-bottom: 0;
  }
}

.call-to-action {
  background-position: right;
  margin: var(--spacer-xs) 0;
  @include for-desktop {
    margin: var(--spacer-xl) 0 var(--spacer-2xl) 0;
  }
}

.carousel {
  margin: 0 calc(-1 * var(--spacer-sm)) 0 0;
  @include for-desktop {
    margin: 0;
  }

  &__item {
    margin: 1.375rem 0 2.5rem 0;
    @include for-desktop {
      margin: var(--spacer-xl) 0 var(--spacer-xl) 0;
    }

    &__product {
      --product-card-add-button-transform: translate3d(0, 30%, 0);
    }
  }
}

::v-deep .sf-section{
  margin-top: 0;
  margin-bottom: 0;
  padding: 30px 0;

  @include for-desktop {
    padding: 60px 0;
  }
}
</style>
