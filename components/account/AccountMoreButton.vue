<script setup lang="ts">
import type { mastodon } from 'masto'
import { toggleBlockAccount, toggleMuteAccount } from '~~/composables/masto/relationship'

const { account } = defineProps<{
  account: mastodon.v1.Account
  command?: boolean
}>()
const emit = defineEmits<{
  (evt: 'addNote'): void
  (evt: 'removeNote'): void
}>()

let relationship = $(useRelationship(account))

const isSelf = $(useSelfAccount(() => account))

const { t } = useI18n()
const { client } = $(useMasto())
const useStarFavoriteIcon = usePreferences('useStarFavoriteIcon')
const { share, isSupported: isShareSupported } = useShare()

function shareAccount() {
  share({ url: location.href })
}

async function toggleReblogs() {
  if (!relationship!.showingReblogs && await openConfirmDialog({
    title: t('confirm.show_reblogs.title'),
    description: t('confirm.show_reblogs.description', [account.displayName]),
    confirm: t('confirm.show_reblogs.confirm'),
    cancel: t('confirm.show_reblogs.cancel'),
  }) !== 'confirm')
    return

  const showingReblogs = !relationship?.showingReblogs
  relationship = await client.v1.accounts.$select(account.id).follow({ reblogs: showingReblogs })
}

async function addUserNote() {
  emit('addNote')
}

async function removeUserNote() {
  if (!relationship!.note || relationship!.note.length === 0)
    return

  const newNote = await client.v1.accounts.$select(account.id).note.create({ comment: '' })
  relationship!.note = newNote.note
  emit('removeNote')
}
</script>

<template>
  <CommonDropdown :eager-mount="command">
    <button flex gap-1 items-center w-full rounded op75 hover="op100 text-purple" group aria-label="More actions">
      <div rounded-5 p2 elk-group-hover="bg-purple/10">
        <div i-ri:more-2-fill />
      </div>
    </button>

    <template #popper>
      <CommonDropdownItem
        v-if="isShareSupported"
        :text="$t('menu.share_account', [`@${account.displayName}`])"
        icon="i-ri:share-line"
        :command="command"
        @click="shareAccount()"
      />

      <template v-if="currentUser">
        <template v-if="!isSelf">
          <CommonDropdownItem
            :text="$t('menu.mention_account', [`@${account.displayName}`])"
            icon="i-ri:at-line"
            :command="command"
            @click="mentionUser(account)"
          />
          <CommonDropdownItem
            :text="$t('menu.direct_message_account', [`@${account.displayName}`])"
            icon="i-ri:message-3-line"
            :command="command"
            @click="directMessageUser(account)"
          />

          <CommonDropdownItem
            v-if="!relationship?.showingReblogs"
            icon="i-ri:repeat-line"
            :text="$t('menu.show_reblogs', [`@${account.displayName}`])"
            :command="command"
            @click="toggleReblogs()"
          />
          <CommonDropdownItem
            v-else
            :text="$t('menu.hide_reblogs', [`@${account.displayName}`])"
            icon="i-ri:repeat-line"
            :command="command"
            @click="toggleReblogs()"
          />

          <CommonDropdownItem
            v-if="!relationship?.note || relationship?.note?.length === 0"
            :text="$t('menu.add_personal_note', [`@${account.displayName}`])"
            icon="i-ri-edit-2-line"
            :command="command"
            @click="addUserNote()"
          />
          <CommonDropdownItem
            v-else
            :text="$t('menu.remove_personal_note', [`@${account.displayName}`])"
            icon="i-ri-edit-2-line"
            :command="command"
            @click="removeUserNote()"
          />

          <CommonDropdownItem
            v-if="!relationship?.muting"
            :text="$t('menu.mute_account', [`@${account.displayName}`])"
            icon="i-ri:volume-mute-line"
            :command="command"
            @click="toggleMuteAccount (relationship!, account)"
          />
          <CommonDropdownItem
            v-else
            :text="$t('menu.unmute_account', [`@${account.displayName}`])"
            icon="i-ri:volume-up-fill"
            :command="command"
            @click="toggleMuteAccount (relationship!, account)"
          />

          <CommonDropdownItem
            v-if="!relationship?.blocking"
            :text="$t('menu.block_account', [`@${account.displayName}`])"
            icon="i-ri:forbid-2-line"
            :command="command"
            @click="toggleBlockAccount (relationship!, account)"
          />
          <CommonDropdownItem
            v-else
            :text="$t('menu.unblock_account', [`@${account.displayName}`])"
            icon="i-ri:checkbox-circle-line"
            :command="command"
            @click="toggleBlockAccount (relationship!, account)"
          />

          <CommonDropdownItem
            :text="$t('menu.report_account', [`@${account.displayName}`])"
            icon="i-ri:flag-2-line"
            :command="command"
            @click="openReportDialog(account)"
          />
        </template>

        <template v-else>
          <NuxtLink to="/pinned">
            <CommonDropdownItem :text="$t('account.pinned')" icon="i-ri:pushpin-line" :command="command" />
          </NuxtLink>
          <NuxtLink to="/favourites">
            <CommonDropdownItem :text="$t('account.favourites')" :icon="useStarFavoriteIcon ? 'i-ri:star-line' : 'i-ri:heart-3-line'" :command="command" />
          </NuxtLink>
          <NuxtLink to="/mutes">
            <CommonDropdownItem :text="$t('account.muted_users')" icon="i-ri:volume-mute-line" :command="command" />
          </NuxtLink>
          <NuxtLink to="/blocks">
            <CommonDropdownItem :text="$t('account.blocked_users')" icon="i-ri:forbid-2-line" :command="command" />
          </NuxtLink>
        </template>
      </template>
    </template>
  </CommonDropdown>
</template>
