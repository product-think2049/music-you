<template>
  <v-btn v-if="logged" icon class="account-avatar" @click="showProfile = !showProfile">
    <v-avatar size="32">
      <v-img :aspect-ratio="1" contain :src="profile.avatarUrl" :lazy-src="placeholderUrl" />
    </v-avatar>
  </v-btn>
  <v-btn v-else icon flat @click="showLogin = !showLogin">
    <v-icon>
      {{ mdiAccountCircle }}
    </v-icon>
  </v-btn>
  <user-profile v-model="showProfile" />
</template>
<script setup lang="ts">
import { mdiAccountCircle } from '@mdi/js'
import { storeToRefs } from 'pinia'
import { computed, ref } from 'vue'

import placeholderUrl from '@/assets/placeholder.png'
import UserProfile from '@/pages/modal/Profile.vue'
import { useAppStore } from '@/store/app'
import { useUserStore } from '@/store/user'

const appStore = useAppStore()
const userStore = useUserStore()
const { showLogin } = storeToRefs(appStore)
const { logged, account } = storeToRefs(userStore)

const profile = computed(() => {
  return account.value?.profile ?? {}
})

const showProfile = ref<boolean>(false)
</script>
