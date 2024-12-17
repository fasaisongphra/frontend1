<template>
  <v-app>
    <v-app-bar 
      color="teal" 
      height="25vh"
      class="rounded-b-xl"
      elevation="0"
    >
      <v-btn 
        icon="mdi-arrow-left" 
        color="white" 
        class="back-button"
      ></v-btn>
      
      <div class="profile-section">
        <div class="text-center">
          <div class="text-h6 text-white mb-6">Student Profile</div>
          
          <v-avatar 
            size="80" 
            class="mb-4 white"
          >
            <v-img
              :src="selectedImage"
              alt="Profile Picture"
              cover
            ></v-img>

            <!-- Hidden file input -->
            <input
              type="file"
              ref="fileInput"
              accept="image/*"
              @change="onFileSelected"
              style="display: none"
            >

            <!-- Edit button to trigger file input -->
            <v-btn
              icon="mdi-pencil"
              size="small"
              class="avatar-edit-btn"
              color="white"
              variant="flat"
              @click="$refs.fileInput.click()"
            ></v-btn>
          </v-avatar>
          
          <div class="white--text text-h6 mb-1">Fasai Songphra</div>
          <div class="white--text text-subtitle-2">Fasai@gmail.com</div>
        </div>
      </div>
    </v-app-bar>

    <v-main class="bg-grey-lighten-4">
      <v-container>
        <!-- General Settings Section -->
        <!-- <div class="text-subtitle-1 mb-2">General Settings</div> -->
        <v-card flat class="mb-4"><br><br>
          <v-list>
            <v-list-item>
              <template v-slot:prepend>
                <v-icon>mdi-theme-light-dark</v-icon>
              </template>
              <v-list-item-title>Mode</v-list-item-title>
              <v-list-item-subtitle>Dark & Light</v-list-item-subtitle>
              <template v-slot:append>
                <v-switch color="primary"></v-switch>
              </template>
            </v-list-item>

            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-key</v-icon>
              </template>
              <v-list-item-title>Change Password</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>

            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-translate</v-icon>
              </template>
              <v-list-item-title>Language</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>
          </v-list>
        </v-card>

        <!-- Information Section -->
        <div class="text-subtitle-1 mb-2">Information</div>
        <v-card flat>
          <v-list>
            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-information</v-icon>
              </template>
              <v-list-item-title>About App</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>

            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-file-document</v-icon>
              </template>
              <v-list-item-title>Terms & Conditions</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>

            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-shield-check</v-icon>
              </template>
              <v-list-item-title>Privacy Policy</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>

            <v-list-item link>
              <template v-slot:prepend>
                <v-icon>mdi-share-variant</v-icon>
              </template>
              <v-list-item-title>Share This App</v-list-item-title>
              <template v-slot:append>
                <v-icon>mdi-chevron-right</v-icon>
              </template>
            </v-list-item>
          </v-list>
        </v-card>
      </v-container>
    </v-main>

    <!-- เลือกรูปโปรไฟล์ -->
    <v-dialog v-model="dialog.visible" max-width="500px">
      <v-card>
        <v-card-title class="headline">Change Profile Picture</v-card-title>
        <v-card-text>
          <!-- Display the selected image before confirming -->
          <v-img 
            :src="newImagePreview" 
            alt="New Profile Picture Preview"
            class="mb-3"
            max-width="100%"
          ></v-img>
          
  
        </v-card-text>
        <v-card-actions>
          <v-btn color="blue darken-1" text @click="cancelChange">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="confirmChange">Confirm</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Snackbar for success message -->
    <v-snackbar v-model="snackbar.visible" color="success" top>
      Image successfully updated!
      <template v-slot:action="{ attrs }">
        <v-btn color="white" text v-bind="attrs" @click="snackbar.visible = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      selectedImage: localStorage.getItem('profileImage') || 'xampp/htdocs/project_vue_2567/myApp/images/profile1.png', // Default or saved image from localStorage
      dialog: {
        visible: false,
      },
      snackbar: {
        visible: false,
      },
      newImage: null,
      newImagePreview: '',
    };
  },
  methods: {
    // Handle file selection
    onFileSelected(event) {
      const file = event.target.files[0];
      if (file) {
        this.previewImage(file);
      }
    },
    onFileChange(file) {
      this.previewImage(file);
    },
    previewImage(file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        this.newImagePreview = e.target.result;
        this.dialog.visible = true; // Show the dialog with preview
      };
      reader.readAsDataURL(file);
    },
    cancelChange() {
      this.dialog.visible = false;
      this.newImagePreview = '';
    },
    confirmChange() {
      if (this.newImage) {
        this.selectedImage = this.newImagePreview; // Set new image as profile picture
        localStorage.setItem('profileImage', this.newImagePreview); // Save the new image URL to localStorage
        this.snackbar.visible = true; // Show success message
        this.dialog.visible = false; // Close dialog
      }
    },
  },
};
</script>

<style scoped>
/* Existing styles for the profile and layout */
.back-button {
  position: absolute;
  top: 16px;
  left: 16px;
  z-index: 1;
}

.profile-section {
  width: 100%;
  padding-top: 2rem;
}

.avatar-edit-btn {
  position: absolute;
  right: -5px;
  bottom: 0;
  background-color: #0158c8 !important;
  border: 2px solid white !important;
  cursor: pointer;
  z-index: 1;
}

.avatar-edit-btn:hover {
  opacity: 0.9;
}

.v-main {
  padding-top: calc(25vh + 16px) !important;
  background-color: #f5f5f5;
}

.white--text {
  color: white !important;
}

.v-avatar.white {
  background-color: #f5f5f5 !important;
}

.v-container {
  max-width: 100%;
  width: 100%;
  padding: 16px;
}

/* Style for the snackbar */
.v-snackbar {
  margin-top: 16px;
}

/* Style for the dialog image preview */
.v-img {
  max-height: 200px;
  object-fit: cover;
}
</style>