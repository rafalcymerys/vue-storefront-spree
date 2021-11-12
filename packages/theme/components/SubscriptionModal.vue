<template>
  <SfModal :visible="isVisible" class="subscription-modal" @close="$emit('close')">
    <SfHeading :level="2" title="Configure your package" />
    <div class="subscription-modal__options">
      <div class="subscription-modal__option-wrapper">
        <SfCard
          :class="{ 'subscription-modal__option--selected': isOptionSelected('small') }"
          title="Small package"
          button-text="Choose"
          description="Selection of 1 toy"
          @click="onOptionSelected('small')"
          image="/dog1.jpg"
        />
      </div>
      <div class="subscription-modal__option-wrapper">
        <SfCard
          :class="{ 'subscription-modal__option--selected': isOptionSelected('large') }"
          title="Large package"
          @click="onOptionSelected('large')"
          description="Selection of 1 toy and 1 outfit"
          button-text="Choose"
          image="/dog1.jpg"
        />
      </div>
    </div>
    <div>
      <SfHeading class="sf-heading--left" description="What it the weight of your dog?" />
      <SfRange
        class="subscription-modal__weight"
        :config="{
          start: 10,
          range: {
            min: 3,
            max: 30
          },
          step: 1,
          format: {
            from: (value) => parseInt(value),
            to: (value) => `${value} kg`
          },
          tooltips: true
        }"
      />
    </div>
    <SfPrice :regular="price" />
    <SfButton>Add to cart</SfButton>
  </SfModal>
</template>

<script>
import { SfCard, SfModal, SfButton, SfRange, SfHeading, SfPrice } from '@storefront-ui/vue';
import { ref, watch, computed } from '@vue/composition-api';

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
  },
  setup(props, context) {

    /// todo why is this not working?
    const selectedOptionId = ref('small');
    const selectedWeight = ref(3);

    const isVisible = computed(() => props.visible);

    const onOptionSelected = (optionId) => {
      selectedOptionId.value = optionId
    };

    const isOptionSelected = (optionId) => {
      return selectedOptionId.value === optionId;
    };

    const onWeightChanged = (value) => {
      console.error(value)
      selectedWeight.value = parseInt(value, 10);
    };

    const price = computed(() => {
      const basePrice = 10
      const value = basePrice + (selectedOptionId.value === 'small' ? 0 : 10) + selectedWeight.value * 0.5;
      return `\$${value.toFixed(2)} / month`
    });

    return {
      isVisible,
      onOptionSelected,
      isOptionSelected,
      onWeightChanged,
      price
    };
  }
}
</script>

<style scoped>
.subscription-modal {
  --modal-width: 900px;
}
.subscription-modal__options {
  display: grid;
  grid-template-columns: 50% 50%;
}

.subscription-modal__option-wrapper {
  padding: 10px;
}

.subscription-modal__weight {
  margin-left: 0;
  margin-right: 0;
}

.subscription-modal__option--selected {
  border: 2px solid var(--c-secondary);
}
</style>
