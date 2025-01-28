<template>
  <div :style="{ maxHeight: dropDownMaxHeight }" class="suggest-dropdown" ref="dropdownRef">
    <ul
      ref="list"
      role="listbox"
      class="dropdown-list"
      v-show="isListIsOpen"
      @keydown.down="handleKeyDown"
      @keydown.up="handleKeyUp"
      tabindex="0"
      aria-label="Список саджестов"
      :aria-expanded="isListIsOpen"
    >
      <slot />
    </ul>
    <AppLoader v-if="isLoading" />
    <div class="dropdown-empty" v-else-if="isDropdownListEmpty">Пусто... =(</div>
  </div>
</template>

<script setup lang="ts">
import type { UserType, CompanyType } from '@/types/types.ts'
import { computed, nextTick, ref, useTemplateRef, watch, onMounted, onUnmounted } from 'vue'
import AppLoader from '@/components/Common/AppLoader.vue'

interface suggestDropdownProps {
  dropdownList: (UserType | CompanyType)[] | null
  isLoading: boolean
  error: string | null
}

const props = withDefaults(defineProps<suggestDropdownProps>(), {
  dropdownList: () => [],
  isLoading: false,
  error: '',
})

const emits = defineEmits<{
  (e: 'closeDropdown'): void
}>()

const listOfSuggests = useTemplateRef<HTMLUListElement | null>('list')
const dropDownMaxHeight = ref<string>('')
const activeIndex = ref<number>(-1)
const dropdownRef = useTemplateRef<HTMLElement | null>('dropdownRef')

const handleClickOutside = (event: MouseEvent) => {
  if (
    props.dropdownList &&
    dropdownRef.value &&
    !dropdownRef.value.contains(event.target as Node)
  ) {
    emits('closeDropdown')
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})

const isListIsOpen = computed(() => {
  return !props.isLoading && !!getDropDownListLength.value
})

const getDropDownListLength = computed(() => {
  if (props.dropdownList) return props.dropdownList.length
  return false
})

const isDropdownListEmpty = computed(() => {
  return !props.isLoading && !getDropDownListLength.value && props.dropdownList && !props.error
})

watch(
  () => props.dropdownList,
  async () => {
    if (props.dropdownList && props.dropdownList.length >= 4) {
      await nextTick()

      let height = 0
      if (listOfSuggests.value) {
        listOfSuggests.value.focus()
        activeIndex.value = 0
        ;[...listOfSuggests.value!.children].forEach((item, index) => {
          if (3 >= index) {
            height += item.clientHeight
          }
        })
      }

      dropDownMaxHeight.value = `${height}px`
    }
  },
)
</script>

<style scoped>
.suggest-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
  width: 100%;
  max-width: 200px;
  max-height: 260px;
  overflow-y: scroll;
  background-color: #fff;
  border-radius: 4px;
  box-shadow: rgba(100, 100, 111, 0.2) 0 7px 29px 0;

  .dropdown-list {
    padding: 0;
    margin: 0;
    outline: none;
  }

  .dropdown-empty {
    position: static;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    width: 100%;
    font-weight: 700;
    min-height: 60px;
  }
}
</style>
