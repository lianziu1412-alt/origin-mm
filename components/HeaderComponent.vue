<template>
  <div class="header flex items-center justify-between w-full px-4">
    <div class="header-icon h-full flex items-center">
      <img
        src="@/assets/images/logo.svg"
        alt="logo"
        class="h-full object-contain p-1"
      />
    </div>
    <nav class="header-menu h-full flex items-center" style="height: 100%">
      <ul class="flex items-center h-full" style="height: 100%">
        <li v-for="menu in menus" :key="menu.id" class="h-full">
          <a
            :href="menu.link"
            class="px-4 py-2 text-gray-800 hover:bg-orange-500 hover:text-white transition-colors duration-200 font-medium h-full flex items-center"
            style="height: 100%"
          >
            {{ menu.name }}
          </a>
        </li>
      </ul>
    </nav>
    <div class="header-button h-full flex items-center gap-2">
      <button
        v-if="!isLoggedIn"
        class="px-2 py-2 border-2 border-brown-700 text-brown-700 bg-transparent hover:bg-orange-500 hover:text-white transition-colors duration-200 font-medium rounded"
        style="
          --tw-border-opacity: 1;
          border-color: rgba(89, 56, 30, var(--tw-border-opacity));
        "
      >
        <NuxtLink to="/register">Register</NuxtLink>
      </button>
      <button
        v-if="!isLoggedIn"
        class="px-2 py-2 border-2 border-brown-700 bg-brown-700 text-white font-medium rounded transition-colors duration-200 hover:bg-orange-500 hover:text-brown-800"
        style="
          --tw-bg-opacity: 1;
          --tw-border-opacity: 1;
          background-color: rgba(89, 56, 30, var(--tw-bg-opacity));
          border-color: rgba(89, 56, 30, var(--tw-border-opacity));
        "
      >
        <NuxtLink to="/login">Login</NuxtLink>
      </button>
      <button
        v-if="isLoggedIn"
        class="px-2 py-2 border-2 border-brown-700 text-brown-700 bg-transparent hover:bg-orange-500 hover:text-white transition-colors duration-200 font-medium rounded"
        style="
          --tw-border-opacity: 1;
          border-color: rgba(89, 56, 30, var(--tw-border-opacity));
        "
        @click="handleLogout"
      >
        Logout
      </button>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed, onMounted } from "vue";
import { useRouter, useState } from "#app";

const menus = [
  { id: 1, name: "Home", link: "/home" },
  { id: 2, name: "Download", link: "/download" },
  { id: 3, name: "Register", link: "/register" },
  { id: 4, name: "Ranking", link: "/ranking" },
  { id: 5, name: "Guide", link: "/guide" },
  { id: 6, name: "FAQ", link: "/faq" },
  { id: 7, name: "Discord", link: "https://discord.com/", external: true },
  { id: 8, name: "Facebook", link: "https://facebook.com/", external: true },
];

const router = useRouter();

// Global auth state dùng chung giữa các component
// Khởi tạo ngay từ localStorage để tránh flash khi navigate
const accessToken = useState<string | null>("access_token", () => {
  if (process.client) {
    return localStorage.getItem("access_token");
  }
  return null;
});

// Sync với localStorage khi component mount để đảm bảo đồng bộ
onMounted(() => {
  if (process.client) {
    const storedToken = localStorage.getItem("access_token");
    // Chỉ sync nếu khác nhau để tránh re-render không cần thiết
    if (storedToken !== accessToken.value) {
      accessToken.value = storedToken;
    }
  }
});

const isLoggedIn = computed(() => !!accessToken.value);

const handleLogout = async () => {
  try {
    await $fetch("http://localhost:8088/api/auth/logout", {
      method: "POST",
    });
  } catch (error) {
    // Có thể log lỗi nếu cần
  } finally {
    if (process.client) {
      localStorage.removeItem("access_token");
    }
    accessToken.value = null;
    router.push("/login");
  }
};
</script>
<style scoped>
.header {
  background-color: #ccc;
  height: 70px;
  font-size: 20px;
}
</style>
