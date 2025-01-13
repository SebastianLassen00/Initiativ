<script setup>
defineProps({
    date: {
        type: Date,
        default: null,
    },
    isSelected: {
        type: Boolean,
        default: false,
    },
    isToday: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits(['dayClick']);

const handleClick = () => {
    if (props.date) {
        emit('dayClick', props.date);
    }
};
</script>

<template>
    <div 
        class="calendar-day" 
        :class="{
            'empty': !date,
            'selected': isSelected && date,
            'today': isToday && date,
        }"
        @click="handleClick"
    >
        <span v-if="date" class="day-number">
            {{ date.getDate() }}
        </span>
        
        <!-- Slot for additional content (e.g., events, indicators) -->
        <slot></slot>
    </div>
</template>

<style scoped>
.calendar-day {
    aspect-ratio: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    background-color: var(--color-background-soft);
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.2s ease;
    padding: 0.5rem;
    position: relative;
}

.day-number {
    font-size: 1.1rem;
    font-weight: 500;
}

.calendar-day:hover:not(.empty) {
    background-color: var(--color-background-mute);
    transform: translateY(-1px);
}

.empty {
    background-color: transparent;
    cursor: default;
}

.selected {
    background-color: hsla(160, 100%, 37%, 0.1);
    border: 2px solid hsla(160, 100%, 37%, 1);
}

.today {
    font-weight: bold;
    color: hsla(160, 100%, 37%, 1);
}
</style>