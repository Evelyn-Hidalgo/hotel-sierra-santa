
<script setup lang="ts">
withDefaults(defineProps<{
  variant?: 'green' | 'coral' | 'outline'
  type?: 'button' | 'submit' | 'reset'
  disabled?: boolean
}>(), {
  variant: 'green',
  type: 'button',
  disabled: false
})
</script>

<template>
  <button
    class="button"
    :class="[
      `button-${variant}`,
      { 'full-button': false }
    ]"
    :type="type"
    :disabled="disabled"
  >
    <slot />
  </button>
</template>

<style scoped>
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  padding: 14px 19px;
  border: 1px solid transparent;
  font: inherit;
  font-size: 10px;
  cursor: pointer;
  transition: transform .2s, opacity .2s;
}

.button:hover:not(:disabled) {
  transform: translateY(-2px);
}

.button:disabled {
  cursor: not-allowed;
  opacity: .6;
}

.button-green {
  background: #125F50;
  color: white;
}

.button-coral {
  background: #EF6F6C;
  color: #160f13;
}

.button-outline {
  background: transparent;
  border-color: #125F50;
  color: #125F50;
}

:global(.dark-mode) .button-outline {
  border-color: #A1EDDE;
  color: #A1EDDE;
}
</style>
