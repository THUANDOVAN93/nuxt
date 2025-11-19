<script setup>
const { alphaValidator, emailValidator, requiredValidator, passwordValidator, confirmedValidator } =
  useValidators();

const refVForm = ref();
const isPasswordVisible = ref(false);
const userName = ref("");
const email = ref("");
const password = ref("");
const confirmPassword = ref("");
const policyCheck = ref(false);

const errors = ref({
  userName: undefined,
  email: undefined,
  password: undefined,
  confirmPassword: undefined,
  policyCheck: undefined,
});

const onSubmit = () => {
  refVForm.value?.validate().then(({ valid: isValid }) => {
    if (isValid) {
      const formData = {
        userName: userName.value,
        email: email.value,
        password: password.value,
        confirmPassword: confirmPassword.value,
        policyCheck: policyCheck.value,
      };
      console.log('Form Data:', formData);
      handleSignup(formData);
    };
  });
};

const handleSignup = async (data) => {
  // errorMessage.value = '';
  const config = useRuntimeConfig();
  try {
    const response = await fetch(config.public.apiBase+ '/register', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ data }),
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.message || 'Signup failed');
    }

    // Handle successful signup (e.g., redirect to login or dashboard)
    console.log('User registered successfully!');
    // Example: navigateTo('/login');
  } catch (error) {
    console.error('Signup error:', error);
    // errorMessage.value = error.message;
  }
};


</script>
<template>
  <v-card elevation="4">
    <v-card-item class="pa-6">
      <div class="mb-6">
        <NuxtLink to="/" class="d-flex mb-2">
          <img src="/images/brand/logo/logo-light.svg" height="30px" />
        </NuxtLink>
        <p class="text-body-1">Please enter your user information.</p>
      </div>

      <v-form ref="refVForm" @submit.prevent="onSubmit">
        <GlobalsTextField
          v-model="userName"
          label="User Name"
          autofocus
          placeholder="Username here"
          :rules="[requiredValidator, alphaValidator]"
          :error-messages="errors.userName"
          class="mb-3"
        />

        <GlobalsTextField
          v-model="email"
          label="Email"
          type="email"
          autofocus
          placeholder="Email address here"
          :rules="[requiredValidator, emailValidator]"
          :error-messages="errors.email"
          class="mb-3"
        />

        <GlobalsTextField
          v-model="password"
          label=" Password"
          placeholder="************"
          :rules="[requiredValidator, passwordValidator]"
          type="password"
          :error-messages="errors.password"
          class="mb-3"
        />

        <GlobalsTextField
          v-model="confirmPassword"
          label=" Confirm Password"
          placeholder="************"
          :rules="[requiredValidator, confirmedValidator(confirmPassword, password)]"
          :type="isPasswordVisible ? 'text' : 'password'"
          :error-messages="errors.confirmPassword"
          :append-inner-icon="isPasswordVisible ? 'tabler-eye-off' : 'tabler-eye'"
          @click:append-inner="isPasswordVisible = !isPasswordVisible"
          class="mb-3"
        />

        <v-checkbox
          v-model="policyCheck"
          label="Remember me"
          class="mb-4"
          :rules="[requiredValidator]"
          :error-messages="errors.policyCheck"
        >
          <template #label>
            <p class="text-body-1">
              I agree to the
              <NuxtLink to="/" class="mx-1 font-weight-5 text-primary">Terms of Service </NuxtLink>
              and
              <NuxtLink to="/" class="ml-1 font-weight-5 text-primary">Privacy Policy</NuxtLink>.
            </p>
          </template>
        </v-checkbox>

        <v-btn type="submit" block> Create Account </v-btn>
        <div class="mt-4 d-flex align-center justify-space-between ga-2 flex-wrap">
          <NuxtLink to="sign-in" class="font-weight-5 text-primary">
            Already member? Login
          </NuxtLink>
          <NuxtLink to="forget-password" class="font-weight-5"> Forgot your password? </NuxtLink>
        </div>
      </v-form>
    </v-card-item>
  </v-card>
</template>
