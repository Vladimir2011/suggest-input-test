<template>
  <li
    v-if="user"
    class="suggest-dropdown-list__user"
    :class="{ 'suggest-dropdown-list__user_active': isActive }"
    role="option"
    :aria-selected="isActive"
    @click="handleClick"
  >
    <div class="user">
      <div class="user__avatar">
        <img v-if="user.avatar" :src="user.avatar" alt="avatar" />
        <img v-else src="@/public/no-user-avatar.png" alt="no-avatar" />
      </div>
      <div class="user__info">
        <div class="user__name">{{ user.name ? user.name : `@${user.alias}` }}</div>
        <div class="user__alias">@{{ user.alias }}</div>
      </div>
    </div>
  </li>
</template>

<script setup lang="ts">
import type { UserType } from '@/types/types.ts'
interface UserProps {
  user: UserType | null
  isActive: boolean
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
.suggest-dropdown-list__user {
  .user {
    display: flex;
    gap: 10px;
    align-items: center;
    padding: 15px 10px;
    border-radius: 5px;

    .user__avatar {
      width: 30px;
      height: 30px;
      img {
        border-radius: 4px;
        width: inherit;
        height: inherit;
      }
    }

    .user__info {
      word-break: break-all;
      .user__name {
        font-size: 14px;
        font-weight: 500;
      }

      .user__alias {
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

.suggest-dropdown-list__user_active {
  background-color: #f0f0f0;
  color: #000;
}
</style>
