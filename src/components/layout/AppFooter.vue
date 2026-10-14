<script setup>
import AppIcon from '@/components/ui/AppIcon.vue'
import BrandMark from '@/components/ui/BrandMark.vue'
import { useSalon } from '@/composables/useSalon'
import { useWhatsapp } from '@/composables/useWhatsapp'

const { salon } = useSalon()
// Le lien WhatsApp passe par le composable : une seule règle pour tout le site.
const { buildWhatsappLink } = useWhatsapp()

const navLinks = [
  { name: 'home', label: 'Accueil' },
  { name: 'services', label: 'Services' },
  { name: 'gallery', label: 'Nos coiffures' },
  { name: 'about', label: 'À propos' },
  { name: 'booking', label: 'Rendez-vous' },
  { name: 'contact', label: 'Contact' },
]

const year = new Date().getFullYear()
</script>

<template>
  <footer class="text-cream/75">
    <!--
      Vague de raccord. Le pied de page n'a pas de fond à lui : c'est ce tracé
      qui le pose, sa partie basse étant pleine et sa partie haute vide. La
      section crème au-dessus s'écoule donc dans le brun par une courbe, sans
      arête.

      « preserveAspectRatio=none » laisse la vague s'étirer en largeur : c'est
      voulu, une vague étirée reste une vague, alors qu'une vague à ratio fixe
      changerait de hauteur selon la largeur de l'écran.
    -->
    <svg
      class="block h-12 w-full text-cocoa-dark sm:h-16 lg:h-20"
      viewBox="0 0 1440 120"
      preserveAspectRatio="none"
      aria-hidden="true"
      focusable="false"
    >
      <path
        d="M0 44 C 210 4 430 0 660 34 C 880 66 1130 92 1440 56 L1440 120 L0 120 Z"
        fill="currentColor"
      />
    </svg>

    <div class="bg-cocoa-dark">
      <div
        class="container-page grid gap-6 py-6 sm:grid-cols-2 sm:gap-x-8 sm:gap-y-7 sm:py-7 lg:grid-cols-12 lg:py-8"
      >
        <!-- Présentation -->
        <div class="flex flex-col gap-3 sm:col-span-2 lg:col-span-4">
          <BrandMark tone="light" />
          <p class="max-w-sm text-sm leading-relaxed">
            {{ salon.shortDescription }}
          </p>

          <!--
          Réseaux sociaux : intitulés comme les autres colonnes du pied de page,
          pour se repérer au même titre que Navigation, Contact et Horaires.
          Le bloc disparaît entièrement tant qu'aucun compte n'est renseigné
          dans le dashboard — on n'affiche pas un titre sans contenu.
        -->
          <div v-if="salon.socials?.length" class="flex flex-col gap-3 pt-1">
            <h3 class="text-[0.72rem] uppercase tracking-[0.24em] text-gold-soft">Suivez-nous</h3>
            <div class="flex flex-wrap items-center gap-3">
              <a
                v-for="social in salon.socials"
                :key="social.id"
                :href="social.url"
                target="_blank"
                rel="noopener noreferrer"
                class="grid h-10 w-10 place-items-center rounded-full border border-cream/20 text-cream/80 transition-colors hover:border-gold-soft hover:text-gold-soft"
                :aria-label="social.label"
              >
                <AppIcon :name="social.id" class="h-[1.15rem] w-[1.15rem]" />
              </a>
            </div>
          </div>
        </div>

        <!-- Navigation -->
        <nav class="flex flex-col gap-2.5 lg:col-span-3" aria-label="Navigation de pied de page">
          <h3 class="text-[0.72rem] uppercase tracking-[0.24em] text-gold-soft">Navigation</h3>
          <ul class="grid grid-cols-2 gap-x-6 gap-y-1.5 sm:grid-cols-2 lg:grid-cols-1">
            <li v-for="link in navLinks" :key="link.name">
              <RouterLink
                :to="{ name: link.name }"
                class="inline-flex min-h-[30px] items-center text-sm transition-colors hover:text-gold-soft"
              >
                {{ link.label }}
              </RouterLink>
            </li>
          </ul>
        </nav>

        <!-- Contact -->
        <div class="flex flex-col gap-2.5 lg:col-span-3">
          <h3 class="text-[0.72rem] uppercase tracking-[0.24em] text-gold-soft">Contact</h3>
          <ul class="flex flex-col gap-2 text-sm">
            <li v-if="salon.phone.display">
              <a
                :href="salon.phone.href"
                class="inline-flex items-start gap-3 transition-colors hover:text-gold-soft"
              >
                <AppIcon name="phone" class="mt-0.5 h-4 w-4 shrink-0 text-gold-soft" />
                {{ salon.phone.display }}
              </a>
            </li>
            <li>
              <a
                v-if="buildWhatsappLink()"
                :href="buildWhatsappLink()"
                target="_blank"
                rel="noopener noreferrer"
                class="inline-flex items-start gap-3 transition-colors hover:text-gold-soft"
              >
                <AppIcon name="whatsapp" class="mt-0.5 h-4 w-4 shrink-0 text-gold-soft" />
                WhatsApp
              </a>
              <span v-else class="inline-flex items-start gap-3 text-cream/50">
                <AppIcon name="whatsapp" class="mt-0.5 h-4 w-4 shrink-0 text-gold-soft/60" />
                WhatsApp — numéro bientôt disponible
              </span>
            </li>
            <li v-if="salon.email">
              <a
                :href="`mailto:${salon.email}`"
                class="inline-flex items-start gap-3 break-all transition-colors hover:text-gold-soft"
              >
                <AppIcon name="mail" class="mt-0.5 h-4 w-4 shrink-0 text-gold-soft" />
                {{ salon.email }}
              </a>
            </li>
            <li v-if="salon.address.city" class="inline-flex items-start gap-3">
              <AppIcon name="map-pin" class="mt-0.5 h-4 w-4 shrink-0 text-gold-soft" />
              <span>
                {{ salon.address.line1 }}<br />
                {{ salon.address.line2 }}<br />
                {{ salon.address.city }}
              </span>
            </li>
          </ul>
        </div>

        <!-- Horaires -->
        <div class="flex flex-col gap-2.5 lg:col-span-2">
          <h3 class="text-[0.72rem] uppercase tracking-[0.24em] text-gold-soft">Horaires</h3>
          <ul class="flex flex-col gap-1 text-[0.8rem]">
            <li
              v-for="slot in salon.hours"
              :key="slot.day"
              class="flex items-baseline justify-between gap-3"
              :class="slot.isClosed ? 'text-cream/45' : ''"
            >
              <span>{{ slot.day }}</span>
              <span class="text-right text-xs">{{ slot.value }}</span>
            </li>
          </ul>
        </div>
      </div>

      <div class="border-t border-cream/10">
        <div
          class="container-page flex flex-col items-center justify-between gap-1.5 py-2.5 text-center text-xs text-cream/55 sm:flex-row sm:text-left"
        >
          <p>© {{ year }} {{ salon.name }} — Tous droits réservés.</p>
          <p>
            <RouterLink :to="{ name: 'contact' }" class="transition-colors hover:text-gold-soft">
              Mentions légales et contact
            </RouterLink>
          </p>
        </div>
      </div>
    </div>
  </footer>
</template>
