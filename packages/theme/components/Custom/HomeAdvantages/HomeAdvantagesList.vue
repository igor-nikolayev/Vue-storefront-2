<template>
  <div class="advantages-section">
    <div
      v-for="advantage in advantages"
      :key="advantage.title"
      class="advantages-item-wrapper"
    >
      <HomeAdvantagesItem
        :image="advantage.image"
        :description="advantage.description"
        :title="advantage.title"
      />
    </div>
  </div>
</template>

<script>
import { addBasePath } from '@vue-storefront/core';
import { ref } from '@nuxtjs/composition-api';
import HomeAdvantagesItem from './HomeAdvantagesItem';

export default {
  name: 'HomeAdvantagesList',
  components: {
    HomeAdvantagesItem,
  },
  setup() {
    const advantages = ref(
      [
        {
          image: addBasePath('/homepage/icon-free-delivery.png'),
          title: 'Free Delivery',
          description: 'For all order over €200',
        },
        {
          image: addBasePath('/homepage/icon-30-days.png'),
          title: '30 Days Return',
          description: 'If goods have problems',
        },
        {
          image: addBasePath('/homepage/icon-secure.png'),
          title: 'Secure Payment',
          description: 'Payment upon receipt after verification',
        },
        {
          image: addBasePath('/homepage/icon-24-7.png'),
          title: '24/7 Support',
          description: 'Delicated support',
        },
      ],
    );
    return { advantages };
  },
};

</script>

<style scoped lang="scss">
.advantages-section {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  padding: 30px 0 10px;

  @include for-desktop {
    padding: 60px 0;
  }
}
</style>
