<template>
  <section class="container">
    <div>
      <app-logo/>
<form v-if="!$auth.user" @submit.prevent="register">
      <p class="error" v-if="formError">{{ formError }}</p>
      <p>Register to create an account</p>
      <p>Username: <input v-validate="{ required: true, min: 6 }" type="text" v-model="formUsername" name="username" /></p>
      <p>Email: <input v-validate="'required|email'" type="text" v-model="formEmail" name="email" /></p>
      <!-- If it has an email error, display the first message associated with it. -->
        <span v-show="errors.has('email')"><b-badge variant="danger">{{ errors.first('email') }}</b-badge></span>

      <!-- Personal info -->

        <p>First Name: <input v-validate="'required'" type="text" v-model="formFirstName" name="firstName" /></p>
        <!-- FirstName error display -->
        <span v-show="errors.has('firstName')"><b-badge variant="danger">{{ errors.first('firstName') }}</b-badge></span>

        <p>Last Name: <input v-validate="'required'" type="text" v-model="formLastName" name="lastName" /></p>
        <!-- LastName error display -->
        <span v-show="errors.has('lastName')"><b-badge variant="danger">{{ errors.first('lastName') }}</b-badge></span>

      <!-- /Personal info -->

      <p>Password: <input v-validate="{ required: true, min: 6 }" type="password" v-model="formPassword" name="password" /></p>
      <span v-show="errors.has('password')"><b-badge variant="danger">{{ errors.first('password') }}</b-badge></span><br/>
      <p>Confirm Password: <input v-validate="{ required: true, min: 6, confirmed:'password' }" type="password" name="confirmPassword" /></p>
      <!-- If it has an email error, display the first message associated with it. -->
        <span v-show="errors.has('confirmPassword')"><b-badge variant="danger">{{ errors.first('confirmPassword') }}</b-badge></span><br/>
      <button :disabled="errors.any()" type="submit">Register</button>
    </form>

    <p><nuxt-link to="/">return to home</nuxt-link></p>
  </div>
  </section>
</template>

<script>
import AppLogo from '~/components/AppLogo.vue'
import axios from 'axios'
import toast from '@nuxtjs/toast'

export default {
  auth: false,
  components: {
    AppLogo
  },
    data() {
    return {
      formError: null,
      formUsername: '',
      formPassword: '',
      formEmail:'',
      formFirstName:'',
      formLastName:''
    }
  },
  methods: {
async register(data) {

  try {
   await this.$axios.$post('http://localhost:1337/auth/local/register', {
    username: this.formUsername,
    password: this.formPassword,
    email: this.formEmail,
    firstName: this.formFirstName,
    lastName: this.formLastName
  }).then(response => {
    console.log(response);
    this.$auth.setToken('local', response.jwt);
  })
  this.$toast.success('Success! You have been registered.', {duration: 2000});
  await this.$auth.loginWith('local', {
  data: {
    identifier: this.formUsername,
    password: this.formPassword
  }

})


  } catch(e){
    console.log(e);
     this.$toast.error(`Hmmm... Something isn't right. Did you already register?`, {duration: 2000});
  }
  }

  }
}
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
