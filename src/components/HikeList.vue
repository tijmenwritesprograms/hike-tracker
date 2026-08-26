<script setup lang="ts">
import type { Hike } from './types'

defineProps<{
  hikes: Hike[]
  totalDistance: number
  totalVerticalMeters: number
}>()

const emit = defineEmits<{
  (e: 'add'): void
  (e: 'select', id: number): void
  (e: 'remove', id: number): void
}>()
</script>

<template>
  <section class="tracker">
    <div class="header-row">
      <h1>Wandelingen</h1>
      <button type="button" class="btn-add" @click="emit('add')">+ Toevoegen</button>
    </div>

    <p class="summary">
      {{ hikes.length }} wandeling{{ hikes.length !== 1 ? 'en' : '' }} geregistreerd ·
      {{ totalDistance.toFixed(1) }} km totaal ·
      {{ totalVerticalMeters }} hm totaal
    </p>

    <ul v-if="hikes.length" class="hike-list">
      <li v-for="hike in hikes" :key="hike.id" class="hike-item" @click="emit('select', hike.id)">
        <div class="hike-header">
          <strong>{{ hike.title }}</strong>
          <button
            type="button"
            class="btn-remove"
            @click.stop="emit('remove', hike.id)"
          >Verwijder</button>
        </div>
        <p>{{ hike.location }} · {{ hike.date }} · {{ hike.distanceKm.toFixed(1) }} km · {{ hike.verticalMetersGained }} hm</p>
        <p v-if="hike.notes">{{ hike.notes }}</p>
      </li>
    </ul>
    <p v-else class="empty">Nog geen wandelingen geregistreerd.</p>
  </section>
</template>

<style scoped>
.tracker {
  max-width: 680px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

.header-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.summary {
  color: #4b5563;
}

.btn-add {
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
  margin-top: 1rem;
}

.hike-item {
  border: 1px solid #dbe3ef;
  border-radius: 0.5rem;
  padding: 0.75rem;
  cursor: pointer;
  transition: background 0.15s;
}

.hike-item:hover {
  background: #f1f5f9;
}

.hike-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 0.5rem;
}

.btn-remove {
  border: none;
  border-radius: 0.375rem;
  background: #dc2626;
  color: white;
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  font-size: 0.85rem;
}

.empty {
  color: #6b7280;
}
</style>
