<template>
  <div class="auth-container">
    <div class="auth-card">
      <!-- Instagram logo and title -->
      <div class="auth-logo">
        <img src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png" alt="Instagram Logo" class="logo" />
        <h1 class="auth-title">Log In</h1>
      </div>

      <!-- Login form -->
      <form @submit.prevent="login">
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
        <button type="submit" class="auth-button">Log In</button>
      </form>

      <!-- Error message -->
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

      <!-- Sign-up link -->
      <p class="auth-switch">
        Don’t have an account?
        <router-link to="/signup" class="auth-link">Sign Up</router-link>
      </p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "UserLogin",
  data() {
    return {
      username: "",
      password: "",
      errorMessage: "",
      csrfToken: "",
    };
  },
  methods: {
    async fetchCsrfToken() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/csrf/", {
          withCredentials: true,
        });

        if (response.data.csrfToken) {
          this.csrfToken = response.data.csrfToken;
        } else {
          throw new Error("CSRF token missing in response.");
        }
      } catch (error) {
        console.error("CSRF fetch error:", error);
        this.errorMessage = "Failed to fetch CSRF token. Please refresh.";
      }
    },

    async login() {
      try {
        if (!this.csrfToken) {
          await this.fetchCsrfToken();
        }

        const response = await axios.post(
          "http://127.0.0.1:8000/api/login/",
          {
            username: this.username,
            password: this.password,
          },
          {
            headers: {
              "X-CSRFToken": this.csrfToken,
            },
            withCredentials: true,
          }
        );

        if (response.status === 200) {
          localStorage.setItem("access_token", response.data.access_token);
          this.errorMessage = "";
          alert("Login successful!");
          this.$router.push("/dashboard");
        } else {
          this.errorMessage = "Login failed: " + response.data.error;
        }
      } catch (error) {
        console.error("Login error:", error);
        this.errorMessage =
          error.response?.data?.error || "Login failed. Please try again.";
      }
    },
  },
  created() {
    this.fetchCsrfToken();
  },
};
</script>

<style scoped>
/* Container background with Instagram-style minimalism */
.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #fafafa;
}

/* Card styling */
.auth-card {
  background: #ffffff;
  padding: 2.5rem 3rem;
  border: 1px solid #dbdbdb;
  border-radius: 12px;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.03);
  text-align: center;
  max-width: 350px;
  width: 100%;
}

/* Logo and title */
.auth-logo {
  margin-bottom: 1.5rem;
}

.logo {
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}

.auth-title {
  font-size: 26px;
  font-weight: 600;
  color: #262626;
  margin-bottom: 1.5rem;
}

/* Input styling */
.auth-input {
  width: 100%;
  padding: 10px;
  margin: 6px 0;
  font-size: 14px;
  border: 1px solid #dbdbdb;
  border-radius: 6px;
  background-color: #fafafa;
  color: #262626;
  outline: none;
}

.auth-input:focus {
  border-color: #a8a8a8;
  background-color: #ffffff;
}

/* Button styling */
.auth-button {
  width: 100%;
  padding: 10px;
  margin-top: 12px;
  background-color: #0095f6;
  color: #fff;
  font-weight: bold;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  font-size: 14px;
}

.auth-button:hover {
  background-color: #0077c2;
}

/* Error message */
.error-message {
  margin-top: 10px;
  color: #ed4956;
  font-size: 13px;
}

/* Switch (Sign up) */
.auth-switch {
  margin-top: 2rem;
  font-size: 14px;
  color: #8e8e8e;
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

/* Responsive design */
@media (max-width: 400px) {
  .auth-card {
    padding: 2rem 1.5rem;
  }

  .logo {
    width: 80px;
    height: 80px;
  }

  .auth-title {
    font-size: 22px;
  }
}
</style>

