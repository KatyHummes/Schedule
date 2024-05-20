<script setup>
import { ref } from 'vue'
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin from '@fullcalendar/interaction'
import AppLayout from '@/Layouts/AppLayout.vue'

const dialogVisible = ref(false)

const selectedDate = ref('')

const calendarOptions = ref({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    dateClick: handleDateClick,
    selectable: true,
    slotMinTime: '06:00:00', 
    slotMaxTime: '22:00:00',
    slotDuration: '00:30:00', 
    slotLabelInterval: '01:00',
    allDaySlot: false,
    height: 'auto',  
    slotLabelFormat: {
        hour: '2-digit',
        minute: '2-digit', 
        hour12: false
    }
})

// Função para lidar com clique na data
function handleDateClick(arg) {
    selectedDate.value = arg.dateStr 
    dialogVisible.value = true 
}
</script>

<template>
    <AppLayout>
      <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
          <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg p-4">
            <v-dialog v-model="dialogVisible" max-width="800">
            <template v-slot:default="{ isActive }">
              <v-card title="Novo Evento">
                <v-card-text>
                  Criar Novo Evento em {{ selectedDate }}
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn text="Cancelar" @click="dialogVisible = false"></v-btn>
                </v-card-actions>
              </v-card>
            </template>
          </v-dialog>
            <FullCalendar :options="calendarOptions" />
          </div>
        </div>
      </div>
    </AppLayout>
  </template>

  