<script setup lang="ts">
import { ref, watch } from 'vue'
import type { Hike } from './types'

const props = defineProps<{
  hike: Hike | null
}>()

const emit = defineEmits<{
  (e: 'save', hike: Hike): void
  (e: 'cancel'): void
}>()

const form = ref(emptyForm())

watch(
  () => props.hike,
  (h) => {
    if (h) {
      form.value = { ...h }
    } else {
      form.value = emptyForm()
    }
  },
  { immediate: true },
)

function emptyForm(): Hike {
  return { id: 0, title: '', location: '', date: '', distanceKm: 0, verticalMetersGained: 0, notes: '' }
}

function save() {
  if (!form.value.title.trim() || !form.value.location.trim() || !form.value.date) {
    return
  }
  emit('save', {
    id: props.hike?.id ?? Date.now(),
    title: form.value.title.trim(),
    location: form.value.location.trim(),
    date: form.value.date,
    distanceKm: Number(form.value.distanceKm),
    verticalMetersGained: Number(form.value.verticalMetersGained),
    notes: form.value.notes.trim(),
  })
}
</script>

<template>
  <section class="editor">
    <div class="header-row">
      <h1>{{ hike ? 'Wandeling bewerken' : 'Wandeling toevoegen' }}</h1>
    </div>

    <form class="hike-form" @submit.prevent="save">
      <label>
        Naam
        <input v-model="form.title" required type="text" />
      </label>

      <label>
        Locatie
        <input v-model="form.location" required type="text" />
      </label>

      <label>
        Datum
        <input v-model="form.date" required type="date" />
      </label>

      <label>
        Afstand (km)
        <input v-model.number="form.distanceKm" min="0" required step="0.1" type="number" />
      </label>

      <label>
        Hoogtemeters (hm)
        <input v-model.number="form.verticalMetersGained" min="0" required step="1" type="number" />
      </label>

      <label>
        Notities
        <textarea v-model="form.notes" rows="2" />
      </label>

      <div class="actions">
        <button type="submit" class="btn-save">Opslaan</button>
        <button type="button" class="btn-cancel" @click="emit('cancel')">Annuleren</button>
      </div>
    </form>
  </section>
</template>

<style scoped>
.editor {
  max-width: 680px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

.header-row {
  margin-bottom: 1rem;
}

.hike-form {
  display: grid;
  gap: 0.75rem;
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

.actions {
  display: flex;
  gap: 0.5rem;
}

.btn-save {
  border: none;
  border-radius: 0.375rem;
  background: #2563eb;
  color: white;
  padding: 0.5rem 0.75rem;
  cursor: pointer;
}

.btn-cancel {
  border: 1px solid #cbd5e1;
  border-radius: 0.375rem;
  background: white;
  color: #374151;
  padding: 0.5rem 0.75rem;
  cursor: pointer;
}
</style>
