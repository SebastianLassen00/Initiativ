<script setup>
import { ref as dbRef, set } from 'firebase/database'
import { db } from '@/data/config'
import { useRouter } from 'vue-router'
import { ref } from 'vue'

const router = useRouter()
const isLoading = ref(false)
const error = ref(null)

const createEvent = async () => {
    isLoading.value = true
    error.value = null
    const eventID = Date.now()
    const eventRef = dbRef(db, `events/${eventID}`)

    try {
        await set(eventRef, {
            id: eventID,
            createdAt: eventID,
            status: 'active',
            participants: {}
        })
        router.push(`/event/${eventID}`)
    } catch (err) {
        console.error('Error creating event:', err)
        error.value = 'Failed to create event. Please try again.'
    } finally {
        isLoading.value = false
    }
}
</script>

<template>
    <div class="create-event">
        <button 
            @click="createEvent" 
            class="button"
            :disabled="isLoading"
        >
            {{ isLoading ? 'Creating...' : 'Create New Event' }}
        </button>
        <p v-if="error" class="error">{{ error }}</p>
    </div>
</template>

<style scoped>
.create-event {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    margin: 2rem 0;
}

.button {
    font-size: 1.25rem;
    padding: 1rem 2rem;
    border: none;
    border-radius: 8px;
    background-color: hsla(160, 100%, 37%, 1);
    color: white;
    cursor: pointer;
    transition: all 0.3s ease;
    font-weight: 500;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.button:hover:not(:disabled) {
    background-color: hsla(160, 100%, 37%, 0.9);
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.button:active:not(:disabled) {
    transform: translateY(0);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.button:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    background-color: hsla(160, 100%, 37%, 0.7);
}

.error {
    color: #ff4444;
    font-size: 0.875rem;
    font-weight: 500;
}
</style>
