<script setup>
import { ref, defineProps } from 'vue'
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin from '@fullcalendar/interaction'
import AppLayout from '@/Layouts/AppLayout.vue'
import { useForm } from 'laravel-precognition-vue-inertia';
import { useToast } from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';

const props = defineProps({
    events: Array,
});

const toast = useToast();

const events = ref([...props.events]); // Armazenar a lista de eventos

const form = useForm('post', route('event.store'), {
    title: '',
    start: '',
    end: '',
    startTime: '',
    endTime: '',
    address: '',
    color: '',
});

const dialogVisible = ref(false);
const selectedDate = ref('');

// Função para lidar com clique na data
function handleDateClick(arg) {
    selectedDate.value = arg.dateStr;
    form.startTime = "12:00"; // Valor padrão inicial
    form.endTime = "13:00"; // Valor padrão final
    dialogVisible.value = true;
}

// Função para formatar a data e hora no formato correto
function formatDateTime(date, time) {
    return `${date} ${time}:00`;
}

// Função para formatar a data
const formatDate = (dateString) => {
    const options = { day: '2-digit', month: '2-digit', year: 'numeric' };
    return new Date(dateString).toLocaleDateString(undefined, options);
};

const submit = () => {
    form.start = formatDateTime(selectedDate.value, form.startTime);
    form.end = formatDateTime(selectedDate.value, form.endTime);

    form.submit({
        preserveScroll: true,
        onSuccess: () => {
            // Adicionar o novo evento à lista de eventos
            events.value.push({
                title: form.title,
                start: form.start,
                end: form.end,
                address: form.address,
                color: form.color
            });

            form.reset();
            dialogVisible.value = false;
            toast.success("Evento criado com Sucesso!", {
                position: 'top-right',
            });
        },
        onError: (error) => {
            console.log(error);
            toast.error("Erro ao criar Evento!", {
                position: 'top-right',
            });
        }
    });
}

const calendarOptions = ref({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    dateClick: handleDateClick,
    selectable: true,
    slotMinTime: '06:00:00',
    slotMaxTime: '22:00:00',
    slotDuration: '00:30:00',
    slotLabelInterval: '01:00',
    height: 'auto',
    slotLabelFormat: {
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
    }
});
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
                                    <h1 class="mb-4 font-medium">Criar Novo Evento em {{ formatDate(selectedDate) }}</h1>
                                    <form @submit.prevent="submit">
                                        <v-text-field
                                            v-model="form.title"
                                            variant="outlined"
                                            label="Título do Evento"
                                            hide-details
                                            required
                                        ></v-text-field>
                                        <span v-if="form.errors.title" class="text-base text-red-500">
                                            {{ form.errors.title }}
                                        </span>
                                        <div class="my-4 flex justify-center items-center">
                                            <v-text-field
                                                class="me-4"
                                                label="Início"
                                                variant="outlined"
                                                v-model="form.startTime"
                                                type="time"
                                                required
                                            ></v-text-field>
                                            <v-text-field
                                                label="Fim"
                                                variant="outlined"
                                                v-model="form.endTime"
                                                type="time"
                                                required
                                            ></v-text-field>
                                        </div>
                                        <v-text-field
                                            v-model="form.address" class="my-4"
                                            variant="outlined"
                                            label="Endereço"
                                            hide-details
                                            required
                                        ></v-text-field>
                                        <span v-if="form.errors.address" class="text-base text-red-500">
                                            {{ form.errors.address }}
                                        </span>
                                        <v-text-field
                                            v-model="form.color"
                                            variant="outlined"
                                            label="Cor"
                                            hide-details
                                            required
                                        ></v-text-field>
                                        <span v-if="form.errors.color" class="text-base text-red-500">
                                            {{ form.errors.color }}
                                        </span>
                                        <v-card-actions class="flex justify-between">
                                            <v-spacer></v-spacer>
                                            <v-btn text="Cancelar" @click="dialogVisible = false"></v-btn>
                                            <v-btn type="submit" variant="tonal">Salvar</v-btn>
                                        </v-card-actions>
                                    </form>
                                </v-card-text>
                            </v-card>
                        </template>
                    </v-dialog>
                    <FullCalendar :options="calendarOptions" :events="events" />
                </div>
            </div>
        </div>
    </AppLayout>
</template>
