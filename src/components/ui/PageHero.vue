<script setup>
import { computed } from 'vue'

import { useIsWide } from '@/composables/useBreakpoint'

import AppImage from './AppImage.vue'

/**
 * Bandeau d'en-tête commun aux pages intérieures.
 *
 * La mise en forme est celle de l'accueil, mais chaque page fournit sa propre
 * photo : le fond flouté, le marron et la photo nette sont déjà fondus dans
 * le fichier, il ne reste ici que l'ombre qui porte le titre.
 */
const props = defineProps({
  eyebrow: { type: String, default: '' },
  title: { type: String, required: true },
  description: { type: String, default: '' },
  image: { type: String, default: null },
  /**
   * Cadrage portrait pour les écrans étroits. Tailler le bandeau large dans
   * le sens de la hauteur donnait une bande où le visage tombait pile là où
   * commence le texte ; ici la photo occupe le haut et le texte le bas.
   * Sans valeur, le bandeau large sert aux deux.
   */
  imageMobile: { type: String, default: null },
  /**
   * Photo occupant tout le bandeau, sans sujet détouré. L'ombre passe alors
   * en bas — c'est le seul endroit où elle est nécessaire, puisque le texte y
   * descend lui aussi — et le reste de la photo se lit à nu.
   */
  pleinCadre: { type: Boolean, default: false },
  /** Partie de la photo à préserver (valeur object-position, par palier). */
  cadrage: { type: String, default: null },
})

/** On ne charge que le cadrage réellement affiché. */
const isLarge = useIsWide()

const source = computed(() =>
  !isLarge.value && props.imageMobile ? props.imageMobile : props.image,
)
const portrait = computed(() => source.value === props.imageMobile && Boolean(props.imageMobile))
</script>

<template>
  <section class="relative isolate overflow-hidden bg-cocoa-dark">
    <!-- Même décalage que sur l'accueil : la photo ne touche pas le header. -->
    <div v-if="source" class="absolute inset-x-0 top-[4.25rem] bottom-0 -z-10 sm:top-0">
      <AppImage
        :src="source"
        :ratio="portrait ? '3/5' : '13/5'"
        alt=""
        eager
        decorative
        class="h-full w-full"
        :img-class="cadrage || (portrait ? 'object-[50%_12%]' : 'object-[50%_45%]')"
      />
      <!--
        Sujet détouré : l'ombre de l'accueil, aux mêmes valeurs — de gauche à
        droite sur écran large, du bas vers le haut sur téléphone. Toute
        reprise de l'une doit être répercutée sur l'autre.

        Photo plein cadre : l'ombre ne monte que du bas, sous le texte, et
        laisse tout le reste de l'image à nu.
      -->
      <div
        v-if="pleinCadre"
        class="absolute inset-0 bg-gradient-to-t from-cocoa-dark from-22% via-cocoa-dark/58 via-46% to-transparent to-70%"
      />
      <div
        v-else
        class="absolute inset-0 bg-gradient-to-t from-cocoa-dark/82 from-36% via-cocoa-dark/18 via-54% to-transparent to-66% sm:bg-gradient-to-r sm:from-cocoa-dark/62 sm:from-10% sm:via-cocoa-dark/22 sm:via-38% sm:to-transparent sm:to-58%"
      />
    </div>

    <!--
      Hauteur : de quoi laisser la photo se lire. En dessous, le cadrage la
      rognait tellement qu'il n'en restait qu'un fragment.
      « pt » doit rester supérieur à la hauteur de l'en-tête fixe (mesurée à
      68 px sur mobile et 86 px à partir de « lg »), sinon le titre passerait
      dessous. Toute reprise de la hauteur de l'en-tête doit être répercutée ici.
    -->
    <div
      class="container-page relative flex min-h-[calc(80svh+4.25rem)] flex-col justify-end pt-20 pb-9 sm:min-h-[54svh] sm:pt-24 sm:pb-12 lg:min-h-[58svh] lg:pt-28 lg:pb-14"
      :class="pleinCadre ? 'justify-end' : 'sm:justify-center'"
    >
      <div class="texte-sur-photo max-w-2xl">
        <span v-if="eyebrow" class="eyebrow text-gold-soft">{{ eyebrow }}</span>
        <h1 class="mt-3 text-[1.7rem] leading-tight text-cream sm:text-[2.1rem] lg:text-[2.6rem]">
          {{ title }}
        </h1>
        <span class="mt-3.5 block h-px w-16 rounded-full bg-gold-soft/60" />
        <p v-if="description" class="mt-3.5 text-[0.95rem] leading-relaxed text-cream/75">
          {{ description }}
        </p>
        <slot />
      </div>
    </div>
  </section>
</template>
