<script lang="ts" setup>
import { mdiDotsVertical } from '@mdi/js'
import dayjs from 'dayjs'

import placeHolderUrl from '@/assets/placeholder.png'
import type { Comment } from '@/types'
// coding here
const props = defineProps<{
  comment: Comment
}>()
function formatDate(datetime: string | number, format = 'LL') {
  return dayjs(datetime).format(format)
}
</script>
<template>
  <div>
    <header>
      <div class="d-flex justify-space-between">
        <div class="d-flex gap-3 align-center">
          <v-avatar size="32">
            <v-img :aspect-ratio="1" contain :lazy-src="placeHolderUrl" :src="comment.user.avatarUrl" />
          </v-avatar>

          <span class="text-body-2">{{ comment.user.nickname }}</span>
        </div>
        <div>
          <v-btn icon variant="text"
            ><v-icon> {{ mdiDotsVertical }}</v-icon></v-btn
          >
        </div>
      </div>
      <div class="mt-2 text-caption">
        {{ formatDate(comment.time) }}
      </div>
    </header>

    <div class="mt-2 text-body-2">
      {{ comment.content }}
    </div>
    <slot />
  </div>
</template>
