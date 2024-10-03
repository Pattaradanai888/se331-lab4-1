<script setup lang="ts">
import type { Organizer } from '@/types'
import { ref } from 'vue'; 
import EventService from '@/services/EventService';
import { useRouter } from 'vue-router';
import { useMessageStore } from '@/stores/message';
const organizer = ref<Organizer>({
    id: 0,
    name: '',
})
const router = useRouter()
const store = useMessageStore()
function saveEvent() {
    EventService.saveOrganizer(organizer.value)
        .then((response) => {
            router.push({ name: 'event-detail-view' , params: { id: response.data.id}})
            store.updateMessage('You are successfully add a new organizer for ' + response.data.title)
            setTimeout(() => {
                store.resetMessage()
            }, 3000)
        })
        .catch( () =>{
            router.push({ name: 'network-error-view'})
        })
}
</script>

<template>

    <div>
        <h1>Create an event</h1>
        <form @submit.prevent="saveEvent">
            <label>Name</label>
            <input v-model="organizer.name" type="text" placeholder="Name" class="field"/>
            <h3>address of you  your organizer</h3>
            <button class="button" type="submit">Submit</button>
        </form>

        <pre>{{ organizer }}</pre>
    </div>

</template>