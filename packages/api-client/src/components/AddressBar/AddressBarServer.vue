<script setup lang="ts">
import { commandPaletteBus } from '@/libs'
import { useWorkspace } from '@/store'
import {
  ScalarDropdown,
  ScalarDropdownDivider,
  ScalarDropdownItem,
  ScalarIcon,
} from '@scalar/components'
import { computed, watch } from 'vue'

import AddressBarServerItem from './AddressBarServerItem.vue'

const {
  activeRequest,
  activeCollection,
  activeServer,
  isReadOnly,
  servers,
  collectionMutators,
} = useWorkspace()

const requestServerOptions = computed(() =>
  activeRequest.value?.servers?.map((serverUid: string) => ({
    id: serverUid,
    label: servers[serverUid]?.url ?? 'Unknown server',
  })),
)

const collectionServerOptions = computed(() =>
  activeCollection.value?.servers?.map((serverUid: string) => ({
    id: serverUid,
    label: servers[serverUid]?.url ?? 'Unknown server',
  })),
)

/** If we have both request and collection servers we show the labels */
const showDropdownLabels = computed(
  () =>
    requestServerOptions.value?.length && collectionServerOptions.value?.length,
)

// Ensure we always have a selected server
watch([activeCollection, activeRequest], ([collection, request]) => {
  if (!collection || collection.selectedServerUid || request?.selectedServerUid)
    return

  collectionMutators.edit(
    collection.uid,
    'selectedServerUid',
    collection.servers[0],
  )
})

/** Add server */
const handleAddServer = () =>
  commandPaletteBus.emit({
    commandName: 'Add Server',
  })
</script>
<template>
  <template
    v-if="
      !isReadOnly &&
      (requestServerOptions?.length || collectionServerOptions?.length)
    ">
    <ScalarDropdown teleport="#scalar-client">
      <button
        class="font-code lg:text-sm text-xs whitespace-nowrap border border-b-3 border-solid rounded px-1.5 py-0.5 text-c-2 z-[1]"
        type="button"
        @click.stop>
        {{ activeServer?.url }}
      </button>
      <template #items>
        <!-- Request -->
        <div
          v-if="showDropdownLabels"
          class="text-xxs text-c-2 ml-8">
          Request Servers
        </div>
        <AddressBarServerItem
          v-for="serverOption in requestServerOptions"
          :key="serverOption.id"
          :serverOption="serverOption"
          type="request" />

        <template v-if="showDropdownLabels">
          <ScalarDropdownDivider />
          <div class="text-xxs text-c-2 ml-8">Collection Servers</div>
        </template>

        <!-- Collection -->
        <AddressBarServerItem
          v-for="serverOption in collectionServerOptions"
          :key="serverOption.id"
          :serverOption="serverOption"
          type="collection" />

        <ScalarDropdownDivider />
        <ScalarDropdownItem>
          <div
            class="font-code text-xxs flex items-center gap-1.5"
            @click="handleAddServer">
            <div class="flex items-center justify-center h-4 w-4">
              <ScalarIcon
                class="h-2.5"
                icon="Add" />
            </div>
            <span>Add Server</span>
          </div>
        </ScalarDropdownItem>
      </template>
    </ScalarDropdown>
  </template>
  <template v-else-if="activeServer?.url">
    <div
      class="flex whitespace-nowrap items-center font-code lg:text-sm text-xs">
      {{ activeServer.url }}
    </div>
  </template>
</template>
