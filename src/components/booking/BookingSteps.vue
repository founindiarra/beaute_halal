<script setup>
import AppIcon from '@/components/ui/AppIcon.vue'

defineProps({
  steps: { type: Array, required: true },
  current: { type: Number, required: true },
  maxReached: { type: Number, required: true },
})

defineEmits(['select'])
</script>

<template>
  <!-- Fil d'étapes : liste compacte défilante sur mobile -->
  <nav aria-label="Étapes de la réservation" class="-mx-5 overflow-x-auto px-5 sm:mx-0 sm:px-0">
    <ol class="flex w-max min-w-full items-center gap-2 sm:w-full sm:gap-3">
      <li
        v-for="(step, index) in steps"
        :key="step.id"
        class="flex shrink-0 items-center gap-2 sm:flex-1 sm:shrink"
      >
        <button
          type="button"
          class="flex items-center gap-2.5 rounded-full py-1.5 pl-1.5 pr-3 transition-colors sm:pr-4"
          :class="[
            step.id === current ? 'bg-cocoa-dark text-cream' : 'text-muted hover:text-cocoa-dark',
            step.id > maxReached ? 'cursor-not-allowed opacity-50 hover:text-muted' : '',
          ]"
          :disabled="step.id > maxReached"
          :aria-current="step.id === current ? 'step' : undefined"
          @click="$emit('select', step.id)"
        >
          <span
            class="grid h-8 w-8 shrink-0 place-items-center rounded-full text-[0.78rem] transition-colors"
            :class="
              step.id < current
                ? 'bg-gold text-white'
                : step.id === current
                  ? 'bg-cream text-cocoa-dark'
                  : 'bg-sand text-cocoa'
            "
          >
            <AppIcon v-if="step.id < current" name="check" class="h-4 w-4" />
            <template v-else>{{ step.id }}</template>
          </span>
          <span class="whitespace-nowrap text-[0.78rem] uppercase tracking-[0.1em]">
            {{ step.label }}
          </span>
        </button>

        <span
          v-if="index < steps.length - 1"
          class="hidden h-px flex-1 bg-sand-dark sm:block"
          aria-hidden="true"
        />
      </li>
    </ol>
  </nav>
</template>
