<script setup lang="ts">
import backgroundImage from '@/assets/images/CockFight.jpg';
import logoImage from '@/assets/images/Logo.png';
import { Button } from '@/components/ui/button';
import {
  Card,
  CardContent,
  CardHeader,
  CardTitle
} from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { LockClosedIcon, PersonIcon } from '@radix-icons/vue';
import { useField, useForm } from 'vee-validate';
import { computed, ref } from 'vue';
import * as yup from 'yup';

const schema = yup.object({
  username: yup.string().required('Username is required'),
  password: yup.string().required('Password is required'),
});

const { handleSubmit, isSubmitting, errors } = useForm({
  validationSchema: schema,
});

const { value: username, errorMessage: usernameError } = useField<string>('username');
const { value: password, errorMessage: passwordError } = useField<string>('password');
const showAlert = ref(false);
const alertMessage = ref('');
const alertType = ref('error');
const isLoading = ref(false);

const alertClasses = computed(() => {
  return alertType.value === 'error'
    ? 'bg-red-100 text-red-500 border-red-500 rounded-lg shadow-md'
    : 'bg-green-100 text-green-500 border-blue-500 rounded-lg shadow-md';
});

const onSubmit = handleSubmit(
  async (values) => {
    isLoading.value = true;
    console.log('Logging in with:', values);
    
    await new Promise(resolve => setTimeout(resolve, 2000));
    
    isLoading.value = false;
    showAlert.value = true;
    alertMessage.value = 'Login successful!';
    alertType.value = 'success';
    setTimeout(() => {
      showAlert.value = false;
    }, 3000);
  },
  (errors) => {
    if (Object.keys(errors).length) {
      showAlert.value = true;
      alertMessage.value = 'Please fill in all required fields.';
      alertType.value = 'error';
    }
  }
);
</script>

<template>
  <div class="relative min-h-screen bg-cover bg-center" :style="{ backgroundImage: `url(${backgroundImage})` }">
    <div class="flex items-center justify-center min-h-screen px-4">
      <div class="flex flex-col items-center w-full max-w-lg">
        <img :src="logoImage" alt="Logo" class="mb-6 w-72"/>
        <Card class="w-full p-8 bg-white bg-opacity-80 rounded-lg shadow-md">
          <CardHeader class="flex items-center justify-center">
            <CardTitle class="text-xxl font-bold">LOGIN</CardTitle>
          </CardHeader>
          <CardContent>
            <form @submit="onSubmit">
              <div class="grid gap-6">
                <div class="relative">
                  <Label for="username" class="text-lg">
                    Username <span class="text-red-500">*</span>
                  </Label>
                  <div class="flex items-center mt-2">
                    <PersonIcon class="absolute left-3 text-gray-500" />
                    <Input v-model="username" id="username" type="text" placeholder="Username" class="text-lg pl-10" />
                  </div>
                  <div v-if="usernameError" class="text-red-500 mt-1">
                    {{ usernameError }}
                  </div>
                </div>
                <div class="relative">
                  <Label for="password" class="text-lg">
                    Password <span class="text-red-500">*</span>
                  </Label>
                  <div class="flex items-center mt-2">
                    <LockClosedIcon class="absolute left-3 text-gray-500" />
                    <Input v-model="password" id="password" type="password" placeholder="Password" class="text-lg pl-10" />
                  </div>
                  <div v-if="passwordError" class="text-red-500 mt-1">
                    {{ passwordError }}
                  </div>
                </div>
              </div>
              <div class="flex justify-end mt-6">
                <Button :disabled="isSubmitting || isLoading" type="submit" class="text-lg relative">
                  <span :class="{ invisible: isLoading }">Login</span>
                  <div v-if="isLoading" class="absolute inset-0 flex items-center justify-center">
                    <svg class="animate-spin h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                      <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                      <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                  </div>
                </Button>
              </div>
            </form>
          </CardContent>
        </Card>

        <transition name="slide-alert">
          <div
            class="fixed top-4 right-4 w-80"
            v-if="showAlert"
            :class="alertClasses"
          >
            <div class="border p-4 flex items-center rounded-lg shadow-md">
              <div>
                <p>
                  {{ alertMessage }}
                </p>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<style>
.slide-alert-enter-active, .slide-alert-leave-active {
  transition: transform 0.5s ease, opacity 0.5s ease;
}
.slide-alert-enter {
  transform: translateX(100%);
  opacity: 0;
}
.slide-alert-enter-to {
  transform: translateX(0);
  opacity: 1;
}
.slide-alert-leave-active {
  transition: transform 0.5s ease, opacity 0.5s ease;
}
.slide-alert-leave-to {
  transform: translateX(100%);
  opacity: 0;
}

.fixed {
  position: fixed;
  z-index: 999;
}
</style>