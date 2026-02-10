<template>
  <div
    class="centered-box min-h-[calc(100vh-70px)] flex items-center justify-center bg-[#f3e1d3] px-4 py-8"
  >
    <div
      class="centered-box__inner w-full bg-white rounded-2xl shadow-xl border border-[#c59a7b] space-y-6"
      :class="[maxWidthClass, paddingClass]"
    >
      <slot />
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";

type BoxSize = "sm" | "md" | "lg" | "xl" | "2xl";
type BoxPadding = "sm" | "md" | "lg";

const props = withDefaults(
  defineProps<{
    size?: BoxSize;
    padding?: BoxPadding;
  }>(),
  {
    size: "lg",
    padding: "md",
  }
);

const maxWidthMap: Record<BoxSize, string> = {
  sm: "max-w-sm",
  md: "max-w-md",
  lg: "max-w-lg",
  xl: "max-w-xl",
  "2xl": "max-w-2xl",
};

const paddingMap: Record<BoxPadding, string> = {
  sm: "px-6 py-5",
  md: "px-10 py-8",
  lg: "px-12 py-10",
};

const maxWidthClass = computed(() => maxWidthMap[props.size]);
const paddingClass = computed(() => paddingMap[props.padding]);
</script>
