<template>
  <q-layout>
    <q-page-container>
      <q-page class="row full-height">
        <!-- Coloquei os campos de login centralizados como sugestão de alteração,
        caso não aprovado retorno ao plano original do Figma -->
        <div class="col-12 col-md-7 flex flex-center bg-white">
          <div class="login-form">
            <div class="text-center">
              <q-img :src="logo" class="logo" />
            </div>
            <q-form @submit="onSubmit">
              <div class="text-subtitle2 text-bold text-primary">
                E-mail
              </div>
              <q-input
                v-model="email"
                type="email"
                outlined
                class="q-mb-md"
                :dense="true"
              />
              <div class="text-subtitle2 text-bold text-primary">
                Senha
              </div>
              <q-input
                v-model="password"
                :type="isPwd ? 'password' : 'text'"
                outlined
                class="q-mb-md"
                dense
              >
                <template v-slot:append>
                  <q-icon
                    :name="isPwd ? 'visibility_off' : 'visibility'"
                    class="cursor-pointer"
                    @click="togglePassword"
                  />
                </template>
              </q-input>
              <div class="text-left q-mb-md">
                <q-btn
                  flat
                  no-caps
                  dense
                  label="Esqueceu a senha"
                  class="text-primary"
                />
              </div>
              <q-btn
                type="submit"
                color="primary"
                label="Entrar"
                no-caps
                class="full-width"
              />
            </q-form>
          </div>
          <div
            class="absolute-bottom-left q-mb-xs q-ml-sm"
            style="font-size: 10px; color: #97A1A8;"
          >
            ® Desenvolvido por Azape
          </div>
        </div>
        <div class="col-12 col-md-5 bg-image"></div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import CryptoJS from 'crypto-js';
import logo from '../assets/logo.png';

import { useRouter } from 'vue-router';

const router = useRouter();
const email = ref('');
const password = ref('');
const isPwd = ref(true);

const togglePassword = () => {
  isPwd.value = !isPwd.value;
};

const onSubmit = () => {
  const opt = {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      email: email.value,
      password: password.value,
    })
  }
  fetch('http://localhost:3333/proof/session', opt)
  .then((response) => response.json())
  .then((data) => {
    if(data.token !== undefined) {
      localStorage.setItem('token', data.token);
      localStorage.setItem('user', data.profile.name);
      router.push('/dashboard');
    }
  })
  .catch((err) => {
    console.error(err);
  });
};
</script>

<style scoped>
.login-form {
  width: 100%;
  max-width: 320px;
}

.logo {
  width: 120px;
  margin-bottom: 32px;
}

.info-card {
  width: 80%;
  max-width: 400px;
}

.q-btn.full-width {
  width: 100%;
}

.bg-red-3 {
  background-color: #ff7f7f;
}

.shadow-2 {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.bg-image {
  background-image: url('../assets/background_image_login.svg');
  background-size: cover;
  background-position: center;
}
</style>
