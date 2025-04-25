<template>
  <div class="insta-home">
    <!-- Navbar -->
    <nav class="navbar">
      <div class="logo-container">
        <img
          src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png"
          alt="Instagram"
          class="logo"
        />
      </div>
      <div class="nav-icons">
        <span class="icon">🏠</span>
        <span class="icon">🔍</span>
        <span class="icon">❤️</span>
        <span class="icon">👤</span>
      </div>
    </nav>

    <!-- Feed -->
    <main class="feed">
      <div v-for="post in filteredPosts" :key="post.id" class="post-card">
        <!-- Header -->
        <div class="post-header">
          <img src="https://i.pravatar.cc/40" alt="avatar" class="avatar" />
          <span class="username">{{ post.username || 'user123' }}</span>
        </div>

        <!-- Image -->
        <img
          v-if="post.image_file"
          class="post-image"
          :src="post.image_file"
          alt="Post"
        />

        <!-- Actions -->
        <div class="post-actions">
          <button @click="likePost(post)" title="Like" class="action-btn">❤️</button>
          <button @click="sharePost(post)" title="Share" class="action-btn">📤</button>
        </div>

        <!-- Caption -->
        <div class="post-caption">
          <strong>{{ post.title }}</strong> — {{ post.description }}
        </div>

        <!-- Comments Display -->
        <div class="comments-list">
          <div v-for="(comment, index) in post.comments" :key="index" class="comment">
            <strong>{{ comment.username }}</strong> {{ comment.text }}
          </div>
        </div>

        <!-- Comment Box -->
        <div class="comment-box">
          <input
            v-model="post.newComment"
            type="text"
            placeholder="Add a comment..."
            @keyup.enter="submitComment(post)"
          />
          <button @click="submitComment(post)">Post</button>
        </div>
      </div>

      <!-- Error -->
      <div v-if="error" class="error">{{ error }}</div>

      <!-- Share Feedback -->
      <div v-if="copied" class="share-feedback">Link copied to clipboard</div>
    </main>
  </div>
</template>

<script>
import apiClient from "../axios";

export default {
  data() {
    return {
      posts: [],
      error: null,
      copied: false,
    };
  },
  computed: {
    filteredPosts() {
      return this.posts;
    },
  },
  methods: {
    async fetchPosts() {
      try {
        const res = await apiClient.get("http://localhost:8000/api/videos/");
        // Initialize newComment and comments array per post
        this.posts = res.data.map((post) => ({
          ...post,
          comments: [],
          newComment: "",
        }));
      } catch (e) {
        console.error("Error fetching posts:", e);
        this.error = "Could not load posts.";
      }
    },
    likePost(post) {
      post.likes = (post.likes || 0) + 1;
    },
    sharePost(post) {
      const link = post.image_file;
      if (link) {
        navigator.clipboard.writeText(link);
        this.copied = true;
        setTimeout(() => {
          this.copied = false;
        }, 2000);
      }
    },
    submitComment(post) {
      if (post.newComment.trim()) {
        post.comments.push({ username: "you", text: post.newComment.trim() });
        post.newComment = "";
      }
    },
  },
  created() {
    this.fetchPosts();
  },
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  font-family: "Segoe UI", sans-serif;
  background-color: #fafafa;
}

.insta-home {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.navbar {
  width: 100%;
  background-color: #fff;
  padding: 12px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #dbdbdb;
  position: sticky;
  top: 0;
  z-index: 10;
}

.logo-container {
  display: flex;
  align-items: center;
}

.logo {
  height: 32px;
}

.nav-icons {
  display: flex;
  gap: 20px;
}

.icon {
  font-size: 20px;
  cursor: pointer;
}

.feed {
  max-width: 600px;
  width: 100%;
  margin-top: 20px;
  padding: 0 16px;
}

.post-card {
  background: #fff;
  border: 1px solid #dbdbdb;
  border-radius: 8px;
  margin-bottom: 24px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
}

.post-header {
  display: flex;
  align-items: center;
  padding: 12px;
  border-bottom: 1px solid #eee;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  margin-right: 12px;
}

.username {
  font-weight: 600;
  font-size: 14px;
}

.post-image {
  width: 100%;
  max-height: 400px;
  object-fit: cover;
  background-color: #000;
}

.post-actions {
  padding: 10px 12px;
  display: flex;
  gap: 16px;
}

.action-btn {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  transition: transform 0.2s;
}

.action-btn:hover {
  transform: scale(1.15);
}

.post-caption {
  padding: 0 12px 12px 12px;
  font-size: 14px;
}

.post-caption strong {
  font-weight: 600;
}

.comments-list {
  padding: 0 12px;
  font-size: 13px;
  color: #262626;
}

.comment {
  margin-top: 6px;
}

.comment strong {
  margin-right: 4px;
  font-weight: 600;
}

/* Comment Input */
.comment-box {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  border-top: 1px solid #efefef;
}

.comment-box input {
  flex: 1;
  border: none;
  background: none;
  font-size: 14px;
  padding: 6px;
  outline: none;
}

.comment-box button {
  border: none;
  background: none;
  color: #0095f6;
  font-weight: 600;
  cursor: pointer;
  padding-left: 8px;
  font-size: 14px;
}

.comment-box button:disabled {
  color: #b2dffc;
  cursor: default;
}

.error {
  color: red;
  text-align: center;
  margin-top: 20px;
}

.share-feedback {
  position: fixed;
  bottom: 30px;
  background-color: #262626;
  color: white;
  padding: 10px 18px;
  border-radius: 20px;
  font-size: 14px;
  animation: fadeInOut 2s ease-in-out;
}

@keyframes fadeInOut {
  0% {
    opacity: 0;
    transform: translateY(10px);
  }
  20% {
    opacity: 1;
    transform: translateY(0);
  }
  80% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translateY(10px);
  }
}
</style>






