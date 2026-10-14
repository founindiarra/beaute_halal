<script setup>
import AppIcon from '@/components/ui/AppIcon.vue'
import StarRating from '@/components/ui/StarRating.vue'
import { formatDateShort } from '@/utils/format'

defineProps({
  testimonial: { type: Object, required: true },
})
</script>

<template>
  <article class="card-surface flex h-full flex-col gap-5 p-6 sm:p-7">
    <AppIcon name="quote" class="h-7 w-7 shrink-0 text-sand-dark" />

    <p class="flex-1 text-[0.98rem] leading-relaxed text-cocoa">« {{ testimonial.comment }} »</p>

    <div class="flex flex-wrap items-end justify-between gap-3 border-t border-sand pt-4">
      <div class="min-w-0">
        <p class="font-display text-lg text-cocoa-dark">{{ testimonial.firstName }}</p>
        <p v-if="testimonial.serviceName" class="truncate text-xs text-muted">{{ testimonial.serviceName }}</p>
      </div>
      <div class="flex flex-col items-end gap-1">
        <StarRating :rating="testimonial.rating" />
        <span v-if="testimonial.reviewedOn" class="text-[0.7rem] text-muted">{{ formatDateShort(testimonial.reviewedOn) }}</span>
      </div>
    </div>
  </article>
</template>
