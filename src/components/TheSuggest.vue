<template>
  <div class="suggest">
    <SuggestInput
      v-model="inputValue"
      :label
      :placeholder="'Введите логин'"
      @input="loadSuggestions"
      :error="error"
      :tags="tags"
      :is-disabled="isInputDisabled"
      @removeTag="handleRemoveTag"
    />
    <SuggestDropdown
      :dropdown-list="suggestionList"
      :is-loading="isLoading"
      :error="error"
      @selectUser="handleSelectUser"
      @selectCompany="handleSelectCompany"
      @close-dropdown="handleDropdownClose"
    />
  </div>
</template>

<script setup lang="ts">
import { computed, onUnmounted, ref, watch } from 'vue'
import type { UserType, CompanyType } from '@/types/types'
import SuggestInput from '@/components/SuggestInput.vue'
import SuggestDropdown from '@/components/SuggestDropdown.vue'

let debounceTimeout: ReturnType<typeof setTimeout> | null = null
const inputValue = ref('')
const suggestionList = ref<(UserType | CompanyType)[] | null>(null)
const isLoading = ref<boolean>(false)
const error = ref<string | null>(null)
const tags = ref<(UserType | CompanyType)[]>([])

interface SuggestProps {
  url?: string
  limitOfTags?: number
  label?: string
}

const props = withDefaults(defineProps<SuggestProps>(), {
  url: 'https://habr.com/kek/v2/publication/suggest-mention',
  limitOfTags: 1,
  label: 'Пользователь или компания',
})

const loadSuggestions = () => {
  suggestionList.value = null
  error.value = null
  clearTimeout(debounceTimeout!)

  debounceTimeout = setTimeout(async () => {
    if (inputValue.value.length > 2) {
      isLoading.value = true
      try {
        // Симулируем 500 ошибку
        if (inputValue.value === 'simulate500') {
          throw new Error('500: Internal Server Error')
        }

        const response = await fetch(`${props.url}?q=${inputValue.value}`)

        if (!response.ok) {
          if (response.status === 400) {
            const errorResponse = await response.clone().json()
            error.value = errorResponse.message
          }

          if (response.status === 500) {
            throw new Error('500: Internal server error')
          }
        }

        if (response.ok) {
          const { data } = await response.json()
          suggestionList.value = data
        }
      } catch (e) {
        error.value = e instanceof Error ? e.message : 'An unknown error occurred'
      } finally {
        isLoading.value = false
      }
    }
  }, 600)
}

const isInputDisabled = computed(() => {
  return tags.value.length >= props.limitOfTags
})

const checkIfAnObjectInAnArray = (obj: UserType | CompanyType, arr: (UserType | CompanyType)[]) => {
  return arr.some((item) => {
    if (item.type === obj.type) {
      return item.alias === obj.alias
    }
  })
}

const handleSelectUser = (user: UserType) => {
  if (props.limitOfTags > tags.value.length) {
    if (checkIfAnObjectInAnArray(user, tags.value)) {
      error.value = 'Пользователь уже добавлен'
    } else {
      tags.value.push(user)
    }
  } else {
    error.value = 'Превышен лимит добавленных пользователей/компаний'
    suggestionList.value = null
    inputValue.value = ''
  }
}

const handleSelectCompany = (company: CompanyType) => {
  if (props.limitOfTags > tags.value.length) {
    if (checkIfAnObjectInAnArray(company, tags.value)) {
      error.value = 'Компания уже добавлена'
    } else {
      tags.value.push(company)
    }
  } else {
    error.value = 'Превышен лимит добавленных пользователей/компаний'
    suggestionList.value = null
    inputValue.value = ''
  }
}

const handleRemoveTag = (tag: UserType | CompanyType) => {
  tags.value = tags.value.filter((item) => item.name !== tag.name)
}

const handleDropdownClose = () => {
  suggestionList.value = null
}

onUnmounted(() => {
  if (debounceTimeout) {
    clearTimeout(debounceTimeout)
  }
})

watch(error, () => {
  const timeoutId = setTimeout(() => {
    error.value = null
  }, 3000)

  return () => clearTimeout(timeoutId)
})

watch(inputValue, () => {
  if (inputValue.value && inputValue.value.length < 3) {
    suggestionList.value = null
  }
})

watch(isInputDisabled, () => {
  if (inputValue.value) {
    inputValue.value = ''
  }
})
</script>

<style scoped>
.suggest {
  position: relative;
}
</style>
