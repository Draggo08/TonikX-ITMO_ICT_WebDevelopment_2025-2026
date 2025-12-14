<template>
  <v-container class="fill-height" fluid>
    <v-row align="center" justify="center">
      <v-col cols="12" sm="8" md="4">
        <v-card>
          <v-card-title>Регистрация</v-card-title>
          <v-card-text>
            <v-form @submit.prevent="handleRegister">
              <v-text-field
                v-model="username"
                label="Имя пользователя"
                required
              ></v-text-field>
              <v-text-field
                v-model="password"
                label="Пароль"
                type="password"
                required
              ></v-text-field>
              <v-text-field
                v-model="passwordRetype"
                label="Повторите пароль"
                type="password"
                required
              ></v-text-field>
              <v-alert v-if="error" type="error" class="mt-2">{{ error }}</v-alert>
              <v-alert v-if="success" type="success" class="mt-2">Регистрация успешна! Теперь войдите.</v-alert>
              <v-btn type="submit" color="primary" block class="mt-4">Зарегистрироваться</v-btn>
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn text to="/login">Уже есть аккаунт? Войти</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { ref } from 'vue'
import { useAuth } from '../composables/useAuth'
import { useRouter } from 'vue-router'

export default {
  name: 'Register',
  setup() {
    const { register } = useAuth()
    const router = useRouter()
    const username = ref('')
    const password = ref('')
    const passwordRetype = ref('')
    const error = ref('')
    const success = ref(false)

    const handleRegister = async () => {
      error.value = ''
      success.value = false

      if (password.value !== passwordRetype.value) {
        error.value = 'Пароли не совпадают'
        return
      }

      const result = await register({
        username: username.value,
        password: password.value,
        password_retype: passwordRetype.value
      })

      if (result.success) {
        success.value = true
        setTimeout(() => {
          router.push('/login')
        }, 1500)
      } else {
        error.value = result.error?.username?.[0] || result.error?.password?.[0] || 'Ошибка регистрации'
      }
    }

    return { username, password, passwordRetype, error, success, handleRegister }
  }
}
</script>
