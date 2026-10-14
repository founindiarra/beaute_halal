<script setup>
import AppIcon from '@/components/ui/AppIcon.vue'
import AppImage from '@/components/ui/AppImage.vue'
import BaseButton from '@/components/ui/BaseButton.vue'
import { useIsWide } from '@/composables/useBreakpoint'
import { useSalon } from '@/composables/useSalon'
import { images } from '@/data/images'

const { salon } = useSalon()

/** Le bandeau existe en deux cadrages : on ne charge que celui qui s'affiche. */
const isLarge = useIsWide()
</script>

<template>
  <section class="relative isolate overflow-hidden bg-cocoa-dark">
    <!-- Visuel -->
    <!--
      Sur mobile, le header est FIXE et opaque : une photo posée en `inset-0`
      démarre derrière lui et ressort tranchée net sous la barre blanche, les
      cheveux coupés au rasoir. On décale donc le haut de l'image de la
      hauteur du header, et le brun du bandeau fait la transition. À partir
      de « sm » la photo reprend toute la hauteur : la composition y est
      latérale, le problème n'existe pas.
    -->
    <div class="absolute inset-x-0 top-[4.25rem] bottom-0 -z-10 sm:top-0">
      <!--
        Deux cadrages de la même photo, et non un seul étiré : en bandeau
        large le sujet doit venir à droite pour laisser la place au texte,
        alors qu'en portrait il doit monter dans le tiers haut pour que le
        texte se pose dessous. Aucun recadrage automatique ne sait faire les
        deux à la fois, d'où les deux fichiers.
      -->
      <AppImage
        v-if="isLarge"
        :src="images.hero"
        alt="Locks soignées réalisées au salon Beauté Halal"
        ratio="2/1"
        eager
        decorative
        class="h-full w-full"
        img-class="object-[72%_45%]"
      />
      <AppImage
        v-else
        :src="images.heroMobile"
        alt="Locks soignées réalisées au salon Beauté Halal"
        ratio="3/5"
        eager
        decorative
        class="h-full w-full"
        img-class="object-[50%_14%]"
      />
      <!--
        La photo n'est plus noyée sous un voile uniforme : elle doit rester
        franchement visible, c'est elle l'accroche. L'ombre ne couvre donc que
        la zone du texte et s'efface avant d'atteindre le visage.

        À partir de « sm », le texte est à gauche : l'ombre va de gauche à
        droite. Sur mobile il occupe toute la largeur et passe en bas : elle
        monte donc du bas, et le haut de la photo reste net.
      -->
      <div
        class="absolute inset-0 bg-gradient-to-t from-cocoa-dark/82 from-36% via-cocoa-dark/18 via-54% to-transparent to-66% sm:bg-gradient-to-r sm:from-cocoa-dark/62 sm:from-10% sm:via-cocoa-dark/22 sm:via-38% sm:to-transparent sm:to-58%"
      />
      <!-- Raccord discret avec la section crème qui suit. -->
      <div
        class="absolute inset-x-0 bottom-0 h-24 bg-gradient-to-t from-cocoa-dark/85 to-transparent"
      />
    </div>

    <!--
      Hauteur : assez généreuse pour que la photo se lise entièrement — c'est
      elle l'accroche — mais pas au point de repousser les prestations hors
      de l'écran.
      « pt » doit rester supérieur à la hauteur de l'en-tête fixe
      (mesurée à 65 px sur mobile, 86 px à partir de « lg »).
    -->
    <div
      class="container-page flex min-h-[calc(84svh+4.25rem)] flex-col justify-end pt-20 pb-10 sm:min-h-[70svh] sm:justify-center sm:pt-24 sm:pb-12 lg:min-h-[74svh] lg:pt-28 lg:pb-14"
    >
      <div class="texte-sur-photo max-w-2xl">
        <span class="eyebrow text-gold-soft">{{ salon.specialty }}</span>

        <h1 class="mt-3 text-[2rem] leading-[1.05] text-cream sm:text-[2.7rem] lg:text-[3.1rem]">
          {{ salon.name }}
        </h1>

        <p
          v-if="salon.tagline"
          class="mt-2.5 max-w-xl font-display text-base text-gold-soft sm:text-lg lg:text-xl"
        >
          {{ salon.tagline }}
        </p>

        <span class="rule-gold rule-gold-light mt-5 block w-24 rounded-full" aria-hidden="true" />

        <p class="mt-3 max-w-xl text-[0.92rem] leading-relaxed text-cream/75">
          Création, entretien, réparation, coiffure, coloration et soins : un savoir-faire
          entièrement dédié aux locks, dans un cadre calme et attentionné.
        </p>

        <div class="mt-6 flex flex-col gap-3 sm:flex-row sm:flex-wrap sm:items-center">
          <BaseButton :to="{ name: 'booking' }" variant="light" size="lg" class="w-full sm:w-auto">
            Prendre rendez-vous
          </BaseButton>
          <BaseButton
            :to="{ name: 'services' }"
            variant="outline-light"
            size="lg"
            class="w-full sm:w-auto"
          >
            Découvrir nos services
            <AppIcon name="arrow-right" class="h-4 w-4" />
          </BaseButton>
        </div>
      </div>
    </div>
  </section>
</template>
