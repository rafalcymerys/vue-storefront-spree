<template>
  <div id="product">
    <div class="product">
      <SfGallery
        :images="productGallery"
        enable-zoom="true"
      />
      <div>
        <SfHeading :title="productGetters.getName(product)" description="A set of toys and outfits for your puppy" :level="2" class="sf-heading--left" />
        <div class="product__characteristics">
          <SfCharacteristic class="product__characteristic" title="A new set of toys for your puppy" description="Delivered at your door every month" icon="heart" />
          <SfCharacteristic class="product__characteristic" title="Cancel anytime" description="No commitment required" icon="clock" />
          <SfCharacteristic class="product__characteristic" title="Veterinary approved" description="Only products that are safe for your pet" icon="account" />
        </div>
        <SfButton class="product__subscription-button" @click="openModal">Subscribe now</SfButton>
      </div>
    </div>

    <SubscriptionModal
      :visible="isModalOpen"
      @close="isModalOpen = false"
      :variants="products"
    />
  </div>
</template>

<script>
import { SfHeading, SfGallery, SfCharacteristic, SfButton } from '@storefront-ui/vue';
import { onSSR } from '@vue-storefront/core';
import { useProduct, productGetters } from '@vue-storefront/spree';
import { computed, ref } from '@vue/composition-api';
import SubscriptionModal from '@/components/SubscriptionModal.vue';

export default {
  components: {
    SfHeading,
    SfGallery,
    SfCharacteristic,
    SfButton,
    SubscriptionModal
  },
  setup(props, context) {
    const { slug } = context.root.$route.params;
    const { products, search } = useProduct('products');
    const productGallery = computed(() => productGetters.getGallery(product.value));
    const isModalOpen = ref(false);

    const product = computed(() => {
      return products.value[0];
    });

    onSSR(async () => {
      await search({ slug });
    });

    const openModal = () => {
      console.error('test');
      isModalOpen.value = true;
    };

    return {
      product,
      productGetters,
      products,
      productGallery,
      openModal,
      isModalOpen
    };
  },
};
</script>

<style lang="scss">
.product {
  display: grid;
  grid-template-columns: 60% 40%;
  margin-top: 6.5rem;
  margin-bottom: 6.5rem;
}

.product__characteristics {
  margin-top: 3rem;
}

.product__characteristic {
  margin-bottom: 2rem;

  --characteristic-title-font-weight: var(--font-weight--semi-bold);

  .sf-characteristic__icon {
    margin-right: 1.5rem;
    border-radius: 100%;
    padding: 8px;
    background: white;
    filter: drop-shadow(3px 3px 6px rgba(0, 0, 0, 0.08));
  }
}

.product__subscription-button {
  margin-top: 4.5rem;
}
</style>
