<script setup>
import { ref, computed } from 'vue'
import CalendarDay from '@/components/Day.vue'

const currentDate = ref(new Date());
const selectedDate = ref(null);

const daysInMonth = computed(() => {
    const year = currentDate.value.getFullYear();
    const month = currentDate.value.getMonth();
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);

    const firstDayOfWeek = firstDay.getDay();
    const paddingDays = Array(firstDayOfWeek).fill(null);

    const days = Array.from(
        { length: lastDay.getDate() },
        (_, i) => new Date(year, month, i + 1)
    );

    return [...paddingDays, ...days];
});

const monthName = computed(() => {
    return currentDate.value.toLocaleString('da-DK', {month: 'long'});
});

const year = computed(() => {
    return currentDate.value.getFullYear();
});

const nextMonth = computed(() => {
    currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() + 1
    );
});

const previousMonth = computed(() => {
    currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() + -1
    );
});


const isToday = (date) => date?.toDateString() === new Date().toDateString();

const handleDayClick = (date) => {
    selectedDate.value = date;
    // Emit event or handle selection logic
};

</script>

<template>
    <div class="calendar">
        <div class="calendar-header">
            <button @click="previousMonth" class="nav-button">&lt;</button>
            <h2>{{ monthName }} {{ year }}</h2>
            <button @click="nextMonth" class="nav-button">&gt;</button>
        </div>
        
        <div class="weekdays">
            <div class="weekday">Man</div>
            <div class="weekday">Tir</div>
            <div class="weekday">Ons</div>
            <div class="weekday">Tor</div>
            <div class="weekday">Fre</div>
            <div class="weekday">Lør</div>
            <div class="weekday">Søn</div>
        </div>

        <div class="days">
            <CalendarDay
                v-for="(day, index) in daysInMonth"
                :key="index"
                :date="day"
                :is-selected="selectedDate?.toDateString() === day?.toDateString()"
                :is-today="isToday(day)"
                @day-click="handleDayClick"
            >
                <!-- Future slot content for events -->
            </CalendarDay>
        </div>
    </div>
</template>

<style scoped>
.calendar {
    max-width: 800px;
    margin: 2rem auto;
    padding: 1rem;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1rem;
}

.nav-button {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    color: var(--color-text);
    padding: 0.5rem 1rem;
    transition: opacity 0.2s;
}

.nav-button:hover {
    opacity: 0.7;
}

.weekdays {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 0.5rem;
    margin-bottom: 0.5rem;
    text-align: center;
}

.weekday {
    font-weight: 500;
    padding: 0.5rem;
}

.days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 0.5rem;
}

.day {
    aspect-ratio: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: var(--color-background-soft);
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.2s;
}

.day:hover:not(.empty) {
    background-color: var(--color-background-mute);
}

.empty {
    background-color: transparent;
    cursor: default;
}
</style>