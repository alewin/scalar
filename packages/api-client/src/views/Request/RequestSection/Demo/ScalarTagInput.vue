<script setup lang="ts">
import {
  TagsInputInput,
  TagsInputItem,
  TagsInputItemDelete,
  TagsInputItemText,
  TagsInputRoot,
} from 'radix-vue'
import { Icon } from '@iconify/vue'

type Props = {
  modelValue: string[]
  placeholder?: string
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: 'Enter values...',
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: string[]): void
}>()

const updateModelValue = (newValue: string[]) => {
  emit('update:modelValue', newValue)
}
</script>

<template>
  <TagsInputRoot
    class="flex gap-2 items-center border p-2 rounded-lg w-full flex-wrap border-whiteA7 bg-white text-black"
    :modelValue="modelValue"
    @update:modelValue="updateModelValue">
    <TagsInputItem
      v-for="item in modelValue"
      :key="item"
      class="text-black flex shadow-md items-center justify-center gap-2 bg-white text-black aria-[current=true]:bg-red9 rounded p-1"
      :value="item">
      <TagsInputItemText class="text-sm pl-1" />
      <TagsInputItemDelete
        class="p-0.5 rounded bg-transparent hover:bg-whiteA4">
        <Icon icon="lucide:x" />
      </TagsInputItemDelete>
    </TagsInputItem>

    <TagsInputInput
      class="text-sm focus:outline-none flex-1 rounded text-black bg-white placeholder:text-blackA9 px-1"
      :placeholder="placeholder" />
  </TagsInputRoot>
</template>

<script lang="ts">
export default {
  name: 'ScalarTagInput',
}
</script>
