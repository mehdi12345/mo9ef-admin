<script>
import { required, email } from "vuelidate/lib/validators";

import { authMethods, notificationMethods } from "@/state/helpers";

export default {
  data() {
    return {
      email: "",
      password: "",
      submitted: false,
    };
  },
  computed: {
    notification() {
      return this.$store ? this.$store.state.notification : null;
    },
  },
  created() {
    document.body.classList.add("auth-body-bg");
  },
  validations: {
    email: { required, email },
    password: { required },
  },
  methods: {
    ...authMethods,
    ...notificationMethods,
    // Try to log the user in with the username
    // and password they provided.
    tryToLogIn() {
      this.submitted = true;
      // stop here if form is invalid
      this.$v.$touch();

      if (this.$v.$invalid) {
        return;
      } else {
        this.tryingToLogIn = true;
        // Reset the authError if it existed.
        this.authError = null;
        return (
          this.logIn({
            email: this.email,
            password: this.password,
          })
            // eslint-disable-next-line no-unused-vars
            .then((token) => {
              this.tryingToLogIn = false;
              this.isAuthError = false;
              // Redirect to the originally requested page, or to the home page
              this.$router.push(this.$route.query.redirectFrom || { name: "home" });
            })
            .catch((error) => {
              this.tryingToLogIn = false;
              this.authError = error ? error : "";
              this.isAuthError = true;
            })
        );
      }
    },
  },
};
</script>

<template>
  <div>
    <div class="home-btn d-none d-sm-block">
      <a href="/">
        <i class="mdi mdi-home-variant h2 text-white"></i>
      </a>
    </div>
    <div>
      <div class="container-fluid p-0">
        <div class="row no-gutters">
          <div class="col-lg-4">
            <div
              class="authentication-page-content p-4 d-flex align-items-center min-vh-100"
            >
              <div class="w-100">
                <div class="row justify-content-center">
                  <div class="col-lg-9">
                    <div>
                      <div class="text-center">
                        <div>
                          <a href="/" class="logo">
                            <img src="@/assets/images/logo.png" height="120" alt="logo" />
                          </a>
                        </div>

                        <h4 class="font-size-18 mt-4">Content de te revoir!</h4>
                        <p class="text-muted">
                          Connectez-vous pour continuer à Mo9ef.ma.
                        </p>
                      </div>

                      <b-alert
                        variant="danger"
                        class="mt-3"
                        v-if="notification.message"
                        show
                        dismissible
                        >{{ notification.message }}</b-alert
                      >

                      <div class="p-2 mt-5">
                        <form class="form-horizontal" @submit.prevent="tryToLogIn">
                          <div class="form-group auth-form-group-custom mb-4">
                            <i class="ri-mail-line auti-custom-input-icon"></i>
                            <label for="email">Address Email</label>
                            <input
                              type="email"
                              v-model="email"
                              class="form-control"
                              id="email"
                              placeholder="Enter email"
                              :class="{ 'is-invalid': submitted && $v.email.$error }"
                            />
                            <div
                              v-if="submitted && $v.email.$error"
                              class="invalid-feedback"
                            >
                              <span v-if="!$v.email.required"
                                >Address Email est obligatoire</span
                              >
                              <span v-if="!$v.email.email"
                                >Please enter valid email.</span
                              >
                            </div>
                          </div>

                          <div class="form-group auth-form-group-custom mb-4">
                            <i class="ri-lock-2-line auti-custom-input-icon"></i>
                            <label for="userpassword">Mot de pass</label>
                            <input
                              v-model="password"
                              type="password"
                              class="form-control"
                              id="userpassword"
                              placeholder="Enter password"
                              :class="{ 'is-invalid': submitted && $v.password.$error }"
                            />
                            <div
                              v-if="submitted && !$v.password.required"
                              class="invalid-feedback"
                            >
                              Mot de pass est obligatoire
                            </div>
                          </div>

                          <div class="mt-4 text-center">
                            <button
                              class="btn btn-primary w-md waves-effect waves-light"
                              type="submit"
                            >
                              S'identifier
                            </button>
                          </div>
                        </form>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-8">
            <div class="authentication-bg">
              <div class="bg-overlay"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
