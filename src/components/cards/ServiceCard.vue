<script setup>
import { useRouter } from 'vue-router'

import AppIcon from '@/components/ui/AppIcon.vue'
import AppImage from '@/components/ui/AppImage.vue'
import BaseButton from '@/components/ui/BaseButton.vue'
import { preselectService } from '@/composables/useBooking'
import { formatDuration, formatPrice } from '@/utils/format'

const props = defineProps({
  service: { type: Object, required: true },
  ratio: { type: String, default: '4/3' },
})

const router = useRouter()

/**
 * Le repère de la prestation voyage dans l'adresse, comme l'identifiant de
 * coiffure du parcours « Nos coiffures » : la page Réservation le relit pour
 * réafficher la prestation choisie, et la sélection survit à un
 * rafraîchissement ou au partage du lien.
 */
const book = () => {
  preselectService(props.service)
  router.push({ name: 'booking', query: { prestation: props.service.slug } })
}
</script>

<template>
  <!--
    Toute la carte mène à la réservation. Le bouton reste le seul élément
    focalisable : son clic remonte jusqu'ici, donc il n'a pas de gestionnaire
    propre et la carte n'a pas besoin d'être atteignable au clavier — elle ne
    fait que dupliquer une action déjà accessible.
  -->
  <article
    class="card-surface group flex h-full cursor-pointer flex-col overflow-hidden transition-all duration-300 hover:-translate-y-1 hover:border-gold/45 hover:shadow-[var(--shadow-lift)]"
    @click="book"
  >
    <div class="media-veil relative overflow-hidden">
      <AppImage
        :src="service.imageUrl"
        :alt="`${service.name} — Beauté Halal`"
        :ratio="ratio"
        sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
        class="transition-transform duration-700 group-hover:scale-[1.04]"
      />
    </div>

    <div class="flex flex-1 flex-col gap-4 p-5 sm:p-6">
      <div class="flex flex-wrap items-center gap-x-4 gap-y-1.5 text-xs text-muted">
        <span class="inline-flex items-center gap-1.5">
          <AppIcon name="clock" class="h-4 w-4 text-gold" />
          {{ formatDuration(service.durationMinutes) }}
        </span>
        <span class="inline-flex items-center gap-1.5 text-cocoa">
          <AppIcon name="sparkle" class="h-4 w-4 text-gold" />
          À partir de {{ formatPrice(service.price, service.currency) }}
        </span>
      </div>

      <div class="flex flex-col gap-2">
        <h3 class="text-[1.4rem] leading-snug">{{ service.name }}</h3>
        <p class="text-sm leading-relaxed text-muted">{{ service.description }}</p>
      </div>

      <div class="mt-auto pt-2">
        <BaseButton size="sm" variant="secondary" block>
          Réserver
          <AppIcon name="arrow-right" class="h-4 w-4" />
        </BaseButton>
      </div>
    </div>
  </article>
</template>
