<script setup>
import { ref, nextTick } from 'vue'
import { ref as dbRef, update } from 'firebase/database'
import { db } from '@/data/config'

const props = defineProps({
    eventID: {
        type: String,
        required: true,
    },
    initialName: {
        type: String,
        default: 'Ukendt begivenhed',
    }
});

const isEditing = ref(false);
const eventName = ref(props.initialName);
const inputRef = ref(null);

const startEditing = async () => {
    isEditing.value = true;
    await nextTick(() => {
        inputRef.value.focus();
        inputRef.value.select();
    });
}

//If the eventname is empty, set it to default, otherwise update the name
const saveEventName = async () => {
    if (!eventName.value.trim()) {
        eventName.value = props.initialName;
        return;
    }

    try {
        const eventRef = dbRef(db, `events/${props.eventID}`);
        await update(eventRef, {
            name: eventName.value.trim(),
        });
        isEditing.value = false;


    } catch (err) {
        console.log("Error updating event name: ", err);
    }
}

const handleKeydown = (e) => {
    if (e.key === 'Enter') {
        saveEventName();
    } else if (e.key === 'Escape') {
        eventName.value = props.initialName;
        isEditing.value = false;;
    }
}

</script>

<template>
    <header class="event-header">
        <div v-if="isEditing" class="edit-container">
            <input 
                type="text" 
                v-model="eventName" 
                @blur="saveEventName" 
                @keydown="handleKeydown" 
                class="edit-input" 
                ref="inputRef" 
                maxlength="50" 
            >
            
        </div>
        <h1 v-else class="event-title" @click="startEditing">
            {{ eventName }}
            <span class="edit-hint">(Klik for ændring)</span>
        </h1>
    </header>
</template>

<style scoped>
.event-header {
  padding: 2rem;
  text-align: center;
}

.event-title {
  color: var(--color-heading);
  font-size: 2.5rem;
  font-weight: 500;
  margin: 0;
  cursor: pointer;
  display: inline-block;
}

.edit-hint {
  font-size: 0.875rem;
  opacity: 0;
  font-weight: normal;
  font-style: italic;
  transition: opacity 0.2s;
  margin-left: 0.5rem;
}

.event-title:hover .edit-hint {
  opacity: 0.5;
}

.edit-container {
  display: inline-block;
}

.edit-input {
  font-size: 2.5rem;
  font-weight: 500;
  color: var(--color-heading);
  background: transparent;
  border: none;
  border-bottom: 2px solid var(--color-border);
  padding: 0.5rem;
  width: 100%;
  text-align: center;
  outline: none;
  transition: border-color 0.2s;
}

.edit-input:focus {
  border-color: hsla(160, 100%, 37%, 1);
}
</style>