---
layout: center
highlighter: shiki
css: unocss
colorSchema: dark
transition: fade-out
title: Experimenting with sli.dev
lineNumbers: false
drawings:
  persist: false
fonts:
  sans: 'Inter'
  serif: 'Architects Daughter'
  mono: 'Fira Code'
mdc: true
preload: false
routerMode: hash
addons:
  - slidev-addon-qrcode
  - fancy-arrow
---

# Experiment with sli.dev

Testing features

---
clicks: 3
---

# Test Mermaid Animations

<div
  class="mermaid-container transition-all duration-500"
  :class="{
    'scale-50': $clicks === 0,
    'scale-300 translate-x-200': $clicks === 1,
    'scale-200 translate-x-40 translate-y-10': $clicks === 2
  }"
>

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] --> B[Flag Evaluation]
    B --> C[OpenTelemetry]
    C --> D[Observability Platform]
    
    style A fill:#4a9eff
    style C fill:#f5a623
    style D fill:#6f6
```

</div>

---
layout: center
---

<div class="flex flex-col items-center">

# slidev-addon-qrcode

<QRCode
    :width="300"
    :height="300"
    type="svg"
    data="https://openfeature.dev"
    :margin="10"
    :imageOptions="{ margin: 10 }"
    :dotsOptions="{ type: 'rounded', color: 'rgb(221 221 221 / var(--un-text-opacity))' }"
/>

</div>

---

# Arrows!

<code data-id="anchor3" absolute left-460px top-180px>data-id=anchor3</code>
<code data-id="anchor4" absolute left-460px top-260px>data-id=anchor4</code>
<FancyArrow v-click
    from="[data-id=anchor3]@bottom"
    to="[data-id=anchor4]@top"
/>

<!-- 
Addon info: https://github.com/whitphx/slidev-addon-fancy-arrow
-->

---
clicks: 1
layout: center
---

<script setup>
import { ref, watch } from 'vue'
import { useSlideContext } from '@slidev/client'

const slideContext = useSlideContext()
const showIncident = ref(false)

watch(() => slideContext.$clicks.value, (newVal) => {
  if (newVal > 0) {
    setTimeout(() => {
      showIncident.value = true
    }, 2000)
  } else {
    showIncident.value = false
  }
})
</script>

# Service Failure Rate Monitor

<div class="flex items-center justify-center mt-8">
  <Charts class="bg-white rounded-lg shadow-2xl p-6" />
</div>

<Transition name="fade">
  <div v-if="showIncident" class="text-center mt-6 text-red-400 font-bold text-xl">
    ⚠️ Incident Detected: Failure Rate Spike!
  </div>
</Transition>

<style>
.fade-enter-active {
  transition: opacity 0.5s ease-in;
}

.fade-enter-from {
  opacity: 0;
}
</style>

---

