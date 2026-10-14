<script setup>
import { computed } from 'vue'

import AppIcon from '@/components/ui/AppIcon.vue'
import DataState from '@/components/ui/DataState.vue'
import { useApiData } from '@/composables/useApiData'
import { fetchServiceCategories, fetchServices } from '@/services/api'
import { formatDuration, formatPrice } from '@/utils/format'

const props = defineProps({
  /** Slug de la prestation actuellement sélectionnée. */
  selectedSlug: { type: String, default: null },
})

const emit = defineEmits(['select'])

const loadCatalogue = async () => {
  const [categories, services] = await Promise.all([fetchServiceCategories(), fetchServices()])
  return { categories, services }
}

const { data, loading, error, reload } = useApiData(loadCatalogue, {
  categories: [],
  services: [],
})

const byCategory = computed(() =>
  (data.value?.categories ?? [])
    .map((category) => ({
      ...category,
      items: (data.value?.services ?? []).filter(
        (service) => service.category.slug === category.slug,
      ),
    }))
    .filter((category) => category.items.length > 0),
)

const isSelected = (service) => props.selectedSlug === service.slug
</script>

<template>
  <DataState
    :loading="loading"
    :error="error"
    :empty="!byCategory.length"
    loading-label="Chargement des prestations…"
    empty-label="Aucune prestation n’est réservable pour le moment."
    @retry="reload"
  >
    <div class="flex flex-col gap-8">
      <section v-for="category in byCategory" :key="category.id" class="flex flex-col gap-4">
        <h3 class="text-[0.72rem] uppercase tracking-[0.24em] text-gold">{{ category.name }}</h3>

        <ul class="grid gap-3 sm:grid-cols-2">
          <li v-for="service in category.items" :key="service.id">
            <button
              type="button"
              class="flex h-full w-full flex-col gap-2 rounded-2xl border p-4 text-left transition-all duration-200"
              :class="
                isSelected(service)
                  ? 'border-cocoa-dark bg-cocoa-dark/[0.04] shadow-[var(--shadow-soft)]'
                  : 'border-sand bg-white hover:border-gold'
              "
              :aria-pressed="isSelected(service)"
              @click="emit('select', service)"
            >
              <span class="flex items-start justify-between gap-3">
                <span class="font-display text-lg leading-snug text-cocoa-dark">
                  {{ service.name }}
                </span>
                <span
                  class="mt-0.5 grid h-5 w-5 shrink-0 place-items-center rounded-full border transition-colors"
                  :class="isSelected(service) ? 'border-cocoa-dark bg-cocoa-dark text-cream' : 'border-sand-dark'"
                >
                  <AppIcon v-if="isSelected(service)" name="check" class="h-3 w-3" />
                </span>
              </span>

              <span class="flex flex-wrap items-center gap-x-4 gap-y-1 text-xs text-muted">
                <span class="inline-flex items-center gap-1.5">
                  <AppIcon name="clock" class="h-3.5 w-3.5 text-gold" />
                  {{ formatDuration(service.durationMinutes) }}
                </span>
                <span class="text-cocoa">{{ formatPrice(service.price, service.currency) }}</span>
              </span>
            </button>
          </li>
        </ul>
      </section>
    </div>
  </DataState>
</template>
