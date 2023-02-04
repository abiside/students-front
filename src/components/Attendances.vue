<template>
  <div class="flex h-screen overflow-hidden">

    <!-- Content area -->
    <div class="relative flex flex-col flex-1 overflow-y-auto overflow-x-hidden">

      <main>
        <div class="px-4 sm:px-6 lg:px-8 py-8 w-full max-w-9xl mx-auto">

          <!-- Page header -->
          <div class="sm:flex sm:justify-between sm:items-center mb-8">

            <!-- Left: Title -->
            <div class="mb-4 sm:mb-0">
              <h1 class="text-2xl md:text-3xl text-slate-800 font-bold">{{ currentCourse?.name }} Roster✨</h1>
            </div>

            <!-- Right: Actions  -->
            <div class="grid grid-flow-col sm:auto-cols-max justify-start sm:justify-end gap-2">
              <!-- Dropdown -->
              <CoursePicker :options="courses" :selected="currentCourse"/>
              <Datepicker v-model="date" :format="format" />                       
            </div>

          </div>

          <!-- Table -->
          <StudentsTable :students="students" @checkIsPresent="checkIsPresent" :attendances="attendances"/>

        </div>
      </main>

    </div> 

  </div>
</template>

<script>
import { ref, watch, reactive, onMounted, onBeforeMount } from 'vue'
import StudentsTable from './StudentsTable.vue'
import CoursePicker from './CoursePicker.vue'
import moment from 'moment';

import Datepicker from '@vuepic/vue-datepicker'

import '@vuepic/vue-datepicker/dist/main.css';

export default {
  name: 'Attendances',
  components: {
    Datepicker,
    CoursePicker,
    StudentsTable,
  },
  setup(props) {
    const baseUrl = ref('http://localhost/api/v1')
    const date = ref(new Date())
    const students = ref(null)
    const courses = ref([])
    const attendances = ref([])

    const currentCourse = ref(null)
    const format = (date) => {
      const day = date.getDate();
      const month = date.getMonth() + 1;
      const year = date.getFullYear();

      return `${month}/${day}/${year}`;
    }

    const updateCourses = async () => {
      const data = await fetch(baseUrl.value + '/courses', {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
        }
      })
      .then(response => response.json())
      .then(data => {
        if (!currentCourse.value) {
          currentCourse.value = data.data[0]
        }

        courses.value = data.data
      }).catch(error => console.log(error))
    }

    const updateCourseStudents = async () => {
      const data = await fetch(baseUrl.value + '/courses/' + currentCourse.value.id + '/students', {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
        }
      })
      .then(response => response.json())
      .then(data => {
        students.value = data.data
      }).catch(error => console.log(error))
    }

    const getAttendances = async () => {
      const data = await fetch(baseUrl.value + '/courses/' + currentCourse.value.id + '/attendances?date=' + moment(date.value).format('YYYY-MM-DD') , {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
        }
      })
      .then(response => response.json())
      .then(data => {
        var attTemp = []

        data.data.forEach((att, key) => {
          attTemp[att.student_id] = att.is_present
        })

        attendances.value = attTemp

      }).catch(error => console.log(error))
    }

    const saveAttendance = async (studentId, isPresent) => {
      const data = await fetch(baseUrl.value + '/courses/' + currentCourse.value.id + '/attendances', 
      {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          student_id: studentId,
          is_present: isPresent,
          date: date.value,
        })
      })
      .then(response => response.json())
      .then(data => {
        getAttendances()
      }).catch(error => console.log(error))
    }

    onMounted(() => {
      updateCourses()
    })

    watch(currentCourse, (newValue, oldValue) => {
      updateCourseStudents()
      getAttendances()
    }, {
      inmediate: true,
    })

    watch(date, (newValue, oldValue) => {
      updateCourseStudents()
      getAttendances()
    }, {
      inmediate: true,
    })

    const checkIsPresent = (studentId, isPresent) => {
      saveAttendance(studentId, isPresent)
    }

    return { 
      courses,
      currentCourse,
      date,
      format,
      students,
      checkIsPresent,
      attendances,
    }  
  }
}
</script>