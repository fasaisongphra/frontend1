student_proflie1 จริง

<template>
  <v-app>
    <!-- App Bar -->
    <v-app-bar color="teal-darken-2" elevation="2">
      <template v-slot:prepend>
        <div class="rounded-circle d-flex justify-center align-center" style="background-color: white; width: 40px; height: 40px;">
          <v-icon color="teal-darken-2" size="24">mdi-arrow-left</v-icon>
        </div>
      </template>
      <v-app-bar-title>Student List</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-btn v-bind="props" variant="text">
            Sort by time <v-icon>mdi-chevron-down</v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item value="newest">
            <v-list-item-title>Newest First</v-list-item-title>
          </v-list-item>
          <v-list-item value="oldest">
            <v-list-item-title>Oldest First</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>

    <!-- Main Content -->
    <v-main class="bg-grey-lighten-3"> <!-- เพิ่มสีพื้นหลังหลัก -->
      <v-container class="px-2">
        <div class="text-subtitle-2 text-grey-darken-1 mb-4">
          E5 เทคโนโลยีสารสนเทศ<br>
          ครูที่ปรึกษา : ครูกฤษณุา แนววิเศษ
        </div>

        <v-list bg-color="#f5f5f5" class="custom-list"> <!-- กำหนดสีพื้นหลังเริ่มต้น -->
          <v-list-item
            v-for="(item, index) in activities"
            :key="index"
            :class="{'border-b': index !== activities.length - 1}"
            class="activity-item"
          >
          <!-- เรียกโปรไฟล์จากฐานข้อมูล -->
            <template v-slot:prepend>
              <v-avatar size="48" color="info" class="mr-3">
                <v-img :src="`http://localhost:7000/uploads/profile/${item.picture}`"></v-img>
              </v-avatar>
            </template>

            <v-list-item-title class="font-weight-medium">
              {{ item.username }}
            </v-list-item-title>
            <v-list-item-subtitle class="text-grey-darken-1">
              {{ item.student_id }}
            </v-list-item-subtitle>

            <template v-slot:append>
              <div class="d-flex flex-column align-end">
                <!-- ใช้ radio group สำหรับการเข้าร่วม -->
                <v-radio-group v-model="item.attendance" row>
                  <v-radio label="มา" value="1"></v-radio>
                  <v-radio label="สาย" value="2"></v-radio>
                  <v-radio label="ขาด" value="3"></v-radio>
                  <v-radio label="ลา" value="4"></v-radio>
                </v-radio-group>
                <div class="text-grey-darken-1 text-caption mb-1">
                  <span v-if="item.attendance !== undefined" class="ml-2">({{ item.attendance }})</span>
                </div>
              </div>
            </template>
          </v-list-item>
        </v-list>

        <!-- ปุ่ม Submit สำหรับส่งข้อมูลไปยัง API -->
        <v-btn @click="submitAttendance" color="primary" class="mt-4">
          Submit Attendance
        </v-btn>

      </v-container>
    </v-main>
    
    <!-- Bottom Navigation -->
    <v-bottom-navigation grow color="teal-darken-2">
      <v-btn value="home">
        <v-icon>mdi-home</v-icon>
        Home
      </v-btn>
      <v-btn value="chat">
        <v-icon>mdi-chat</v-icon>
        Chat
      </v-btn>
      <v-btn value="mail">
        <v-icon>mdi-email</v-icon>
        Mail
      </v-btn>
      <v-btn value="profile">
        <v-icon>mdi-account</v-icon>
        Profile
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';

const attendanceOptions = [
  { text: 'มา', value: 1 },
  { text: 'สาย', value: 2 },
  { text: 'ขาด', value: 3 },
  { text: 'ลา', value: 4 }
];

const activities = ref([]);

// ฟังก์ชันสำหรับการดึงข้อมูลจาก API
const listData = async () => {
  try {
    const response = await axios.get("http://localhost:7000/listStudent");
    const result = response.data;
    console.log('listData = ', result);
    activities.value = result.datas.map(item => ({
      ...item,
      attendance: 1 // Set attendance to "มา" (1) by default for all students
    }));
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};

// โหลดข้อมูลเมื่อคอมโพเนนต์ถูก mount
onMounted(() => {
  listData();
});

const updateAttendance = (index, value) => {
  activities.value[index] = {
    ...activities.value[index],
    attendance: value
  };
  // เตรียมข้อมูลที่จะส่งไปที่ API
  const payload = {
    studentName: activities.value[index].username,
    student_id: activities.value[index].student_id,
    attendance: value,
    time: activities.value[index].password
  };
  console.log('data=', payload);
};

// ฟังก์ชันที่จะถูกเรียกเมื่อกดปุ่ม Submit เพื่อส่งข้อมูลทั้งหมดไปยัง API
const submitAttendance = async () => {
  // เตรียมข้อมูลที่จะส่งไปที่ API
  const payload = activities.value.map(activity => ({
    studentName: activity.username,
    studentId: activity.student_id,
    attendance: activity.attendance,
    time: activity.password
  }));

  try {
    // ส่งข้อมูลไปที่ API ด้วย axios
    const response = await axios.post("http://localhost:7000/insert", { activities: payload });
    console.log('API Response:', response.data);
  } catch (error) {
    console.error('Error sending data to API:', error);
  }
};
</script>

<style scoped>
.border-b {
  border-bottom: 1px solid rgba(0, 0, 0, 0.12);
}

/* กำหนด style สำหรับ list */
.custom-list {
  border-radius: 4px;
  overflow: hidden;
}

/* กำหนด style สำหรับ list item และ hover effect */
.activity-item {
  transition: background-color 0.2s ease;
}

.activity-item:hover {
  background-color: #ffffff !important;
  cursor: pointer;
}
</style>
