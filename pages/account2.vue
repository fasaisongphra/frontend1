<template>
  <v-app class="app-container">
    <v-card class="gradient-card custom-rounded-buttom" height="200px" width="100%" flat></v-card>

    <v-app-bar app color="transparent" elevation="0" class="app-bar" fixed>
      <v-app-bar-nav-icon color="white">
        <v-icon>mdi-dots-horizontal</v-icon>
      </v-app-bar-nav-icon>
      <v-spacer></v-spacer>
      <v-btn icon color="white" @click="closeForm">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </v-app-bar>

    <div class="welcome-text">
      <div class="normal-text">Let's</div>
      <div class="bold-text">Create</div>
      <div class="bold-text">Your</div>
      <div class="bold-text">Account</div>
    </div>

    <v-main class="main-content">
      <v-container fill-height>
        <v-row justify="center" align="start">
          <v-col cols="12" sm="8" md="6">
            <v-form ref="form" v-model="valid">
              <!-- Form fields remain unchanged -->
              <v-text-field v-model="fullName" :rules="nameRules" label="Full Name" required class="custom-textfield">
                <template v-slot:prepend-inner>
                  <v-icon color="#2b524a">mdi-account</v-icon>
                </template>
              </v-text-field>

              <v-text-field v-model="student_id" label="Student ID" required class="custom-textfield">
                <template v-slot:prepend-inner>
                  <v-icon color="#2b524a">mdi-account</v-icon>
                </template>
              </v-text-field>

              <v-text-field v-model="email" :rules="emailRules" label="Email Address" required class="custom-textfield">
                <template v-slot:prepend-inner>
                  <v-icon color="#2b524a">mdi-email</v-icon>
                </template>
              </v-text-field>

              <v-text-field v-model="password" :rules="passwordRules" label="Password"
                :type="showPassword ? 'text' : 'password'" required class="custom-textfield">
                <template v-slot:prepend-inner>
                  <v-icon color="#2b524a">mdi-lock</v-icon>
                </template>
                <template v-slot:append>
                  <v-icon @click="showPassword = !showPassword" color="#2b524a">
                    {{ showPassword ? 'mdi-eye' : 'mdi-eye-off' }}
                  </v-icon>
                </template>
              </v-text-field>

              <v-text-field v-model="retypePassword" :rules="[...passwordRules, passwordMatch]" label="Retype Password"
                :type="showRetypePassword ? 'text' : 'password'" required class="custom-textfield">
                <template v-slot:prepend-inner>
                  <v-icon color="#2b524a">mdi-lock-check</v-icon>
                </template>
                <template v-slot:append>
                  <v-icon @click="showRetypePassword = !showRetypePassword" color="#2b524a">
                    {{ showRetypePassword ? 'mdi-eye' : 'mdi-eye-off' }}
                  </v-icon>
                </template>
              </v-text-field>

              <v-checkbox v-model="agreeTerms" :rules="[v => !!v || 'You must agree to continue!']"
                label="I agree to the Terms & Privacy" required class="custom-checkbox"></v-checkbox>

              <v-btn block @click="signUp" class="gradient-btn mt-4" height="50">
                Sign Up
              </v-btn>

              <div class="text-center mt-4">
                Already have an account?
                <router-link to="/signin" class="font-weight-bold white--text">Sign In</router-link>
              </div>
            </v-form>
          </v-col>
        </v-row>
        <div class="text-center pa-4">
          <v-dialog v-model="fail" width="auto">
            <v-card max-width="400" prepend-icon="mdi-message-alert-outline"
              :text="insertStatus"
              title="Sign Up">
              <template v-slot:actions>
                <v-btn class="ms-auto" text="Ok" @click="fail = false"></v-btn>
              </template>
            </v-card>
          </v-dialog>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from 'axios'
export default {
  name: 'RegistrationForm',
  data: () => ({
    insertStatus: '',
    fail: false,
    valid: true,
    fullName: '',
    student_id: '',
    email: '',
    password: '',
    retypePassword: '',
    agreeTerms: false,
    showPassword: false,
    showRetypePassword: false,
    nameRules: [
      v => !!v || 'Name is required',
      v => v.length <= 50 || 'Name must be less than 50 characters',
    ],
    emailRules: [
      v => !!v || 'E-mail is required',
      v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
    ],
    passwordRules: [
      v => !!v || 'Password is required',
      v => v.length >= 8 || 'Password must be at least 8 characters',
    ],
  }),
  methods: {
    async signUp() {
      console.log('SignUp')
      const student = {
        fullname: this.fullName,
        student_id: this.student_id,
        username: this.email,
        password: this.password
      }

      if (this.$refs.form.validate()) {
        console.log('Sign up successful')
        try {
          const response = await axios.post('http://localhost:7000/insertStudent', student);
          const status = response.data
          
          console.log('status = ', status)
          if (status.insert === "ok") {
            this.fail = true
            console.log('insert ok')
            this.insertStatus = "Sign up successful!"
          }
          if (status.ok === 0) {
            this.fail = true
            console.log('error insert')
            this.insertStatus = "Fail to sign up."
          }
        } catch (error) {
          console.log(error.error)
        }
      } else {
        console.log('Please fill all required fields correctly')
      }
    },
    passwordMatch(value) {
      return value === this.password || 'Passwords do not match'
    },
    closeForm() {
      console.log('Close button clicked')
    },
  },
}
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow: hidden;
}

.gradient-card {
  background: linear-gradient(90deg, #4c8479 0%, #2b5f56 100%);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 0;
}

.app-bar {
  position: fixed !important;
  top: 0;
  z-index: 3;
}


.custom-rounded-buttom {
  border-bottom-left-radius: 40px !important;
  border-bottom-right-radius: 40px !important;
}

.welcome-text {
  position: fixed;
  top: 64px;
  /* Adjust this value based on your app-bar height */
  left: 16px;
  color: white;
  line-height: 1;
  z-index: 1;
}

.normal-text {
  font-size: 30px;
  font-weight: normal;
}

.bold-text {
  font-size: 30px;
  font-weight: bold;
}

.main-content {
  position: relative;
  z-index: 2;
  flex: 1;
  overflow-y: auto;
  padding-top: 220px;
  /* Adjust this value to fine-tune the space between v-card and form */
}

.gradient-btn {
  background: linear-gradient(90deg, #4c8479 0%, #2b5f56 100%) !important;
  color: white !important;
  border: none !important;
  border-radius: 20px !important;
}

.gradient-btn:hover {
  opacity: 0.9;
}

.custom-textfield {
  background-color: rgba(255, 255, 255, 0.9) !important;
}

.custom-textfield>>>.v-input__control {
  border: 2px solid #2b524a !important;
  border-radius: 20px !important;
}

.custom-textfield>>>.v-input__slot {
  background-color: transparent !important;
  border-radius: 20px !important;
  padding: 0 15px !important;
}

.custom-textfield>>>.v-text-field__details {
  display: none;
}

.custom-textfield>>>.v-label {
  left: 15px !important;
}

.custom-checkbox>>>.v-input__slot {
  border: none !important;
}

.v-application {
  background-color: transparent !important;
}
</style>