<script setup lang="ts">
import type { Event, Organizer} from '@/types'
import { onMounted, ref } from 'vue'; 
import EventService from '@/services/EventService';
import OrganizerService from '@/services/OrganizerService';
import { useRouter } from 'vue-router';
import { useMessageStore } from '@/stores/message';
import BaseInput from '@/components/BaseInput.vue';
import BaseSelect from '@/components/BaseSelect.vue';

const event = ref<Event>({
    id: 0,
    category: '',
    title: '',
    description: '',
    location: '',
    date: '',
    time: '',
    petsAllowed: false,
    organizer: {
        id: 0,
        name: ''
    }
})
const router = useRouter()
const store = useMessageStore()
function saveEvent() {
    EventService.saveEvent(event.value)
        .then((response) => {
            router.push({ name: 'event-detail-view' , params: { id: response.data.id}})
            store.updateMessage('You are successfully add a new event for ' + response.data.title)
            setTimeout(() => {
                store.resetMessage()
            }, 3000)
        })
        .catch( () =>{
            router.push({ name: 'network-error-view'})
        })
}

const organizers = ref<Organizer[]>([])
onMounted(() => {
    OrganizerService.getOrgaizers()
    .then((respone) => {
        organizers.value = respone.data
    })
    .catch(() => {
        router.push({ name: 'network-error-view'})
    })
})
</script>

<template>

    <div>
        <h1>Create an event</h1>
        <form @submit.prevent="saveEvent">
            <BaseInput v-model="event.category" type = "text" label="Category"/>
            <h3>Name & describe your event</h3>
            <BaseInput v-model="event.title" type = "text" label="Title"/>
            <BaseInput v-model="event.description" type = "text" label="Description"/>
            <BaseInput v-model="event.location" type = "text" label="Location"/>
            <h3>Who is your organizer?</h3>
            <label>Select an Organizer</label>
            <BaseSelect v-model= "event.organizer.id" :options="organizers" label="Organizer"/>
            <button class="button" type="submit">Submit</button>
        </form>

        <pre>{{ event }}</pre>
    </div>

</template>