<script setup lang="ts">
import { Pane, Splitpanes } from 'splitpanes'
import type { ServerRouteInput } from '../types'
import { ServerRouteInputType } from '../types'

const [DefineDefaultInputs, UseDefaultInputs] = createReusableTemplate()

const requestMethodClass: Record<string, string> = {
  get: 'bg-green-400:10 text-green-400',
  post: 'bg-blue-400:10 text-blue-400',
  put: 'bg-orange-400:10 text-orange-400',
  delete: 'bg-red-400:10 text-red-400',
  patch: 'bg-purple-400:10 text-purple-400',
  head: 'bg-teal-400:10 text-teal-400',
  default: 'bg-gray-400:10 text-gray-400',
}

function getRequestMethodClass(method: string) {
  return requestMethodClass[method.toLowerCase()] || requestMethodClass.default
}

const requestMethod = ref('GET')

const methods = ref([
  'GET',
  'POST',
  'PUT',
  'DELETE',
  'PATCH',
  'HEAD',
])

const requestUrl = ref('')

const activeTab = ref('query')

const tabs = ref([
  {
    name: 'Query',
    slug: 'query',
    length: 1,
  },
  {
    name: 'Body',
    slug: 'body',
    length: 1,
  },
  {
    name: 'Headers',
    slug: 'headers',
    length: 1,
  },
])

const ServerRouteTabIcons: Record<string, string> = {
  headers: 'i-carbon-html-reference',
  params: 'i-carbon-text-selection',
  query: 'i-carbon-help',
  body: 'i-carbon-document',
}

const routeInputs = reactive({
  query: [{ key: '', value: '', type: 'string' }] as ServerRouteInput[],
  body: [{ key: '', value: '', type: 'string' }] as ServerRouteInput[],
  headers: [{ key: 'Content-Type', value: 'application/json', type: 'string' }] as ServerRouteInput[],
})

type RouteInputs = keyof typeof routeInputs
const currentParams = computed({
  get: () => routeInputs[activeTab.value as RouteInputs],
  set: (value: any) => {
    routeInputs[activeTab.value as RouteInputs] = value
  },
})
</script>

<template>
  <Splitpanes>
    <Pane border="r base">
      <div h-full w-full flex="~ col">
        <div flex="~ col gap-2" flex-none p4 navbar-glass>
          <div flex="~ gap2">
            <VDSelect v-model="requestMethod" :class="getRequestMethodClass(requestMethod)">
              <option v-for="method of methods" :key="method" :class="getRequestMethodClass(method)">
                {{ method.toUpperCase() }}
              </option>
            </VDSelect>
            <div relative w-full>
              <VDTextInput
                v-model="requestUrl"
                flex-auto font-mono
                p="x5 y2"
                n="primary xs"
              />
              <VDButton
                v-tooltip="'Copy URL'"
                title="Copy URL"
                absolute right-2 top-1.7
                n="xs blue"
              >
                <VDIcon icon="carbon:copy" />
              </VDButton>
            </div>
            <VDButton n="primary solid">
              <VDIcon icon="carbon:send" />
            </VDButton>
          </div>
        </div>

        <div flex="~ gap2 wrap" w-full items-center px4 pb2 text-center text-sm border="b base">
          <VDButton
            v-for="tab in tabs"
            :key="tab.slug"
            :class="activeTab === tab.slug ? 'text-primary n-primary' : 'border-transparent shadow-none'"
            @click="activeTab = tab.slug"
          >
            <VDIcon :icon="ServerRouteTabIcons[tab.slug]" />
            {{ tab.name }}
            {{ tab?.length ? `(${tab.length})` : '' }}
            <span />
          </VDButton>
          <div flex-auto />
        </div>
        <DefineDefaultInputs />
      </div>
    </Pane>
  </Splitpanes>
</template>
