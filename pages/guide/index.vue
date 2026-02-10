<template>
  <CenteredBox size="2xl" padding="md">
    <div class="space-y-6">
      <h1 class="text-3xl font-semibold text-center text-[#4b2a16]">
        Hướng dẫn
      </h1>

      <div class="flex flex-col sm:flex-row gap-3 items-stretch sm:items-center">
        <input
          v-model="keyword"
          type="text"
          class="flex-1 rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Tìm hướng dẫn..."
        />
      </div>

      <ul class="space-y-4">
        <li
          v-for="guide in filteredGuides"
          :key="guide.id"
          class="rounded-lg border border-gray-200 bg-white overflow-hidden shadow-sm hover:shadow-md transition-shadow"
        >
          <button
            type="button"
            class="w-full text-left px-4 py-3 flex items-center justify-between gap-2 outline-none"
            @click="onGuideClick(guide.id, $event)"
          >
            <span class="font-semibold text-gray-800">{{ guide.title }}</span>
            <span
              class="text-orange-500 text-xl leading-none transition-transform duration-200"
              :class="{ 'rotate-180': expandedId === guide.id }"
            >
              ▼
            </span>
          </button>
          <Transition name="guide-expand">
            <div
              v-show="expandedId === guide.id"
              class="guide-expand-content px-4 pb-4 pt-0 border-t border-gray-100"
            >
              <p class="text-gray-600 text-sm mt-3">{{ guide.description }}</p>
              <ol
                v-if="guide.steps?.length"
                class="mt-3 list-decimal list-inside space-y-1 text-sm text-gray-700"
              >
                <li v-for="(step, i) in guide.steps" :key="i">
                  {{ step }}
                </li>
              </ol>
            </div>
          </Transition>
        </li>

        <li
          v-if="!filteredGuides.length"
          class="text-center text-gray-400 py-8"
        >
          Không tìm thấy hướng dẫn phù hợp.
        </li>
      </ul>
    </div>
  </CenteredBox>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";

interface Guide {
  id: number;
  title: string;
  description: string;
  steps?: string[];
}

const guides = ref<Guide[]>([
  {
    id: 1,
    title: "Bắt đầu chơi",
    description:
      "Hướng dẫn tạo tài khoản, tải game và đăng nhập lần đầu.",
    steps: [
      "Đăng ký tài khoản tại trang Register.",
      "Tải client game tại mục Download.",
      "Cài đặt và mở game, đăng nhập bằng email đã đăng ký.",
    ],
  },
  {
    id: 2,
    title: "Tạo nhân vật",
    description: "Cách chọn lớp nhân vật và đặt tên nhân vật.",
    steps: [
      "Sau khi đăng nhập, chọn server và vào game.",
      "Chọn lớp nhân vật (Chiến Binh, Pháp Sư, Cung Thủ, ...).",
      "Nhập tên nhân vật và xác nhận.",
    ],
  },
  {
    id: 3,
    title: "Nâng cấp và cấp độ",
    description: "Cách kiếm kinh nghiệm, lên cấp và tăng sức mạnh.",
    steps: [
      "Tiêu diệt quái để nhận EXP và vàng.",
      "Hoàn thành nhiệm vụ chính và phụ.",
      "Sử dụng trang bị và kỹ năng phù hợp với lớp.",
    ],
  },
  {
    id: 4,
    title: "Bảng xếp hạng",
    description: "Cách xem ranking và so tài với người chơi khác.",
    steps: [
      "Vào mục Ranking trên website hoặc trong game.",
      "Xem top theo cấp độ, lớp nhân vật.",
      "Cố gắng leo rank để nhận phần thưởng theo mùa.",
    ],
  },
  {
    id: 5,
    title: "Liên hệ & hỗ trợ",
    description: "Khi gặp lỗi hoặc cần hỗ trợ, liên hệ qua các kênh sau.",
    steps: [
      "Đọc mục FAQ để xem câu hỏi thường gặp.",
      "Tham gia Discord hoặc Facebook của game.",
      "Gửi ticket hoặc email cho đội ngũ hỗ trợ.",
    ],
  },
]);

const keyword = ref("");
const expandedId = ref<number | null>(guides.value[0]?.id ?? null);

const filteredGuides = computed(() => {
  const kw = keyword.value.toLowerCase().trim();
  if (!kw) return guides.value;
  return guides.value.filter(
    (g) =>
      g.title.toLowerCase().includes(kw) ||
      g.description.toLowerCase().includes(kw),
  );
});

function toggle(id: number) {
  expandedId.value = expandedId.value === id ? null : id;
}

function onGuideClick(id: number, e: MouseEvent) {
  toggle(id);
  (e.currentTarget as HTMLElement)?.blur();
}
</script>

<style scoped>
.guide-expand-content {
  overflow: hidden;
}

.guide-expand-enter-active,
.guide-expand-leave-active {
  transition:
    max-height 0.3s ease,
    opacity 0.2s ease;
}

.guide-expand-enter-from,
.guide-expand-leave-to {
  max-height: 0;
  opacity: 0;
}

.guide-expand-enter-to,
.guide-expand-leave-from {
  max-height: 500px;
  opacity: 1;
}
</style>
