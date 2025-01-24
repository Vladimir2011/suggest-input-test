<template>
  <li
    v-if="company"
    class="suggest-dropdown-list__company"
    :class="{ 'suggest-dropdown-list__company_active': isActive }"
    role="option"
    :aria-selected="isActive"
    @click="handleClick"
  >
    <div class="company">
      <div class="company__avatar">
        <img v-if="company.avatar" :src="company.avatar" alt="avatar" />
        <img v-else src="@/public/no-company-avatar.png" alt="no-avatar" />
      </div>
      <div class="company__info">
        <div class="company__name">{{ company.name ? company.name : `@${company.alias}` }}</div>
        <div class="company__alias">Компания</div>
      </div>
    </div>
  </li>
</template>

<script setup lang="ts">
import type { CompanyType, UserType } from '@/types/types.ts'
interface CompanyProps {
  company: CompanyType | null
  isActive: boolean
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
.suggest-dropdown-list__company {
  .company {
    display: flex;
    gap: 10px;
    align-items: center;
    padding: 15px 10px;
    border-radius: 5px;

    .company__avatar {
      width: 30px;
      height: 30px;
      img {
        border-radius: 4px;
        width: inherit;
        height: inherit;
      }
    }

    .company__info {
      word-break: break-all;
      .company__name {
        font-size: 14px;
        font-weight: 500;
      }

      .company__alias {
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

.suggest-dropdown-list__company_active {
  background-color: #f0f0f0;
  color: #000;
}
</style>
