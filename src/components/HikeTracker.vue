<script setup lang="ts">
import { computed, ref, watch } from 'vue'
import type { Hike } from './types'
import HikeList from './HikeList.vue'
import HikeEdit from './HikeEdit.vue'

type Screen = 'list' | 'edit'

const STORAGE_KEY = 'hike-tracker:hikes'

const hikes = ref<Hike[]>(loadHikes())
const screen = ref<Screen>('list')
const editingHike = ref<Hike | null>(null)

const totalDistance = computed(() =>
  hikes.value.reduce((total, hike) => total + hike.distanceKm, 0),
)

const totalVerticalMeters = computed(() =>
  hikes.value.reduce((total, hike) => total + hike.verticalMetersGained, 0),
)

watch(
  hikes,
  (value) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(value))
  },
  { deep: true },
)

function openAdd() {
  editingHike.value = null
  screen.value = 'edit'
}

function openEdit(id: number) {
  editingHike.value = hikes.value.find((h) => h.id === id) ?? null
  screen.value = 'edit'
}

function saveHike(hike: Hike) {
  const idx = hikes.value.findIndex((h) => h.id === hike.id)
  if (idx >= 0) {
    hikes.value[idx] = hike
  } else {
    hikes.value.unshift(hike)
  }
  screen.value = 'list'
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
        typeof item?.verticalMetersGained === 'number' &&
        typeof item?.notes === 'string',
    )
  } catch {
    return []
  }
}
</script>

<template>
  <HikeList
    v-if="screen === 'list'"
    :hikes="hikes"
    :total-distance="totalDistance"
    :total-vertical-meters="totalVerticalMeters"
    @add="openAdd"
    @select="openEdit"
    @remove="removeHike"
  />
  <HikeEdit
    v-else
    :hike="editingHike"
    @save="saveHike"
    @cancel="screen = 'list'"
  />
</template>
