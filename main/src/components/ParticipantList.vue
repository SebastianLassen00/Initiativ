<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { ref as dbRef, onValue, off, update } from 'firebase/database'
import { db } from '@/data/config'
import Participant from '@/components/Participant.vue'

const props = defineProps({
    eventID: {
        type: String,
        required: true,
    },
})

const participants = ref([]);


const removeParticipant = async (index) => {
    try {
        const eventRef = dbRef(db, `events/${props.eventID}`);
        const updatedParticipants = [...participants.value];
        updatedParticipants.splice(index, 1);

        await update(eventRef, {
            participants: updatedParticipants,
        });

    } catch (err) {
        console.error("Error deleting participant: ", err);
    }
}

// Subscribe to participants updates
onMounted(() => {
    const eventRef = dbRef(db, `events/${props.eventID}`);
    onValue(eventRef, (snapshot) => {
        const eventData = snapshot.val();
        participants.value = eventData?.participants || [];
    });
});

// Unsubscribe
onUnmounted(() => {
    const eventRef = dbRef(db, `events/${props.eventID}`);
    off(eventRef);
});
</script>

<template>
    <div class="participants-container">
        <h2>Deltagere</h2>
        
        
        <ul class="participants-list" v-if="participants.length > 0">
            <li v-for="(participant, index) in participants" 
                :key="index"
                class="participant-item"
            >
                {{ participant.name }}
                <button 
                    @click="removeParticipant(index)"
                    class="remove-button"
                >
                    x
                </button>
            </li>
        </ul>

        <Participant 
            :eventID="eventID"
            :existingParticipants="participants"
        />
    </div>
</template>

<style scoped>
.participants-container {
    max-width: 600px;
    margin: 2rem auto;
    padding: 0 1rem;
}

.participants-list {
    list-style: none;
    padding: 0;
    margin: 1rem 0;
}

.participant-item {
    padding: 0.75rem;
    margin-bottom: 0.5rem;
    background-color: var(--color-background-soft);
    border-radius: 4px;
    font-size: 1.25rem;
}

/* Delete button stuff */
.participant-item {
    padding: 0.75rem;
    margin-bottom: 0.5rem;
    background-color: var(--color-background-soft);
    border-radius: 4px;
    font-size: 1.25rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.remove-button {
    opacity: 0;
    background: none;
    border: none;
    color: var(--color-text);
    font-size: 1.5rem;
    cursor: pointer;
    padding: 0 0.5rem;
    transition: all 0.2s ease;
}

.participant-item:hover .remove-button {
    opacity: 0.5;
}

.remove-button:hover {
    opacity: 1 !important;
    color: #ff4444;
}
</style>