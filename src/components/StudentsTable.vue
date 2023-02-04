<template>
  <div class="bg-white shadow-lg rounded-sm border border-slate-200 relative">
    <header class="px-5 py-4">
      <h2 class="font-semibold text-slate-800">Total Course Students <span class="text-slate-400 font-medium">{{ students?.length }}</span></h2>
    </header>
    <div>

      <!-- Table -->
      <div class="overflow-x-auto">
        <table class="table-auto w-full">
          <!-- Table header -->
          <thead class="text-xs font-semibold uppercase text-slate-500 bg-slate-50 border-t border-b border-slate-200">
            <tr>
              <th class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
                <div class="font-semibold text-left">ID</div>
              </th>
              <th class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
                <div class="font-semibold text-left">Student</div>
              </th>
              <th class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
                <div class="font-semibold text-left">Email</div>
              </th>
              <th class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
                <div class="font-semibold text-left">Attendance</div>
              </th>
            </tr>
          </thead>
          <!-- Table body -->
          <tbody class="text-sm divide-y divide-slate-200">
            <Student
              v-for="student in students"
              :key="student.id"
              :student="student"
              :value="student.id"
              @checkIsPresent="checkIsPresent"
              :attendances="attendances"
            />
          </tbody>
        </table>

      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'
import Student from './StudentsTableItem.vue'

export default {
  name: 'StudentsTable',
  components: {
    Student,
  },  
  props: {
    students: Array,
    attendances: Array,
  },
  emits: [
    'checkIsPresent',
  ],
  setup(props, { emit }) {
    const checkIsPresent = (studentId, isPresent) => {
      emit('checkIsPresent', studentId, isPresent)
    }

    return {
      checkIsPresent,
    }
  }
}
</script>