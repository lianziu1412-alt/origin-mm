<template>
  <div class="countdown-timer flex flex-col items-center">
    <div class="text-xl font-medium text-[#5e4324] mb-2">
      Thời gian còn lại:
    </div>
    <div v-if="timeLeft" class="text-4xl font-bold text-[#e96b27]">
      {{ timeLeft.days }} ngày :
      {{ timeLeft.hours.toString().padStart(2, "0") }} giờ :
      {{ timeLeft.minutes.toString().padStart(2, "0") }} phút :
      {{ timeLeft.seconds.toString().padStart(2, "0") }} giây
    </div>
    <div v-else class="text-2xl text-red-500 mt-2">Đã hết thời gian</div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from "vue";

const props = defineProps<{
  endDate: string;
}>();

const end = computed(() => new Date(props.endDate));

const timeLeft = ref<{
  days: number;
  hours: number;
  minutes: number;
  seconds: number;
} | null>(null);

let intervalId: ReturnType<typeof setInterval> | null = null;

const calcTimeLeft = () => {
  const now = new Date();
  const diff = end.value.getTime() - now.getTime();
  if (diff <= 0) {
    timeLeft.value = null;
    return;
  }
  timeLeft.value = {
    days: Math.floor(diff / (1000 * 60 * 60 * 24)),
    hours: Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)),
    minutes: Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60)),
    seconds: Math.floor((diff % (1000 * 60)) / 1000),
  };
};

onMounted(() => {
  calcTimeLeft();
  intervalId = setInterval(calcTimeLeft, 1000);
});

onBeforeUnmount(() => {
  if (intervalId) clearInterval(intervalId);
});
</script>
