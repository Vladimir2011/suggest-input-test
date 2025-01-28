<template>
  <div v-if="instance" class="input-wrapper">
    <label class="label" :for="String(instance.uid)"
      ><span class="label-required" v-if="label">*</span>{{ label }}</label
    >
    <div v-if="localErrorValue" class="error">{{ localErrorValue }}</div>
    <div class="input-container">
      <input
        :id="String(instance.uid)"
        type="text"
        :value="modelValue"
        @input="updateInputValue"
        class="input"
        :class="{ 'input-error': localErrorValue }"
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
  modelValue: string
}

const props = withDefaults(defineProps<SuggestInputProps>(), {
  label: '',
  placeholder: '',
  error: null,
  tags: () => [],
  isDisabled: false,
  modelValue: '',
})

const emit = defineEmits<{
  (e: 'remove-tag', tag: UserType | CompanyType): void
  (e: 'update:modelValue', value: string): void
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

const updateInputValue = (event: Event) => {
  const target = event.target as HTMLInputElement
  emit('update:modelValue', target.value)
}

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
.input-wrapper {
  display: flex;
  flex-direction: column;
  gap: 8px;

  .error {
    width: fit-content;
    padding: 5px;
    font-size: 12px;
    background-color: #cf3b3c;
    border-radius: 3px;
    color: white;
  }

  .label {
    display: flex;
    gap: 4px;
    font-size: 14px;
    font-weight: 600;
    color: rgba(0, 0, 0, 0.87);

    .label-required {
      color: red;
    }
  }

  .input-container {
    display: flex;
    align-items: center;
    position: relative;
    .input {
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

    .input-error {
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
