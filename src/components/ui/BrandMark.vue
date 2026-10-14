<script setup>
import AppLogo from '@/components/ui/AppLogo.vue'
import { useSalon } from '@/composables/useSalon'

const { salon } = useSalon()

defineProps({
  /** 'dark' sur fond clair, 'light' sur fond chocolat */
  tone: { type: String, default: 'dark' },
  compact: { type: Boolean, default: false },
})
</script>

<template>
  <span class="flex items-center gap-2.5 sm:gap-3">
    <span
      class="grid shrink-0 place-items-center rounded-full border transition-colors"
      :class="[
        compact ? 'h-9 w-9' : 'h-9 w-9 sm:h-10 sm:w-10',
        tone === 'light' ? 'border-gold-soft/60 bg-cocoa-dark/40' : 'border-gold/40 bg-ivory',
      ]"
    >
      <AppLogo
        class="w-auto"
        :class="[
          compact ? 'h-5' : 'h-5 sm:h-[1.4rem]',
          tone === 'light' ? 'text-gold-soft' : 'text-gold',
        ]"
      />
    </span>

    <span class="flex min-w-0 flex-col leading-none">
      <span
        class="truncate font-display text-base tracking-[0.06em] sm:text-lg"
        :class="tone === 'light' ? 'text-cream' : 'text-cocoa-dark'"
      >
        {{ salon.name }}
      </span>
      <span
        v-if="!compact"
        class="mt-1 hidden text-[0.58rem] uppercase tracking-[0.28em] sm:block sm:text-[0.62rem]"
        :class="tone === 'light' ? 'text-gold-soft' : 'text-gold'"
      >
        {{ salon.specialty }}
      </span>
    </span>
  </span>
</template>
