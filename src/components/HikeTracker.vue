<script setup lang="ts">
import { computed, ref, watch } from 'vue'

interface Hike {
  id: number
  title: string
  location: string
  date: string
  distanceKm: number
  notes: string
}

const STORAGE_KEY = 'hike-tracker:hikes'

const hikes = ref<Hike[]>(loadHikes())
const form = ref({
  title: '',
  location: '',
  date: '',
  distanceKm: 0,
  notes: ''
})

const totalDistance = computed(() =>
  hikes.value.reduce((total, hike) => total + hike.distanceKm, 0),
)

watch(
  hikes,
  (value) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(value))
  },
  { deep: true },
)

function addHike() {
  if (!form.value.title.trim() || !form.value.location.trim() || !form.value.date) {
    return
  }

  hikes.value.unshift({
    id: Date.now(),
    title: form.value.title.trim(),
    location: form.value.location.trim(),
    date: form.value.date,
    distanceKm: Number(form.value.distanceKm),
    notes: form.value.notes.trim()
  })

  form.value = {
    title: '',
    location: '',
    date: '',
    distanceKm: 0,
    notes: ''
  }
}

function removeHike(id: number) {
  hikes.value = hikes.value.filter((hike) => hike.id !== id)
}

function loadHikes(): Hike[] {
  const raw = localStorage.getItem(STORAGE_KEY)

  if (!raw) {
    return []
  }

  try {
    const parsed = JSON.parse(raw)
    if (!Array.isArray(parsed)) {
      return []
    }

    return parsed.filter(
      (item): item is Hike =>
        typeof item?.id === 'number' &&
        typeof item?.title === 'string' &&
        typeof item?.location === 'string' &&
        typeof item?.date === 'string' &&
        typeof item?.distanceKm === 'number' &&
        typeof item?.notes === 'string',
    )
  } catch {
    return []
  }
}
</script>

<template>
  <section class="tracker">
    <h1>Hike Tracker</h1>
    <p class="summary">
      {{ hikes.length }} hikes logged · {{ totalDistance.toFixed(1) }} km total
    </p>

    <form class="hike-form" @submit.prevent="addHike">
      <label>
        Hike name
        <input v-model="form.title" required type="text" />
      </label>

      <label>
        Location
        <input v-model="form.location" required type="text" />
      </label>

      <label>
        Date
        <input v-model="form.date" required type="date" />
      </label>

      <label>
        Distance (km)
        <input v-model.number="form.distanceKm" min="0" required step="0.1" type="number" />
      </label>

      <label>
        Notes
        <textarea v-model="form.notes" rows="2" />
      </label>

      <button type="submit">Add hike</button>
    </form>

    <ul v-if="hikes.length" class="hike-list">
      <li v-for="hike in hikes" :key="hike.id">
        <div class="hike-header">
          <strong>{{ hike.title }}</strong>
          <button type="button" @click="removeHike(hike.id)">Remove</button>
        </div>
        <p>{{ hike.location }} · {{ hike.date }} · {{ hike.distanceKm.toFixed(1) }} km</p>
        <p v-if="hike.notes">{{ hike.notes }}</p>
      </li>
    </ul>
    <p v-else>No hikes logged yet.</p>
  </section>
</template>

<style scoped>
.tracker {
  max-width: 680px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

.summary {
  color: #4b5563;
}

.hike-form {
  display: grid;
  gap: 0.75rem;
  margin: 1rem 0 1.5rem;
}

label {
  display: grid;
  gap: 0.25rem;
}

input,
textarea {
  border: 1px solid #cbd5e1;
  border-radius: 0.375rem;
  padding: 0.5rem;
}

button {
  width: fit-content;
  border: none;
  border-radius: 0.375rem;
  background: #2563eb;
  color: white;
  padding: 0.5rem 0.75rem;
  cursor: pointer;
}

.hike-list {
  list-style: none;
  padding: 0;
  display: grid;
  gap: 0.75rem;
}

.hike-list li {
  border: 1px solid #dbe3ef;
  border-radius: 0.5rem;
  padding: 0.75rem;
}

.hike-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 0.5rem;
}

.hike-header button {
  background: #dc2626;
}
</style>
