<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import { useDropZone } from '@vueuse/core'
import { computed, ref } from 'vue'
import Dot from './components/Dot.vue'


const imageRef = ref<HTMLDivElement>()

const image = ref<string>('')
const fileName = ref<string>('')
const isInsideDropZone = ref(false)


function onDrop(files: File[] | null) {

  if (!files) return
  for (const file of files) {
    console.log(file.name)
    // display image in a div
    image.value = URL.createObjectURL(file)
    fileName.value = file.name
  }
  // called when files are dropped on zone
}


const { isOverDropZone } = useDropZone(imageRef, {
  onDrop,
  dataTypes: ['image/jpeg'],
  multiple: false,
  preventDefaultForUnhandled: false,
  onEnter: () => {
    isInsideDropZone.value = true
  },
  onLeave: () => {
    isInsideDropZone.value = false
  }
})


// dots positioned by top/right percentages relative to the image element
const dots = ref<Array<{ top: number; left: number }>>([])

function onImageClick(event: MouseEvent) {
  const targetEl = imageRef.value
  if (!targetEl) return
  const rect = targetEl.getBoundingClientRect()

  const x = event.clientX - rect.left;               // px from left edge of the div
  const y = event.clientY - rect.top;                // px from top edge of the div


  if (x < 0 || y < 0 || x > rect.width || y > rect.height) return


  // Convert to 0–100% and clamp just in case the click is on the border
  const xPct = (x / rect.width) * 100
  console.log(xPct)
  const yPct = Math.max(0, Math.min(100, (y / rect.height) * 100));


  dots.value.push({ top: Number(yPct.toFixed(2)), left: Number(xPct.toFixed(2)) })

  // dots.value.push({ top: 10, right: Number(xPct.toFixed(2)) })
}

function reset() {
  dots.value = []
}


const codes_1 = computed(() => {
  let c = '{\n'
  c += `  "image": "${image.value}"\n`
  c += `  "fileName": "${fileName.value}"\n`
  c += `  "dots": [\n`
  for (const dot of dots.value) {
    c += `    { "top": ${dot.top}, "left": ${dot.left} },\n`
  }
  c += `  ]\n`
  c += `}`

  return c
})
const codes_2 = computed(() => {
  let c = '{\n'
  for (const dot of dots.value) {
    c += `    { "top": ${dot.top}, "left": ${dot.left} },\n`
  }
  c += `  ]\n`
  c += `}`
  return c
})


function copyToClipboard_1() {
  navigator.clipboard.writeText(codes_1.value)
  console.log('Copied to clipboard', codes_1.value)
}
function copyToClipboard_2() {
  navigator.clipboard.writeText(codes_2.value)
  console.log('Copied to clipboard', codes_2.value)
}


function uploadImage() {
  const input = document.createElement('input')
  input.type = 'file'
  input.accept = 'image/*'
  input.onchange = (event) => {
    const file = (event.target as HTMLInputElement).files?.[0]
    if (file) {
      image.value = URL.createObjectURL(file)
      fileName.value = file.name
    }
  }
  input.click()
}
</script>

<template>

  <div class="h-8"></div>

  <div class="flex flex-col items-center mb-4">
    <div class="text-2xl font-bold mb-8">Drags And Dots</div>

    <!-- Description -->
    <div class='mb-2 text-lg'><span class="btn btn-lg" @click="uploadImage">Upload</span> / Drop Image Here</div>
    <div class="shadow-lg rounded-full p-1 bg-white animate-bounce mb-2"><svg xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24" fill="currentColor" aria-hidden="true" class="h-5 text-sky-500">
        <path fill-rule="evenodd"
          d="M12 2.25a.75.75 0 01.75.75v16.19l6.22-6.22a.75.75 0 111.06 1.06l-7.5 7.5a.75.75 0 01-1.06 0l-7.5-7.5a.75.75 0 111.06-1.06l6.22 6.22V3a.75.75 0 01.75-.75z"
          clip-rule="evenodd"></path>
      </svg></div>

    <!-- Image Container -->
    <div class="w-120 min-h-100 border-2 rounded-sm mb-4" style="position: relative; display: inline-block;"
      @click="onImageClick" ref="imageRef">
      <img :src="image" class="w-full" />
      <Dot v-for="(dot, index) in dots" :key="index" :top="dot.top" :left="dot.left" />
    </div>

    <div class="">
      <button class="btn btn-outline btn-secondary mb-4 mr-2" @click="reset">Reset Dots</button>
    </div>

    <div class="flex flex-row gap-2">
      <!-- Code Display -->
      <div class="flex flex-col items-center"><button class="btn btn-outline mb-4"
          @click="copyToClipboard_1">Copy</button>

        <div class="mockup-code w-80">
          <pre data-prefix="1"><code>{</code></pre>
          <pre data-prefix="2"><code>  "image": "{{ image }}"</code></pre>
          <pre data-prefix="3"><code>  "fileName": "{{ fileName }}"</code></pre>
          <pre data-prefix="4"><code>  "dots": [</code></pre>
          <pre v-for="(dot, index) in dots" :key="index"
            :data-prefix="5 + index"><code>    { "top": {{ dot.top }}, "left": {{ dot.left }} }<span v-if="index < dots.length - 1">,</span></code></pre>
          <pre :data-prefix="5 + dots.length"><code>  ]</code></pre>
          <pre :data-prefix="6 + dots.length"><code>}</code></pre>
        </div>
      </div>
      <!-- 
      {
        sku: "22-0050-0-19",
        top: "67%",
        left: "62%",
        url: "{{store direct_url='antony-cabinets01.html'}}",
      }, -->
      <div class="flex flex-col items-center"><button class="btn btn-outline mb-4"
          @click="copyToClipboard_2">Copy</button>
        <div class="mockup-code w-80">
          <Fragment v-for="(dot, index) in dots" :key="index">
            <!-- <pre :data-prefix="index"><code>{ "top": {{ dot.top }}, "left": {{ dot.left }} }</code></pre> -->
            <pre :data-prefix="index * 6"><code>{</code></pre>
            <pre :data-prefix="index * 6 + 1"><code>  "sku": "",</code></pre>
            <pre :data-prefix="index * 6 + 2"><code>  "url": "",</code></pre>
            <pre :data-prefix="index * 6 + 3"><code>  "top": {{ dot.top }},</code></pre>
            <pre :data-prefix="index * 6 + 4"><code>  "left": {{ dot.left }}</code></pre>
            <pre :data-prefix="index * 6 + 5"><code>}<span v-if="index < dots.length - 1">,</span></code></pre>
          </Fragment>
        </div>
      </div>
    </div>
  </div>


  <div class="h-20"></div>

</template>