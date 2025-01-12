<script setup>
import EditableEventHeader from '@/components/EventHeader.vue'
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { ref as dbRef, onValue } from 'firebase/database'
import { db } from '@/data/config'
import EventHeader from '@/components/EventHeader.vue'

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
        <input type="text" name="eventName" id="eventID">
        <input type="text" name="addParticipant" id="idk">

        <!-- 
        <Calender/>
        -->
    </main>
    
</template>