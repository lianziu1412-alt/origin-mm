<template>
  <CenteredBox size="2xl" padding="md">
    <div class="space-y-6">
      <h1 class="text-3xl font-semibold text-center text-[#4b2a16]">Ranking</h1>
      <!-- Filter đơn giản -->
      <div
        class="flex flex-col sm:flex-row gap-3 items-stretch sm:items-center"
      >
        <input
          v-model="keyword"
          type="text"
          class="flex-1 rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Tìm theo tên hoặc lớp nhân vật..."
        />
      </div>
      <!-- Bảng xếp hạng -->
      <div class="overflow-x-auto rounded-lg border border-gray-200 bg-white">
        <table class="min-w-full divide-y divide-gray-200 text-sm">
          <thead class="bg-gray-50">
            <tr>
              <th class="px-4 py-2 text-left font-semibold text-gray-700">
                Vị trí
              </th>
              <th class="px-4 py-2 text-left font-semibold text-gray-700">
                Tên nhân vật
              </th>
              <th class="px-4 py-2 text-left font-semibold text-gray-700">
                Cấp độ
              </th>
              <th class="px-4 py-2 text-left font-semibold text-gray-700">
                Lớp nhân vật
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-100">
            <tr
              v-for="char in filteredRankings"
              :key="char.position"
              class="hover:bg-orange-50 transition-colors"
            >
              <td class="px-4 py-2 font-semibold text-gray-800">
                #{{ char.position }}
              </td>
              <td class="px-4 py-2 text-gray-900">{{ char.name }}</td>
              <td class="px-4 py-2 text-gray-900">{{ char.level }}</td>
              <td class="px-4 py-2 text-gray-900">{{ char.className }}</td>
            </tr>
            <tr v-if="!filteredRankings.length">
              <td colspan="4" class="px-4 py-6 text-center text-gray-400">
                Chưa có dữ liệu xếp hạng.
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </CenteredBox>
</template>
<script setup lang="ts">
import { computed, ref } from "vue";
interface RankingCharacter {
  position: number;
  name: string;
  level: number;
  className: string;
}
const rankingData = ref<RankingCharacter[]>([
  { position: 1, name: "DragonSlayer", level: 99, className: "Chiến Binh" },
  { position: 2, name: "ShadowMage", level: 97, className: "Pháp Sư" },
  { position: 3, name: "HolyKnight", level: 95, className: "Kỵ Sĩ" },
  { position: 4, name: "SilentAssassin", level: 93, className: "Sát Thủ" },
  { position: 5, name: "WindRanger", level: 90, className: "Cung Thủ" },
]);
const keyword = ref("");
const filteredRankings = computed(() => {
  const kw = keyword.value.toLowerCase().trim();
  if (!kw) return rankingData.value;
  return rankingData.value.filter(
    (c) =>
      c.name.toLowerCase().includes(kw) ||
      c.className.toLowerCase().includes(kw),
  );
});
</script>
