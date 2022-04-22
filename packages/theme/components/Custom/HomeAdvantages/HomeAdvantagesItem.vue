<template>
  <div class="advantages-item">
    <img
      :src="image"
      :alt="title"
    >
    <div>
      <h6>{{ title }}</h6>
      <p>{{ description }}</p>
    </div>
  </div>
</template>
<script>
export default {
  name: 'HomeAdvantagesItem',

  props: {
    title: {
      type: String,
      default: '',
    },
    description: {
      type: String,
      default: '',
    },
    image: {
      type: [Object, String],
      default: '',
    },
  },
};
</script>

<style scoped lang="scss">
.advantages-item {
  display: flex;
  align-items: center;

  @include for-mobile {
    margin-bottom: 20px;
  }

  img {
    margin-right: 15px;
    width: 65px;
    height: 65px;
    object-fit: cover;
    transform: scale(1.8);

    @include for-desktop {
      margin-right: 35px;
      margin-left: 4px;
      width: 97px;
      height: 95px;
      object-fit: cover;
      transform: scale(1.8);
    }
  }

  h6 {
    font-size: 16px;
    line-height: 20px;
    font-weight: var(--font-weight--bold);
    margin: 0;

    @include for-desktop {
      line-height: 16px;
    }
  }

  p {
    font-size: 14px;
    line-height: 17px;
    font-weight: var(--font-weight--normal);
    margin: 10px 0 0;

    @include for-desktop {
      line-height: 22px;
      margin-top: 15px;
    }
  }
}

</style>
