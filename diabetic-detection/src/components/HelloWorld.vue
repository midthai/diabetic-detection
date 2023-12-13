<template>
  <div class="upload-container">
    <!-- Card layout for image upload -->
    <div class="card">
      <!-- Logo at the top of the card -->
      <div class="logo-container">
        <img src="../assets/Colaboration_logo.png" alt="Center Logo" class="logo" />
      </div>
      
      <!-- Image preview -->
      <div v-if="imagePreview" class="image-preview-container">
        <img :src="imagePreview" alt="Image preview" class="image-preview" />
      </div>

      <!-- Upload input and button -->
      <div class="upload-input">
        <input type="file" @change="onFileChange">
        <button @click="uploadImage">Upload Image</button>
      </div>

      <!-- Display the response data -->
      <div v-if="uploadResponse" class="response">
       <p>Prediction:  {{ uploadResponse }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const selectedFile = ref(null);
const uploadResponse = ref('');
const imagePreview = ref(null);

const onFileChange = (e) => {
  selectedFile.value = e.target.files[0];
  imagePreview.value = URL.createObjectURL(selectedFile.value);
};

const uploadImage = async () => {
  if (!selectedFile.value) {
    alert('Please select a file first.');
    return;
  }

  const formData = new FormData();
  formData.append('file', selectedFile.value);

  try {
    const response = await axios.post('http://202.151.176.129:8000/uploadfile/', formData, {
      headers: {
        'Content-Type': 'multipart/form-data',
      },
    });
    uploadResponse.value = response.data.predicted_label;
    URL.revokeObjectURL(imagePreview.value);

    console.log('Server response:', response.data.predicted_label); // Log the server response
  } catch (error) {
    console.error('Error uploading image:', error);
    uploadResponse.value = 'Error uploading image: ' + error.message;
    URL.revokeObjectURL(imagePreview.value);
  }
};
</script>

<style>
.upload-container {
  display: flex;
  justify-content: center;
  padding-top: 50px;
}

.card {
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  border-radius: 5px;
  width: 350px;
  padding: 20px;
  background: white;
}

.logo-container {
  text-align: center;
  margin-bottom: 20px;
}

.logo {
  max-width: 100px;
  max-height: 100px;
}

.image-preview-container {
  text-align: center;
  margin-bottom: 20px;
}

.image-preview {
  max-width: 100%;
  max-height: 200px;
  border-radius: 5px;
}

.upload-input {
  text-align: center;
  margin-bottom: 20px;
}

button {
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  background-color: #008CBA;
  color: white;
  cursor: pointer;
  font-size: 1em;
}

button:hover {
  background-color: #005f73;
}

.response {
  margin-top: 20px;
  text-align: center;
  font-weight: bold;
}
</style>
