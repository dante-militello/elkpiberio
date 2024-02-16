<script setup lang="ts">
import type { mastodon } from 'masto'
import { computed } from 'vue'

const { account, hideEmojis = false } = defineProps<{
  account: mastodon.v1.Account
  hideEmojis?: boolean
}>()

// Listas de usernames verificados
const verificados = ['dantezor', 'dzor']
const donadores = ['dasdsdsdsad', 'dzor1']

// URLs de las imágenes de verificado alojadas en Imgur
const imagen_verificado = 'https://i.imgur.com/Dww9YpY.png'
const verificado_donador = 'https://i.imgur.com/waG7dp2.png'

// Verifica si el account.displayName está en alguna de las listas de verifiedUsernames
const isVerifiedType1 = computed(() => verificados.includes(account.username))
const isVerifiedType2 = computed(() => donadores.includes(account.username))
</script>

<template>
  <div :class="{ glitter_donadores: isVerifiedType2 }" style="display: flex; align-items: center;">
    <ContentRich
      :content="getDisplayName(account, { rich: true })"
      :emojis="account.emojis"
      :hide-emojis="hideEmojis"
      :markdown="false"
    />
    <img v-if="isVerifiedType1" :src="imagen_verificado" alt="Verified Type 1" style="margin-left: 8px; width: 17px; height: 17px;">
    <img v-if="isVerifiedType2" :src="verificado_donador" alt="Verified Type 2" style="margin-left: 8px; width: 17px; height: 17px;">
  </div>
</template>

<style scoped>
.glitter_donadores {
  background: url("https://i.imgur.com/jTXdcLB.gif") repeat scroll 0 0;
  color: #ffd400;
  text-shadow: 0px 0px 0 #ffd4004d, 1px 1px 0 #000000, 1px 1px 6px #ffd4004d, 1px 1px 9px #ffd400;
}
</style>
