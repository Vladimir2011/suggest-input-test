<template>
  <div v-if="instance" class="suggest-input">
    <label class="suggest-input__label" :for="String(instance.uid)"
      ><span class="suggest-input-label_required" v-if="label">*</span>{{ label }}</label
    >
    <div v-if="localErrorValue" class="suggest-input__error">{{ localErrorValue }}</div>
    <div class="suggest-input__container">
      <input
        :id="String(instance.uid)"
        type="text"
        v-model="inputValue"
        class="suggest-input__input"
        :class="{ 'suggest-input__input_error': localErrorValue }"
        :placeholder="placeholder"
        :disabled="isDisabled"
        :style="{ paddingLeft: paddingLeftForInput }"
      />

      <div class="tags" ref="tags">
        <SuggestTag v-for="tag in tags" :key="tag.alias" :tag="tag" @remove-tag="handleRemoveTag" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { getCurrentInstance, ref, watch, useTemplateRef, nextTick } from 'vue'
import { useWindowSize } from '@/composables/useWindowSize.ts'
import type { UserType, CompanyType } from '@/types/types.ts'
import SuggestTag from '@/components/SuggestTag.vue'

interface SuggestInputProps {
  label: string
  placeholder: string
  error: string | null
  tags: (UserType | CompanyType)[]
  isDisabled: boolean
}

const props = withDefaults(defineProps<SuggestInputProps>(), {
  label: '',
  placeholder: '',
  error: null,
  tags: () => [],
  isDisabled: false,
})

const emit = defineEmits<{
  (e: 'remove-tag', tag: UserType | CompanyType): void
}>()

const handleRemoveTag = (tag: UserType | CompanyType) => {
  emit('remove-tag', tag)
}

const localErrorValue = ref(props.error)
const instance = getCurrentInstance()
const inputValue = defineModel()
const tagsList = useTemplateRef<HTMLElement | null>('tags')
const paddingLeftForInput = ref<string>('10px')
const { width: windowWidth } = useWindowSize()

watch(
  () => props.error,
  (newValue) => {
    localErrorValue.value = newValue
  },
)

watch(
  () => props.tags.length,
  async () => {
    await nextTick()
    if (tagsList.value) {
      paddingLeftForInput.value = `${tagsList.value.offsetWidth + 10}px`
    }
  },
  { immediate: true },
)

watch(windowWidth, () => {
  if (tagsList.value) {
    paddingLeftForInput.value = `${tagsList.value.offsetWidth + 10}px`
  }
})
</script>

<style scoped>
.suggest-input {
  display: flex;
  flex-direction: column;
  gap: 8px;

  .suggest-input__error {
    width: fit-content;
    padding: 5px;
    font-size: 12px;
    background-color: #cf3b3c;
    border-radius: 3px;
    color: white;
  }

  .suggest-input__label {
    display: flex;
    gap: 4px;
    font-size: 14px;
    font-weight: 600;
    color: rgba(0, 0, 0, 0.87);

    .suggest-input-label_required {
      color: red;
    }
  }

  .suggest-input__container {
    display: flex;
    align-items: center;
    position: relative;
    .suggest-input__input {
      flex: 1;
      width: 100%;
      min-height: 40px;
      min-width: 40px;
      padding: 0 12px;
      border-radius: 3px;
      border: 1px solid #ededed;
      background-color: transparent;
      color: #464646;

      &::placeholder {
        color: #c4c4c4;
      }

      &:focus {
        background-color: #f5f6f7;
        border-color: #999;
        outline: none;
      }

      &:disabled {
        cursor: not-allowed;
        background-color: #c4c4c4;
        opacity: 0.5;
      }
    }

    .suggest-input__input_error {
      border: 1px solid red;

      &:focus {
        border-color: red;
      }
    }

    .tags {
      position: absolute;
      display: flex;
      flex-wrap: wrap;
    }
  }
}
</style>
