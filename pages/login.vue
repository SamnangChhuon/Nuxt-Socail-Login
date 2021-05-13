<template>
  <div>
    <h2 class="text-center">Login</h2>
    <hr />
    <b-alert v-if="errorMessage" show variant="danger">
      {{ errorMessage }}
    </b-alert>
    <b-alert v-if="$auth.$state.redirect" show>
      You have to login before accessing to
      <strong>{{ $auth.$state.redirect }}</strong>
    </b-alert>
    <b-row align-h="center" class="">
      <b-col md="4">
        <b-card bg-variant="light">
          <busy-overlay />
          <form @keydown.enter="login">
            <b-form-group label="Username">
              <b-input
                ref="username"
                v-model="username"
                placeholder="anything"
              />
            </b-form-group>

            <b-form-group label="Password">
              <b-input v-model="password" type="password" placeholder="123" />
            </b-form-group>

            <div class="text-center">
              <b-btn variant="primary" block @click="login"> Login </b-btn>
              <b-btn variant="secondary" block @click="localRefresh">
                Login with Refresh
              </b-btn>
            </div>
          </form>
        </b-card>
      </b-col>
      <b-col md="1" align-self="center">
        <div class="text-center">
          <b-badge pill> OR </b-badge>
        </div>
      </b-col>
      <b-col md="4" class="text-center">
        <b-card title="Social Login" bg-variant="light">
          <div v-for="s in strategies" :key="s.key" class="mb-2">
            <b-btn
              block
              :style="{ background: s.color }"
              class="login-button"
              @click="$auth.loginWith(s.key)"
            >
              Login with {{ s.name }}
            </b-btn>
          </div>
        </b-card>
      </b-col>
    </b-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: '123',
      error: null
    }
  },
  computed: {
    strategies: () => [
      { key: 'facebook', name: 'Facebook', color: '#3c65c4' },
    ],
    errorMessage() {
      const { error } = this
      if (!error || typeof error === 'string') {
        return error
      }
      let msg = ''
      if (error.message) {
        msg += error.message
      }
      if (error.errors) {
        msg += `(${JSON.stringify(error.errors)
          .replace(/[{}"[\]]/g, '')
          .replace(/:/g, ': ')
          .replace(/,/g, ' ')})`
      }
      return msg
    }
  },
  methods: {
    login() {
      this.error = null
      return this.$auth
        .loginWith('local', {
          data: {
            username: this.username,
            password: this.password
          }
        })
        .catch((err) => {
          // eslint-disable-next-line no-console
          console.error(err)
          this.error = err.response?.data
        })
    },
    localRefresh() {
      this.error = null
      return this.$auth
        .loginWith('localRefresh', {
          data: {
            username: this.username,
            password: this.password
          }
        })
        .catch((err) => {
          // eslint-disable-next-line no-console
          console.error(err)
          this.error = err.response?.data
        })
    },
  }
}
</script>
