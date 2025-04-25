<template>
  <div class="auth-container">
    <div class="auth-card">
      <!-- Instagram logo and text -->
      <div class="auth-logo">
        <img
          src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png"
          alt="Instagram Icon"
          class="logo-icon"
        />
        <h2 class="auth-subtitle">
          Sign up to see photos and videos from your friends.
        </h2>
      </div>

      <!-- Sign-up form -->
      <form @submit.prevent="signup" class="auth-form">
        <input
          v-model="email"
          type="text"
          placeholder="Mobile Number or Email"
          class="auth-input"
        />
        <input
          v-model="fullName"
          type="text"
          placeholder="Full Name"
          class="auth-input"
        />
        <input
          v-model="username"
          type="text"
          placeholder="Username"
          class="auth-input"
        />
        <input
          v-model="password"
          type="password"
          placeholder="Password"
          class="auth-input"
        />
        <button type="submit" class="auth-button">Sign Up</button>
      </form>

      <!-- Terms -->
      <p class="terms-text">
        By signing up, you agree to our
        <a href="#" class="terms-link">Terms</a>,
        <a href="#" class="terms-link">Data Policy</a> and
        <a href="#" class="terms-link">Cookies Policy</a>.
      </p>

      <!-- Error message -->
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    </div>

    <!-- Bottom switch -->
    <div class="auth-switch">
      Have an account?
      <router-link to="/login" class="auth-link">Log In</router-link>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "UserSignup",
  data() {
    return {
      email: "",
      fullName: "",
      username: "",
      password: "",
      errorMessage: "",
    };
  },
  methods: {
    async signup() {
      try {
        const response = await axios.post("http://localhost:8000/api/signup/", {
          email: this.email,
          full_name: this.fullName,
          username: this.username,
          password: this.password,
        });

        if (response.data.success) {
          this.$router.push("/login");
        } else {
          this.errorMessage = "Signup failed, please try again!";
        }
      } catch (error) {
        console.error("Signup error", error);
        this.errorMessage =
          error.response?.data?.message || "An error occurred. Please try again.";
      }
    },
  },
};
</script>

<style scoped>
/* Main container */
.auth-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem 1rem;
  min-height: 100vh;
  background: linear-gradient(135deg, #fdfbfb, #ebedee);
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

/* Signup card */
.auth-card {
  background: #ffffff;
  padding: 2.5rem 3rem;
  border: 1px solid #dbdbdb;
  border-radius: 12px;
  max-width: 350px;
  width: 100%;
  text-align: center;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
}

/* Logo area */
.auth-logo {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 1rem;
}

.logo-icon {
  width: 60px;
  height: 60px;
  margin-bottom: 0.5rem;
}

.logo-text {
  width: 150px;
  margin-bottom: 1rem;
}

.auth-subtitle {
  font-size: 14px;
  color: #8e8e8e;
  font-weight: 500;
  margin-bottom: 1.5rem;
  line-height: 1.4;
}

/* Form */
.auth-form {
  display: flex;
  flex-direction: column;
}

.auth-input {
  background-color: #fafafa;
  border: 1px solid #dbdbdb;
  padding: 10px;
  font-size: 13px;
  border-radius: 6px;
  margin-bottom: 8px;
  outline: none;
  transition: border 0.2s ease-in-out;
}

.auth-input:focus {
  border-color: #a8a8a8;
  background-color: #fff;
}

/* Button */
.auth-button {
  background: linear-gradient(to right, #ff5858, #f857a6);
  border: none;
  color: white;
  padding: 10px;
  font-weight: 600;
  font-size: 14px;
  border-radius: 8px;
  cursor: pointer;
  margin-top: 10px;
  transition: background 0.3s ease;
}

.auth-button:hover {
  background: linear-gradient(to right, #f857a6, #ff5858);
}

/* Terms */
.terms-text {
  font-size: 11px;
  color: #999;
  margin-top: 1.5rem;
  line-height: 1.4;
}

.terms-link {
  color: #00376b;
  font-weight: 600;
  text-decoration: none;
}

.terms-link:hover {
  text-decoration: underline;
}

/* Error */
.error-message {
  margin-top: 12px;
  color: #ed4956;
  background: #ffe6e6;
  padding: 6px;
  font-size: 12px;
  border-radius: 4px;
}

/* Login switch */
.auth-switch {
  margin-top: 1.5rem;
  padding: 1rem;
  font-size: 14px;
  text-align: center;
  background: #ffffff;
  border: 1px solid #dbdbdb;
  border-radius: 12px;
  max-width: 350px;
  width: 100%;
}

.auth-link {
  color: #0095f6;
  font-weight: 600;
  text-decoration: none;
  margin-left: 4px;
}

.auth-link:hover {
  text-decoration: underline;
}

/* Responsive */
@media (max-width: 400px) {
  .auth-card,
  .auth-switch {
    padding: 1.5rem;
  }

  .logo-text {
    width: 120px;
  }

  .logo-icon {
    width: 50px;
    height: 50px;
  }
}
</style>

