<script setup lang="ts">
import { mdiClose, mdiCog, mdiGithub, mdiLogout } from '@mdi/js'
import { useIpcRenderer } from '@vueuse/electron'
import { storeToRefs } from 'pinia'
import { useRouter } from 'vue-router'

import { useUserStore } from '@/store/user'
import type { Account } from '@/types'
import is from '@/util/is'
const userStore = useUserStore()
const router = useRouter()
const { account } = storeToRefs(userStore)

const profile = computed((): Account['profile'] | undefined => {
  return account.value?.profile
})

const props = defineProps({
  modelValue: Boolean,
})

const emit = defineEmits(['update:modelValue'])

const show = computed<boolean>({
  get() {
    return props.modelValue
  },
  set(value) {
    emit('update:modelValue', value)
  },
})

const isVip = computed(() => {
  return account.value?.profile.vipType === 11
})
const goto = (url: string) => {
  if (is.electron()) {
    const ipcRenderer = useIpcRenderer()
    ipcRenderer.invoke('open-url', url)
  } else {
    window.open(url, '_blank')
  }
}

function dispatch(type: string) {
  switch (type) {
    case 'sign_out':
      signOut()
      show.value = false
      break
    case 'settings':
      show.value = false
      router.push('/setting')
      break
  }
}
const signOut = () => {
  userStore.signOut()
}
</script>
<template>
  <v-dialog v-model="show">
    <v-card flat color="surface" width="50vw" max-width="450" rounded="xl" class="align-self-center">
      <div class="px-2 pt-2">
        <v-btn icon variant="plain" @click="show = false">
          <v-icon> {{ mdiClose }}</v-icon>
        </v-btn>
      </div>

      <v-list-item v-if="profile">
        <template #prepend>
          <v-avatar size="56">
            <v-img :src="profile.avatarUrl" />
          </v-avatar>
        </template>
        <div>
          <v-list-item-title
            >{{ profile.nickname
            }}<v-chip v-if="isVip" class="ml-2" label size="small" color="primary">黑胶VIP</v-chip></v-list-item-title
          >
          <v-list-item-subtitle class="text-caption"> {{ profile.signature }}</v-list-item-subtitle>
          <v-btn class="mt-2" variant="outlined" size="small" @click="goto('https://music.163.com/#/user/update')">
            {{ $t('main.setting.manage_account') }}
          </v-btn>
        </div>
      </v-list-item>
      <v-divider class="my-2" />
      <v-list-item class="px-6" @click="dispatch('settings')">
        <template #prepend>
          <div>
            <v-icon>{{ mdiCog }}</v-icon>
          </div>
        </template>
        <v-list-item-title class="ml-2"> {{ $t('common.setting') }} </v-list-item-title>
      </v-list-item>
      <v-list-item class="px-6" @click="dispatch('sign_out')">
        <template #prepend>
          <div>
            <v-icon>{{ mdiLogout }}</v-icon>
          </div>
        </template>
        <v-list-item-title class="ml-2">{{ $t('message.logout') }}</v-list-item-title>
      </v-list-item>
      <div class="mt-auto py-2">
        <span class="d-flex justify-center align-center">
          <v-btn variant="text" size="small"> {{ $t('message.disclaimer') }} </v-btn>
          ·
          <v-btn
            variant="text"
            size="small"
            class="d-flex align-center"
            @click="goto('https://github.com/GuMengYu/v-player')"
            ><v-icon small>{{ mdiGithub }}</v-icon> github</v-btn
          >
        </span>
      </div>
    </v-card>
  </v-dialog>
</template>
