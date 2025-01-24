<template>
  <span class="tag">
    {{ `@${tag.alias}` }}
    <button @click.prevent="handlerRemoveTag" aria-label="Удалить">×</button>
  </span>
</template>

<script setup lang="ts">
import type { UserType, CompanyType } from '@/types/types.ts'

interface SuggestTagProps {
  tag: UserType | CompanyType
}

const props = defineProps<SuggestTagProps>()
const emit = defineEmits<{
  (e: 'removeTag', tag: UserType | CompanyType): void
}>()

const handlerRemoveTag = () => {
  emit('removeTag', props.tag)
}
</script>

<style scoped>
.tag {
  background: #6c8694;
  padding: 2px 5px;
  border-radius: 4px;
  display: flex;
  align-items: center;
  height: 30px;
  margin-left: 5px;

  color: white;
}

button {
  margin-left: 5px;
  background: none;
  border: none;
  color: white;

  @media (hover: hover) {
    cursor: pointer;
  }
}
</style>
