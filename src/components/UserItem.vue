<template>
  <li
    v-if="user"
    class="suggest-dropdown-list-user"
    :class="{ 'suggest-dropdown-list-user--active': isActive }"
    role="option"
    :aria-selected="isActive"
    @click="handleClick"
  >
    <div class="user">
      <div class="user-avatar">
        <img v-if="user.avatar" :src="user.avatar" alt="avatar" />
        <img v-else src="@/public/no-user-avatar.png" alt="no-avatar" />
      </div>
      <div class="user-info">
        <div class="user-name">{{ user.name ? user.name : `@${user.alias}` }}</div>
        <div class="user-alias">@{{ user.alias }}</div>
      </div>
    </div>
  </li>
</template>

<script setup lang="ts">
import type { UserType } from '@/types/types.ts'
interface UserProps {
  user: UserType | null
  isActive?: boolean
}

const props = withDefaults(defineProps<UserProps>(), {
  user: null,
  isActive: false,
})

const emit = defineEmits<{
  (e: 'selectUser', user: UserType): void
}>()

const handleClick = () => {
  if (props.user) {
    emit('selectUser', props.user)
  }
}
</script>

<style scoped>
.suggest-dropdown-list-user {
  .user {
    display: flex;
    gap: 10px;
    align-items: center;
    padding: 15px 10px;
    border-radius: 5px;

    .user-avatar {
      width: 30px;
      height: 30px;
      img {
        border-radius: 4px;
        width: inherit;
        height: inherit;
      }
    }

    .user-info {
      word-break: break-all;
      .user-name {
        font-size: 14px;
        font-weight: 500;
      }

      .user-alias {
        font-size: 14px;
        color: grey;
      }
    }
  }

  @media (hover: hover) {
    :hover {
      cursor: pointer;
      background-color: #f0f0f0;
      color: #000;
    }
  }
}

.suggest-dropdown-list-user--active {
  background-color: #f0f0f0;
  color: #000;
}
</style>
