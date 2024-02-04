<script setup lang="ts">
const buildInfo = useBuildInfo()
const timeAgoOptions = useTimeAgoOptions()
const config = useRuntimeConfig()
const userSettings = useUserSettings()

const buildTimeDate = new Date(buildInfo.time)
const buildTimeAgo = useTimeAgo(buildTimeDate, timeAgoOptions)

const colorMode = useColorMode()
function toggleDark() {
  colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark'
}
</script>

<template>
  <footer p4 text-sm text-secondary-light flex="~ col">
    <div flex="~ gap2" items-center mb4>
      <CommonTooltip :content="$t('nav.toggle_theme')">
        <button flex i-ri:sun-line dark-i-ri:moon-line text-lg :aria-label="$t('nav.toggle_theme')" @click="toggleDark()" />
      </CommonTooltip>
      <CommonTooltip :content="$t('nav.zen_mode')">
        <button
          flex
          text-lg
          :class="getPreferences(userSettings, 'zenMode') ? 'i-ri:layout-right-2-line' : 'i-ri:layout-right-line'"
          :aria-label="$t('nav.zen_mode')"
          @click="togglePreferences('zenMode')"
        />
      </CommonTooltip>
      <CommonTooltip :content="$t('magic_keys.dialog_header')">
        <button flex i-ri:keyboard-box-line dark-i-ri:keyboard-box-line text-lg :aria-label="$t('magic_keys.dialog_header')" @click="toggleKeyboardShortcuts" />
      </CommonTooltip>
    </div>
    <div>
      <NuxtLink cursor-pointer hover:underline to="/settings/about">
        {{ $t('settings.about.label') }}
      </NuxtLink>
      <template v-if="config.public.privacyPolicyUrl">
        &middot;
        <NuxtLink cursor-pointer hover:underline :to="config.public.privacyPolicyUrl">
          {{ $t('nav.privacy') }}
        </NuxtLink>
      </template>
      &middot;
      <NuxtLink href="https://discord.gg/rWpwRwQ9sV" target="_blank" external>
        Discord
      </NuxtLink>
      &middot;
      <NuxtLink href="https://dzor.net" target="_blank" external>
        Dzor
      </NuxtLink>
    </div>
  </footer>
</template>
