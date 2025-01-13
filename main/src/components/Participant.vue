<script setup>
import { ref as vueRef } from 'vue'
import { ref as dbRef, update } from 'firebase/database'
import { db } from '@/data/config'

const props = defineProps({
    eventID: {
        type: String,
        required: true,
    },
    existingParticipants: {
        type: Object,
        default: () => ([])
    }
});

const inputRef = vueRef(null);
const newParticipantName = vueRef('');

const addParticipant = async () => {
    // Return if the participants name is empty
    const name = newParticipantName.value.trim();
    if (!name) return;

    // Return if the participant is not unique
    if (Object.values(props.existingParticipants).some(p => p.name.toLowerCase() === name.toLocaleLowerCase())) {
        // Maybe add some feedback to user
        return;
    }

    try {
        const eventRef = dbRef(db, `events/${props.eventID}`);
        const newParticipant = {
            name: name
        }
        const updatedParticipants = [...(props.existingParticipants || []), newParticipant]

        await update(eventRef, {
            participants: updatedParticipants,
        });
        
        newParticipantName.value = "";
        inputRef.value.focus();

    } catch (err) {
        console.error('Error adding participant: ', err);
    }
}

const handleKeydown = (e) => {
    if (e.key === 'Enter') {
        addParticipant();
    }
}
</script>

<template>
    <div class="add-participant">
        <input 
            type="text"
            v-model="newParticipantName"
            @keydown="handleKeydown"
            placeholder="Tilføj ny deltager..."
            ref="inputRef"
            class="participant-input"
        >
        <button 
            @click="addParticipant"
            class="add-button"
            :disabled="!newParticipantName.trim()"
        >
            Tilføj
        </button>
    </div>
</template>

<style scoped>
.add-participant {
    display: flex;
    gap: 0.5rem;
    margin-top: 1rem;
}

.participant-input {
    flex-grow: 1;
    font-size: 1.25rem;
    color: var(--color-heading);
    background: transparent;
    border: none;
    border-bottom: 2px solid var(--color-border);
    padding: 0.5rem;
    outline: none;
    transition: border-color 0.2s;
}

.participant-input:focus {
    border-color: hsla(160, 100%, 37%, 1);
}

.add-button {
    padding: 0.5rem 1rem;
    background-color: hsla(160, 100%, 37%, 1);
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: opacity 0.2s;
}

.add-button:hover:not(:disabled) {
    opacity: 0.9;
}

.add-button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}
</style>