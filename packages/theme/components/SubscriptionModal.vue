<template>
  <SfModal :visible="isVisible" class="subscription-modal" @close="$emit('close')">
    <SfHeading :level="2" title="Configure your package" />
    <div class="subscription-modal__options">
      <div
        class="subscription-modal__option-wrapper"
        v-for="variant in variants"
        :key="variant._variantId"
      >
        <SfCard
          class="subscription-modal__option"
          :class="{ 'subscription-modal__option--selected': isOptionSelected(getVariantOptionId(variant)) }"
          :title="getVariantTitle(variant)"
          button-text="Choose"
          description="Selection of 1 toy"
          @click="onOptionSelected(getVariantOptionId(variant))"
          :image="getVariantImage(variant)"
        />
      </div>
    </div>
    <div class="subscription-modal__weight">
      <SfHeading class="sf-heading--left" description="What it the weight of your dog?" />
      <SfRange
        class="subscription-modal__weight-range"
        :config="weightRangeConfig"
        @change="onWeightChanged"
      />
    </div>
    <div class="subscription-modal__actions">
      <SfPrice :regular="displayPrice" />
      <SfButton @click="onSubscribe" class="subscription-modal__subscribe">Subscribe</SfButton>
    </div>
  </SfModal>
</template>

<script>
import { SfCard, SfModal, SfButton, SfRange, SfHeading, SfPrice } from '@storefront-ui/vue';
import { ref, computed } from '@vue/composition-api';
import { useCart, productGetters } from '@vue-storefront/spree';
import { useUiState } from '~/composables';

export default {
  components: {
    SfCard,
    SfModal,
    SfButton,
    SfRange,
    SfHeading,
    SfPrice
  },
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    variants: {
      type: Array,
      required: true
    }
  },
  setup(props, context) {
    const selectedOptionId = ref('small');
    const selectedWeight = ref(3);
    const { addItem } = useCart();
    const { toggleCartSidebar } = useUiState();

    const isVisible = computed(() => props.visible);

    const selectedVariant = computed(() => {
      return props.variants.find((variant) => variant.optionValues.find(ov => ov.name === selectedOptionId.value) !== undefined);
    });

    const selectedVariantPrice = computed(() => {
      return parseFloat(selectedVariant.value.price.current);
    });

    const getVariantTitle = (variant) => {
      return variant.optionValues[0].presentation;
    };

    const getVariantOptionId = (variant) => {
      return variant.optionValues[0].name;
    };

    const getVariantImage = (variant) => {
      if (variant.images.length > 1) {
        return productGetters.getGallery(variant)[2].big.url;
      }

      return undefined;
    }

    const onOptionSelected = (optionId) => {
      selectedOptionId.value = optionId
    };

    const isOptionSelected = (optionId) => {
      return selectedOptionId.value === optionId;
    };

    const onWeightChanged = (value) => {
      selectedWeight.value = parseInt(value, 10);
    };

    const onSubscribe = async () => {
      await addItem({ product: selectedVariant.value });
      context.emit('close');
      toggleCartSidebar();
    }

    const displayPrice = computed(() => {
      if (!selectedVariant.value) {
        return 'Select your package first';
      }

      const value = parseFloat(selectedVariantPrice.value) + selectedWeight.value * 0.5;
      return `\$${value.toFixed(2)} / month`
    });

    const weightRangeConfig = ref({
      start: 10,
      range: {
        min: 3,
        max: 30
      },
      step: 1,
      format: {
        from: (value) => parseInt(value),
        to: (value) => `${value.toFixed(2)} kg`
      },
      tooltips: true
    });

    return {
      isVisible,
      onOptionSelected,
      isOptionSelected,
      onWeightChanged,
      onSubscribe,
      displayPrice,
      getVariantTitle,
      getVariantImage,
      getVariantOptionId,
      weightRangeConfig
    };
  }
}
</script>

<style lang="scss">
.subscription-modal {
  --modal-width: 960px;
  --modal-content-padding: var(--spacer-lg) var(--spacer-3xl);

  .sf-modal__container {
    border-radius: 30px;
  }
}

.subscription-modal__options {
  margin-top: 40px;
  display: grid;
  grid-template-columns: 50% 50%;
}

.subscription-modal__option-wrapper {
  padding: 10px;
}

.subscription-modal__weight {
  margin-top: 60px;
}

.subscription-modal__weight-range {
  margin-left: 0;
  margin-right: 0;
}

.subscription-modal__option {
  border-radius: 30px;
  background: white;
  filter: drop-shadow(3px 3px 6px rgba(0, 0, 0, 0.08));

  padding: 20px 20px 0 20px;

  --image-height: 200px;
  width: unset;

  .sf-card__image .sf-image {
    border-radius: 30px;
  }

  .sf-card__details {
    background: transparent;
  }
}

.subscription-modal__option--selected {
  border: 3px solid var(--c-primary);
}

.subscription-modal__actions {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-top: 50px;
}

.subscription-modal__subscribe {
  margin-left: 30px;
}
</style>
