<script setup>
import EditableEventHeader from '@/components/EventHeader.vue'
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { ref as dbRef, onValue } from 'firebase/database'
import { db } from '@/data/config'
import EventHeader from '@/components/EventHeader.vue'
import ParticipantList from '@/components/ParticipantList.vue'
import Calendar from '@/components/Calendar.vue'

const route = useRoute()
const eventID = route.params.id
const eventData = ref(null)

onMounted(() => {
  const eventRef = dbRef(db, `events/${eventID}`)
  onValue(eventRef, (snapshot) => {
    eventData.value = snapshot.val()
  })
})
</script>

<template>
    <EventHeader :eventID="eventID"
                 :initialName="eventData?.name || 'Ukendt begivenhed'"
    />
    <main>
        <ParticipantList :eventID="eventID"/>

        
        <Calendar/>
        
    </main>
    
</template>