<template>
  <CenteredBox size="lg" padding="md">
    <h2 class="text-3xl font-semibold text-center text-[#4b2a16] leading-snug">
      Register
    </h2>

    <form @submit.prevent="onSubmit" class="space-y-5">
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Username
        </label>
        <input
          v-model="form.username"
          type="text"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Enter your username"
        />
        <p v-if="errors.username" class="mt-1 text-xs text-red-500">
          {{ errors.username }}
        </p>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Email
        </label>
        <input
          v-model="form.email"
          type="email"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Enter your email"
        />
        <p v-if="errors.email" class="mt-1 text-xs text-red-500">
          {{ errors.email }}
        </p>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Password
        </label>
        <input
          v-model="form.password"
          type="password"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Enter your password"
        />
        <p v-if="errors.password" class="mt-1 text-xs text-red-500">
          {{ errors.password }}
        </p>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Confirm Password
        </label>
        <input
          v-model="form.confirmPassword"
          type="password"
          class="w-full rounded-md border border-gray-300 bg-white text-gray-900 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-orange-500 placeholder:text-gray-400"
          placeholder="Confirm your password"
        />
        <p v-if="errors.confirmPassword" class="mt-1 text-xs text-red-500">
          {{ errors.confirmPassword }}
        </p>
      </div>

      <button
        type="submit"
        :disabled="isLoading"
        class="w-full rounded-md bg-orange-500 hover:bg-orange-600 disabled:bg-orange-300 disabled:cursor-not-allowed text-white font-medium py-2.5 text-sm transition-colors"
      >
        {{ isLoading ? "Registering..." : "Register" }}
      </button>

      <p v-if="errorMessage" class="text-red-500 text-sm text-center mt-2">
        {{ errorMessage }}
      </p>

      <p v-if="successMessage" class="text-green-500 text-sm text-center mt-2">
        {{ successMessage }}
      </p>
    </form>
  </CenteredBox>
</template>

<script setup lang="ts">
import { reactive, ref } from "vue";
import { useRouter } from "#app";

interface RegisterForm {
  username: string;
  email: string;
  password: string;
  confirmPassword: string;
}

interface RegisterErrors {
  username: string;
  email: string;
  password: string;
  confirmPassword: string;
}

const form = reactive<RegisterForm>({
  username: "",
  email: "",
  password: "",
  confirmPassword: "",
});

const errors = reactive<RegisterErrors>({
  username: "",
  email: "",
  password: "",
  confirmPassword: "",
});

const isLoading = ref(false);
const errorMessage = ref("");
const successMessage = ref("");

const router = useRouter();

// Đổi lại BASE_URL cho đúng với backend của bạn nếu cần thiết
const API_BASE_URL =
  import.meta.env.VITE_API_URL || "http://localhost:8088/api";

const validate = (): boolean => {
  let valid = true;

  errors.username = "";
  errors.email = "";
  errors.password = "";
  errors.confirmPassword = "";
  errorMessage.value = "";

  if (!form.username.trim()) {
    errors.username = "Username is required";
    valid = false;
  }

  if (!form.email.trim()) {
    errors.email = "Email is required";
    valid = false;
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errors.email = "Email is invalid";
    valid = false;
  }

  if (!form.password) {
    errors.password = "Password is required";
    valid = false;
  } else if (form.password.length < 6) {
    errors.password = "Password must be at least 6 characters";
    valid = false;
  }

  if (!form.confirmPassword) {
    errors.confirmPassword = "Please confirm your password";
    valid = false;
  } else if (form.confirmPassword !== form.password) {
    errors.confirmPassword = "Passwords do not match";
    valid = false;
  }

  return valid;
};

const onSubmit = async () => {
  if (!validate()) return;

  isLoading.value = true;
  errorMessage.value = "";
  successMessage.value = "";

  try {
    const res = await fetch(`${API_BASE_URL}/users/register`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        username: form.username.trim(),
        email: form.email.trim(),
        password: form.password,
      }),
    });

    if (!res.ok) {
      const errorData = await res.json();
      const message = errorData?.message || "Registration failed";

      // Handle specific error cases
      if (message.includes("Email already exists") || res.status === 409) {
        errors.email = "Email already exists";
      } else {
        errorMessage.value = message;
      }
      return;
    }

    const data = await res.json();
    successMessage.value = "Registration successful! Redirecting to login...";

    // Redirect to login page after 1.5 seconds
    setTimeout(() => {
      router.push({ path: "/login" });
    }, 1500);
  } catch (err: any) {
    errorMessage.value = "Something went wrong. Please try again.";
    console.error("Registration error:", err);
  } finally {
    isLoading.value = false;
  }
};
</script>
