<template>
  <div class="upload-page">
    <!-- Instagram-style header -->
    <header class="upload-header">
      <img
        src="https://upload.wikimedia.org/wikipedia/commons/2/2a/Instagram_logo.svg"
        alt="Instagram"
        class="logo"
      />
      <h2>New Post</h2>
    </header>

    <!-- Upload Form -->
    <form class="upload-form" @submit.prevent="uploadMedia" enctype="multipart/form-data">
      <!-- Title Input -->
      <div class="form-group">
        <label for="title">Title</label>
        <input
          type="text"
          v-model="video.title"
          id="title"
          placeholder="Write a caption..."
          required
        />
      </div>

      <!-- Description Input -->
      <div class="form-group">
        <label for="description">Description</label>
        <textarea
          v-model="video.description"
          id="description"
          placeholder="Add some details..."
        ></textarea>
      </div>

      <!-- Hashtags -->
      <div class="form-group">
        <label for="hashtags">Hashtags</label>
        <input
          type="text"
          v-model="video.hashtags"
          id="hashtags"
          placeholder="#summer #travel"
        />
      </div>

      <!-- Media Upload -->
      <div class="form-group file-upload">
        <label for="video_file">Upload Media</label>
        <div class="upload-box" @click="triggerFileInput">
          <span v-if="!videoFile">Click to select video or image</span>
          <span v-else>{{ videoFile.name }}</span>
          <input
            type="file"
            ref="fileInput"
            id="video_file"
            accept="video/*,image/*"
            @change="handleFileUpload"
            style="display: none"
            required
          />
        </div>
        <div class="media-preview" v-if="mediaPreview">
          <video
            v-if="isVideo"
            :src="mediaPreview"
            controls
            class="preview-video"
          ></video>
          <img
            v-else
            :src="mediaPreview"
            alt="Preview"
            class="preview-image"
          />
        </div>
      </div>

      <!-- Upload Button -->
      <button type="submit" class="btn-upload">Share</button>
    </form>

    <!-- Feedback Message -->
    <div v-if="message" :class="['message', success ? 'success' : 'error']">
      {{ message }}
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      video: {
        title: "",
        description: "",
        hashtags: "",
      },
      videoFile: null,
      mediaPreview: null,
      isVideo: false,
      message: "",
      success: false,
    };
  },
  methods: {
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      this.videoFile = file;
      this.isVideo = file.type.startsWith("video");
      this.mediaPreview = URL.createObjectURL(file);
    },
    async uploadMedia() {
      if (!this.videoFile) {
        this.message = "Please select a file to upload.";
        this.success = false;
        return;
      }

      const formData = new FormData();
      formData.append("title", this.video.title);
      formData.append("description", this.video.description);
      formData.append("hashtags", this.video.hashtags);
      formData.append("video_file", this.videoFile);

      const csrfToken = document
        .querySelector('meta[name="csrf-token"]')
        ?.getAttribute("content");

      try {
        const res = await axios.post("http://localhost:8000/videos/upload/", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
            ...(csrfToken && { "X-CSRFToken": csrfToken }),
          },
        });

        this.message = `Post uploaded successfully: ${res.data.title}`;
        this.success = true;

        this.video = { title: "", description: "", hashtags: "" };
        this.videoFile = null;
        this.mediaPreview = null;
      } catch (err) {
        console.error(err);
        this.message = "Failed to upload. Please try again.";
        this.success = false;
      }
    },
  },
};
</script>

<style scoped>
.upload-page {
  max-width: 600px;
  margin: 0 auto;
  padding: 32px 20px;
  font-family: 'Segoe UI', sans-serif;
  background: #fff;
  color: #262626;
}

/* Header */
.upload-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 24px;
  border-bottom: 1px solid #dbdbdb;
  padding-bottom: 12px;
}

.upload-header .logo {
  height: 30px; /* Adjust logo height */
  width: auto;  /* Ensure aspect ratio is preserved */
  margin-right: 8px; /* Space between logo and title */
}

/* Form */
.upload-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* Form Group */
.form-group label {
  display: block;
  margin-bottom: 6px;
  font-weight: 600;
}

input,
textarea {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #dbdbdb;
  border-radius: 6px;
  font-size: 14px;
  background: #fafafa;
  color: #262626;
}

textarea {
  resize: vertical;
  min-height: 80px;
}

/* File Upload */
.file-upload .upload-box {
  border: 2px dashed #dbdbdb;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  cursor: pointer;
  color: #999;
  background: #fafafa;
  transition: background-color 0.3s ease;
}

.upload-box:hover {
  background-color: #f0f0f0;
}

/* Media Preview */
.media-preview {
  margin-top: 15px;
}

.preview-video,
.preview-image {
  max-width: 100%;
  border-radius: 8px;
  margin-top: 10px;
}

/* Upload Button */
.btn-upload {
  background-color: #3897f0;
  color: #fff;
  padding: 12px;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn-upload:hover {
  background-color: #2a78c2;
}

/* Feedback */
.message {
  margin-top: 20px;
  padding: 12px;
  border-radius: 6px;
  font-size: 14px;
  text-align: center;
}

.success {
  background: #d4edda;
  color: #155724;
}

.error {
  background: #f8d7da;
  color: #721c24;
}
</style>

