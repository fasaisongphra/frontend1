<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    :sort-by="[{ key: 'calories', order: 'asc' }]">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>รายละเอียดสมาชิก</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">เพิ่มสมาชิก</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field v-model="editedItem.student_id" label="รหัสสมาชิก"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field v-model="editedItem.username" label="ชื่อผู้ใช้"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field v-model="editedItem.password" label="รหัสผ่าน"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                        v-model="editedItem.picture"
                        label="รูปภาพ"
                      ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="close">ยกเลิก</v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="save">ยืนยัน</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon class="me-2" size="small" @click="editItem(item)">mdi-pencil</v-icon>
      <v-icon size="small" @click="deleteItem(item)">mdi-delete</v-icon>
    </template>

    <template v-slot:item.picture="{ item }">
      <v-img v-if="item.picture" :src="'http://localhost:7000/uploads/profile/' + item.picture" max-width="100" max-height="100" />
      <span v-else>No Image</span>
    </template>
    
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</template>

<script>
import axios from 'axios'
import {useRouter} from  'vue-router'
const router = useRouter()

export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      { title: 'student_id', align: 'start', sortable: false, key: 'student_id' },
      { title: 'username', key: 'username' },
      { title: 'password', key: 'password' },
      { title: 'picture', key: 'picture' },
      { title: 'Actions', key: 'actions', sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: { student_id: '', username: '', password: '', picture: '' },
    defaultItem: { student_id: '', username: '', password: '', picture: '' },
  }),

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'เพิ่มข้อมูล' : 'แก้ไขข้อมูล'
    },
  },

  watch: {
    dialog (val) {
      val || this.close()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    },
  },
  
  async mounted() {
    const token = localStorage.getItem("token");
    console.log("check  token from api  =  ", token); 
    try {
      const response = await axios.get("http://localhost:7000/checktoken", {          
        headers: { 
          Authorization: `Bearer ${token}`,    
        },
      })
      console.log('data = ', response.data)
    } catch (error) {
      console.error("Error:", error.response.data);
      if(token == null) {
            window.location.href = "/login"}
    }
  },
  created () {
    this.initialize();
    this.listData();
  },

  methods: {
    initialize () {
      this.desserts = [];
    },
    async listData() {
      try {
        const response = await axios.get("http://localhost:7000/listStudent");
        this.desserts = response.data.datas;
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    async deleteItem(item) {
      const id = item.student_id;
      try {
        const response = await axios.post("http://localhost:7000/deleteStudent", { student_id: id });
        if (response.data.status === 1) {
          this.desserts = this.desserts.filter(dessert => dessert.student_id !== id);
        }
      } catch (e) {
        console.error('Delete error:', e.message);
      }
    },

    close () {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete () {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    async save() {
      try {
        if (this.editedIndex > -1) {
          await axios.post("http://localhost:7000/updateStudent", this.editedItem);
        } else {
          await axios.post("http://localhost:7000/insertStudent", this.editedItem);
        }
        this.listData();
      } catch (e) {
        console.error('Save error:', e.message);
      }
      this.close();
    },
    
    async onImageSelected(event) {
      const file = event.target.files[0];
      const formData = new FormData();
      formData.append('picture', file);
      formData.append('student_id', this.editedItem.student_id);

  try {
    const response = await axios.post('http://localhost:7000/upload-single', formData, {
      headers: { 'Content-Type': 'multipart/form-data' }
    });
    if (response.data.status === 1) {
      // ตั้งค่า `editedItem.picture` เป็นชื่อไฟล์ที่อัปโหลดสำเร็จ
      this.editedItem.picture = response.data.file.filename; // บันทึกเฉพาะชื่อไฟล์
    }
  } catch (error) {
    console.error('Image upload failed:', error);
  }
  }
}}
</script>
