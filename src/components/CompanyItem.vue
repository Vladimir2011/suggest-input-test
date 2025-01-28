<template>
  <li
    v-if="company"
    class="suggest-dropdown-list-company"
    :class="{ 'suggest-dropdown-list-company--active': isActive }"
    role="option"
    :aria-selected="isActive"
    @click="handleClick"
  >
    <div class="company">
      <div class="company-avatar">
        <img v-if="company.avatar" :src="company.avatar" alt="avatar" />
        <img v-else src="@/public/no-company-avatar.png" alt="no-avatar" />
      </div>
      <div class="company-info">
        <div class="company-name">{{ company.name ? company.name : `@${company.alias}` }}</div>
        <div class="company-alias">Компания</div>
      </div>
    </div>
  </li>
</template>

<script setup lang="ts">
import type { CompanyType, UserType } from '@/types/types.ts'
interface CompanyProps {
  company: CompanyType | null
  isActive?: boolean
}

const props = withDefaults(defineProps<CompanyProps>(), {
  company: null,
  isActive: false,
})

const emit = defineEmits<{
  (e: 'selectCompany', company: UserType): void
}>()

const handleClick = () => {
  if (props.company) {
    emit('selectCompany', props.company)
  }
}
</script>

<style scoped>
.suggest-dropdown-list-company {
  .company {
    display: flex;
    gap: 10px;
    align-items: center;
    padding: 15px 10px;
    border-radius: 5px;

    .company-avatar {
      width: 30px;
      height: 30px;
      img {
        border-radius: 4px;
        width: inherit;
        height: inherit;
      }
    }

    .company-info {
      word-break: break-all;
      .company-name {
        font-size: 14px;
        font-weight: 500;
      }

      .company-alias {
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

.suggest-dropdown-list-company--active {
  background-color: #f0f0f0;
  color: #000;
}
</style>
