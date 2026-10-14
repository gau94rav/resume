<script setup>
import { ref, watch, onMounted, nextTick } from 'vue'
defineOptions({ inheritAttrs: false })
const props = defineProps({ src: { type: String, required: true }, alt: { type: String, required: true } })
const pending = ref(true)
const failed = ref(false)
const attempt = ref(0)
const element = ref(null)
const finish = () => { pending.value = false; failed.value = false }
const fail = () => { pending.value = false; failed.value = true }
const checkCachedImage = () => {
 const img = element.value
 if (img?.complete && img.naturalWidth > 0) finish()
}
const retry = () => { pending.value = true; failed.value = false; attempt.value++ }
watch(() => props.src, async () => {
 pending.value = true; failed.value = false
 await nextTick(); checkCachedImage()
})
onMounted(checkCachedImage)
</script>

<template>
 <img :key="`${src}-${attempt}`" ref="element" v-bind="$attrs" :src="src" :alt="alt" :class="{ 'screenshot-pending': pending, 'screenshot-failed': failed }" :aria-busy="pending" @load="finish" @error="fail">
 <span v-if="pending" class="screenshot-loader screenshot-glitch-loader" role="status">
  <span class="screenshot-glitch-icon" aria-hidden="true"><i></i><i></i><i></i></span>
  <span class="screenshot-glitch-label">Loading screenshot<span class="loader-text-echo echo-one" aria-hidden="true">Loading screenshot</span><span class="loader-text-echo echo-two" aria-hidden="true">Loading screenshot</span></span>
 </span>
 <span v-else-if="failed" class="screenshot-loader screenshot-load-error" role="status"><span>Screenshot couldn’t load.</span><button @click="retry">Retry</button></span>
</template>
