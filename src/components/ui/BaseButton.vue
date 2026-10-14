<script setup>
import { computed } from 'vue'
import { RouterLink } from 'vue-router'

/**
 * Bouton unique du site : rendu en <router-link>, <a> ou <button>
 * selon les props fournies. Taille minimale tactile garantie (44 px).
 */
const props = defineProps({
  to: { type: [String, Object], default: null },
  href: { type: String, default: null },
  type: { type: String, default: 'button' },
  variant: { type: String, default: 'primary' }, // primary | secondary | ghost | light | outline-light
  size: { type: String, default: 'md' }, // sm | md | lg
  block: { type: Boolean, default: false },
  disabled: { type: Boolean, default: false },
})

const VARIANTS = {
  primary:
    'bg-cocoa-dark text-cream border border-cocoa-dark hover:bg-cocoa hover:border-cocoa shadow-[0_14px_30px_-18px_rgba(88,48,26,0.9)]',
  secondary: 'bg-transparent text-cocoa-dark border border-cocoa-dark/30 hover:border-gold hover:text-gold',
  ghost: 'bg-ivory text-cocoa-dark border border-transparent hover:bg-sand',
  light: 'bg-cream text-cocoa-dark border border-cream hover:bg-ivory',
  'outline-light': 'bg-transparent text-cream border border-cream/45 hover:border-cream hover:bg-cream/10',
}

const SIZES = {
  sm: 'min-h-[42px] px-4 py-2 text-[0.78rem] tracking-[0.14em]',
  md: 'min-h-[48px] px-6 py-2.5 text-[0.8rem] tracking-[0.16em]',
  lg: 'min-h-[54px] px-8 py-3 text-[0.85rem] tracking-[0.18em]',
}

const tag = computed(() => {
  if (props.disabled) return 'button'
  if (props.to) return RouterLink
  if (props.href) return 'a'
  return 'button'
})

/**
 * Le reflet satiné n'a de sens que sur les boutons pleins : c'est leur surface
 * unie qui le porte. Sur un bouton au trait, il n'y a rien à faire briller.
 * Un bouton désactivé n'en reçoit pas non plus — il ne doit pas attirer l'œil.
 */
const SHEEN_VARIANTS = ['primary', 'light']

const classes = computed(() => [
  'inline-flex items-center justify-center gap-2.5 rounded-full font-sans font-medium uppercase',
  'transition-all duration-300 ease-out select-none',
  SIZES[props.size] || SIZES.md,
  VARIANTS[props.variant] || VARIANTS.primary,
  props.block ? 'w-full' : '',
  props.disabled ? 'cursor-not-allowed opacity-50 hover:!bg-inherit' : '',
  !props.disabled && SHEEN_VARIANTS.includes(props.variant) ? 'sheen' : '',
])

const attrs = computed(() => {
  if (props.disabled) return { type: props.type, disabled: true }
  if (props.to) return { to: props.to }
  if (props.href) {
    const external = props.href.startsWith('http')
    return {
      href: props.href,
      ...(external ? { target: '_blank', rel: 'noopener noreferrer' } : {}),
    }
  }
  return { type: props.type }
})
</script>

<template>
  <component :is="tag" v-bind="attrs" :class="classes">
    <slot />
  </component>
</template>
