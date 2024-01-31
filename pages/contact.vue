<template>
  <form @submit.prevent="handleSubmit">
    <!-- Your form fields go here -->
    <!-- Submit button with an onclick event to trigger reCAPTCHA validation -->

    <button
      type="submit"
      class="rounded-full bg-indigo-600 px-4 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">
      Submit
    </button>
  </form>
</template>

<script lang="ts" setup>
import { useReCaptcha } from "vue-recaptcha-v3";
const recaptchaInstance = useReCaptcha();
const recaptchaToken = ref("");

const recaptcha = async () => {
  await recaptchaInstance?.recaptchaLoaded();
  return await recaptchaInstance?.executeRecaptcha("message");
};

onMounted(async () => {
  const token = await recaptcha();
  if (token) {
    recaptchaToken.value = token;
  }
});

const handleSubmit = async () => {
  console.log(recaptchaToken.value);
  const { data } = await useFetch("/api/contact", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      recaptchaToken: recaptchaToken.value,
    }),
  });
  console.log(data);
};
</script>
