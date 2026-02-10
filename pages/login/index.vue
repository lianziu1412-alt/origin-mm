<template>
  <CenteredBox size="2xl" padding="md">
    <h1 class="text-3xl font-semibold text-center text-[#4b2a16]">Login</h1>
    <form @submit.prevent="onSubmit" class="space-y-5">
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Email
        </label>
        <input
          v-model="email"
          type="email"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Enter your email"
        />
      </div>
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Password
        </label>
        <input
          v-model="password"
          type="password"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Enter your password"
        />
      </div>
      <button
        type="submit"
        class="w-full rounded-md bg-orange-500 hover:bg-orange-600 text-white font-medium py-2.5 text-sm transition-colors"
      >
        Login
      </button>
      <p v-if="errorMessage" class="text-red-500 text-sm text-center mt-2">
        {{ errorMessage }}
      </p>
    </form>
  </CenteredBox>
</template>
<script setup lang="ts">
import { ref } from "vue";
import { useRouter, useState } from "#app";

const email = ref("");
const password = ref("");
const errorMessage = ref("");

const router = useRouter();
// Dùng cùng state toàn cục với HeaderComponent
const accessToken = useState<string | null>("access_token");

// Đổi lại BASE_URL cho đúng với backend của bạn nếu cần thiết
const API_BASE_URL =
  import.meta.env.VITE_API_URL || "http://localhost:8088/api";

const onSubmit = async () => {
  errorMessage.value = "";
  try {
    const res = await fetch(`${API_BASE_URL}/auth/login`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        email: email.value,
        password: password.value,
      }),
    });

    if (!res.ok) {
      const errorData = await res.json();
      errorMessage.value = errorData?.message || "Login failed";
      return;
    }

    const data = await res.json();
    if (data?.access_token) {
      if (process.client) {
        localStorage.setItem("access_token", data.access_token);
      }
      accessToken.value = data.access_token;
    }

    router.push({ path: "/home" });
  } catch (err: any) {
    errorMessage.value = "Something went wrong. Please try again.";
  }
};
</script>
