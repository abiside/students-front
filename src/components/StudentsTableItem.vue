<template>
  <tr>

    <td class="px-2 first:pl-5 last:pr-5 py-3">
      <div class="flex items-center">
        <div class="font-medium text-slate-800">{{student.id}}</div>
      </div>
    </td>
    <td class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
      <div class="flex items-center">
        <div class="w-10 h-10 shrink-0 mr-2 sm:mr-3">
          <img class="rounded-full" src="/src/assets/images/user-40-01.jpg" width="40" height="40" :alt="student.name" />
        </div>
        <div class="font-medium text-slate-800">{{student.name}} {{student.last_name}}</div>
      </div>
    </td>
    <td class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap">
      <div class="text-left">{{student.email}}</div>
    </td>
    <td class="px-2 first:pl-5 last:pr-5 py-3 whitespace-nowrap w-px">
      <button 
        @click="checkAttendance(true)"
        class="mx-2 my-2 bg-gray-100 transition duration-150 ease-in-out hover:bg-gray-200 rounded border border-gray-300 text-gray-600 px-6 py-2 text-xs"
        :class="{'outline-none ring-2 ring-offset-2 ring-green-600 text-green-800': isPresent === true }">Present</button>
      <button 
        @click="checkAttendance(false)"
        class="mx-2 my-2 bg-gray-100 transition duration-150 ease-in-out hover:bg-gray-200 rounded border border-gray-300 text-gray-600 px-6 py-2 text-xs"
        :class="{'outline-none ring-2 ring-offset-2 ring-red-600 text-red-800': isPresent === false }">Absent</button>
    </td>
  </tr>  
</template>

<script>
import { computed, ref } from 'vue'

export default {
  name: 'StudentsTableItem',
  props: [
    'student', 
    'value', 
    'attendances',
  ],
  emits: [
    'checkIsPresent',
  ],
  setup(props, { emit }) {
    const isPresent = computed(() => {
      return props.student.id in props.attendances ? !!props.attendances[props.student.id] : null
    } )
    const processingAttendanceTo = ref(null)
    const choosePresent = ref(null)

    const checkAttendance = (isPresent) => {
      // processingAttendanceTo.value = isPresent
      emit('checkIsPresent', props.student.id, isPresent)
    }

    return {
      checkAttendance,
      choosePresent,
      isPresent,
    }
  },
}
</script>