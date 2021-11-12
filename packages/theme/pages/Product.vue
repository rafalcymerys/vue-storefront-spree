<template>
  <div id="product">
    <SfBreadcrumbs :breadcrumbs="breadcrumbs" />

    <div class="product">
      <SfGallery :images="productGallery" />
      <div>
        <SfHeading :title="productGetters.getName(product)" :level="2" class="sf-heading--left" />
        <SfCharacteristic title="A new set of toys for your puppy" description="Delivered at your door every month" icon="heart" />
        <SfCharacteristic title="Cancel anytime" description="No commitment required" icon="clock" />
        <SfCharacteristic title="Veterinary approved" description="Only products that are safe for your pet" icon="account" />
        <SfButton @click="openModal">Subscribe now</SfButton>
      </div>
    </div>

    <SubscriptionModal :visible="isModalOpen" />
  </div>
</template>

<script>
import { SfBreadcrumbs, SfHeading, SfGallery, SfCharacteristic, SfButton } from '@storefront-ui/vue';
import { onSSR } from '@vue-storefront/core';
import { useProduct, productGetters } from '@vue-storefront/spree';
import { computed, ref } from '@vue/composition-api';
import SubscriptionModal from '@/components/SubscriptionModal.vue';

export default {
  components: {
    SfHeading,
    SfBreadcrumbs,
    SfGallery,
    SfCharacteristic,
    SfButton,
    SubscriptionModal
  },
  setup(props, context) {
    const { id, slug } = context.root.$route.params;
    const { products, search } = useProduct('products');
    const breadcrumbs = computed(() => productGetters.getBreadcrumbs(product.value));
    const productGallery = computed(() => productGetters.getGallery(product.value).map(img => ({
      mobile: { url: img.big },
      desktop: { url: img.big },
      big: { url: img.big },
      alt: product.value._name || product.value.name
    })));
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
      breadcrumbs,
      productGallery,
      openModal,
      isModalOpen
    };
  },
};
</script>

<style scoped>
.product {
  display: grid;
  grid-template-columns: 60% 40%;
}
</style>
