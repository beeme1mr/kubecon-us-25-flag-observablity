---
layout: center
highlighter: shiki
css: unocss
colorSchema: dark
transition: fade-out
title: From "What Broke" to "What Changed"
exportFilename: KubeCon NA 2025 - Feature Flags as First-Class concept in Observability
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
info: |
  ## Feature Flags as First-Class Concept in Observability
  KubeCon + CloudNativeCon 2025

  Learn how to integrate feature flag observability into your workflow using OpenFeature and OpenTelemetry standards.
---

<div translate-x--14>
  <h1>
    From <span class="font-serif">"What Broke"</span> to <span class="font-serif">"What Changed"</span>
  </h1>

  <p>Michael Beemer, Dynatrace<br>Parth Suthar, DevCycle</p>
</div>

<div w-full absolute bottom-0 left-0 flex items-center transform="translate-x--10 translate-y--10">
  <div w-full flex items-center justify-end gap-4>
    <img src="/kccnc-na-2025-white.svg" h-20 translate-y-4>
  </div>
</div>

<!--
Welcome everyone! Today we're going to talk about a critical gap in modern observability - feature flags.
-->

---
layout: intro
class: px-35
---

<div flex>
  <div
    v-click="1" flex flex-col items-center transition duration-500 ease-in-out
    :class="$clicks < 1 ? 'translate-y-20 opacity-0' : 'translate-y-0 opacity-100'"
  >
    <img src="/mike.png" w-50 h-50 rounded-full object-cover mb-5>
    <span font-semibold text-3xl >Michael Beemer</span>
    <div items-center>
      <div>
        <span class="opacity-70">Senior Product Manager</span>
      </div>
      <div text-sm flex items-center justify-center gap-2 mt-4>
        <div i-ri:github-fill /><span underline decoration-dashed font-mono decoration-zinc-300>beeme1mr</span>
      </div>
    </div>
  </div>
  <div flex-1 />
  <div
    v-click="2" flex flex-col items-center transition duration-500 ease-in-out
    :class="$clicks < 2 ? 'translate-y-20 opacity-0' : 'translate-y-0 opacity-100'"
  >
    <img src="/parth.png" w-50 h-50 rounded-full object-cover mb-5>
    <span font-semibold text-3xl>Parth Suthar</span>
    <div items-center>
      <div>
        <span class="opacity-70">Software Engineer</span>
      </div>
      <div text-sm flex items-center justify-center gap-2 mt-4>
        <div i-ri:github-fill /><span underline decoration-dashed font-mono decoration-zinc-300>suthar26</span>
      </div>
    </div>
  </div>
</div>

---
layout: two-cols
class: px-6 py-4
---

<div class="pr-4">

# <span class="text-red-400">3:25 PM</span> Monday

<div class="text-xs opacity-70 mb-4">A familiar story...</div>

<IncidentTimeline :visible-items="$clicks" />

</div>

::right::

<div class="pl-4 pt-4">
  <ProblemCard 
    service-name="checkout-service"
    :error-rate="7.14"
    timestamp="Nov 11, 2025, 3:25 PM"
  />
  
  <div v-click="7" class="mt-3 p-3 bg-red-900/20 border border-red-800 rounded-lg text-xs" style="box-shadow: 0 0 15px rgba(239, 68, 68, 0.2);">
    <div class="font-bold text-red-400 mb-1">⚠️ The Missing Context</div>
    <div class="opacity-80">Feature flag changes are <span class="font-bold">invisible</span> to traditional monitoring</div>
  </div>
</div>

<!--
This is the reality for teams using feature flags without proper observability.
The problem card shows everything EXCEPT what actually changed - the feature flag.
This timeline plays out daily in engineering teams everywhere.
3+ hours wasted because the monitoring tool couldn't tell us about the flag change.
-->

---
layout: center
---

<div class="text-center text-3xl mt-12">

Feature flags are <span class="text-red-400">hidden</span> from observability tools

<v-click>

making it <span class="text-orange-400">difficult</span> to pinpoint changes

</v-click>
<v-click>

as the <span class="text-blue-400">root cause</span> of incidents

</v-click>

</div>

<!--
This is the core problem we're solving today. Feature flags operate in the shadows.
-->

---
class: py-10
hide: true
---

# What Are Feature Flags?

<span>Runtime configuration for modern software delivery</span>

<div mt-6 />

<div grid grid-cols-2 gap-6>

<div>
<v-clicks>

<div class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden p-4 mb-3 style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center mb-2>
    <div i-carbon:settings-adjust text-blue-300 text-lg mr-2 />
    <span font-semibold>Toggle Control</span>
  </div>
  <div text-sm opacity-70>Turn features on/off without deploying code</div>
</div>

<div class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden p-4 mb-3 style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center mb-2>
    <div i-carbon:rocket text-green-300 text-lg mr-2 />
    <span font-semibold>Progressive Rollouts</span>
  </div>
  <div text-sm opacity-70>Gradual releases and canary deployments</div>
</div>

<div class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden p-4 mb-3 style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center mb-2>
    <div i-carbon:chart-multitype text-purple-300 text-lg mr-2 />
    <span font-semibold>A/B Testing</span>
  </div>
  <div text-sm opacity-70>Experiments and variant testing</div>
</div>

<div class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden p-4 mb-3 style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center mb-2>
    <div i-carbon:user-access text-amber-300 text-lg mr-2 />
    <span font-semibold>Access Control</span>
  </div>
  <div text-sm opacity-70>Permissions and targeting rules</div>
</div>

</v-clicks>
</div>

<div v-click="5">

<div class="bg-gradient-card border border-purple-light rounded-lg p-2" style="box-shadow: 0 0 20px rgba(109, 118, 255, 0.25);">

```typescript
const client = OpenFeature.getClient();

const showNewCheckout = client.getBooleanValue(
  'new-checkout-flow',
  false
);

if (showNewCheckout) {
  return <NewCheckoutComponent />;
}
```

</div>

</div>

</div>

<!--
Feature flags are powerful - they let us deploy code that's dormant until we're ready.
-->

---
class: py-10
---

# The Observability Gap

<span>What traditional monitoring misses</span>

<div mt-8 />

<div grid grid-cols-2 gap-6>

<div v-click border="1.5 solid blue-500" rounded-lg overflow-hidden bg="blue-900/20" backdrop-blur-sm style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38);">
  <div flex items-center px-4 py-3 style="background: linear-gradient(135deg, rgba(30, 64, 175, 0.4), rgba(29, 78, 216, 0.5));">
    <div i-carbon:view text-blue-300 text-xl mr-3 />
    <h3 class="text-blue-400 font-semibold text-lg">What We See</h3>
  </div>
  <div px-4 py-4>
    <div flex flex-col gap-3>
      <div flex items-center>
        <div i-carbon:warning-alt text-red-400 mr-2 />
        <span>Error rates spike</span>
      </div>
      <div flex items-center>
        <div i-carbon:time text-orange-400 mr-2 />
        <span>Latency increases</span>
      </div>
      <div flex items-center>
        <div i-carbon:close-filled text-red-400 mr-2 />
        <span>Failed requests</span>
      </div>
      <div flex items-center>
        <div i-carbon:chart-area text-purple-400 mr-2 />
        <span>Resource exhaustion</span>
      </div>
    </div>
    <div class="mt-4 text-sm opacity-70 italic">
      Traditional metrics & traces
    </div>
  </div>
</div>

<div v-click border="1.5 solid red-500" rounded-lg overflow-hidden bg="red-900/20" backdrop-blur-sm style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38);">
  <div flex items-center px-4 py-3 style="background: linear-gradient(135deg, rgba(127, 29, 29, 0.4), rgba(153, 27, 27, 0.5));">
    <div i-carbon:view-off text-red-300 text-xl mr-3 />
    <h3 class="text-red-400 font-semibold text-lg">What's Hidden</h3>
  </div>
  <div px-4 py-4>
    <div flex flex-col gap-3>
      <div flex items-center>
        <div i-carbon:flag text-amber-400 mr-2 />
        <span>Which flag changed?</span>
      </div>
      <div flex items-center>
        <div i-carbon:time text-amber-400 mr-2 />
        <span>When was it toggled?</span>
      </div>
      <div flex items-center>
        <div i-carbon:user text-amber-400 mr-2 />
        <span>Who was affected?</span>
      </div>
      <div flex items-center>
        <div i-carbon:branch text-amber-400 mr-2 />
        <span>What variant was served?</span>
      </div>
    </div>
    <div class="mt-4 text-sm opacity-70 italic">
      Feature flag context
    </div>
  </div>
</div>

</div>

<!-- <div v-click class="mt-6 text-center opacity-70">
[IMAGE PLACEHOLDER: Screenshot of trace or dashboard with NO reference to feature flags]
</div> -->

<!--
Observability tools show us the symptoms, but not the cause when flags are involved.
-->

---
class: py-10
---

# A Real-World Example: OpenTelemetry Demo

<span>Feature flags that trigger production-like issues</span>

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

<v-clicks>

<div>

### Astronomy Shop

<div class="mt-2 text-sm opacity-80">

- Full **microservices e-commerce** platform
- 10+ services, multiple languages
- Production-grade observability stack
- Uses **OpenFeature** for feature flagging

</div>
</div>

<div class="mt-4 p-3 bg-purple-900/20 border border-purple-800 rounded-lg" style="box-shadow: 0 0 20px rgba(141,141,255,0.25);">
<div class="text-sm font-semibold text-purple-300 mb-2">🎯 Perfect Test Bed</div>
<div class="text-xs opacity-80">
Includes flags that deliberately enable problems to demonstrate observability challenges
</div>
</div>

</v-clicks>

</div>

<div v-click>

### Problem Scenarios

<div class="mt-3 space-y-2">

<div class="p-3 bg-red-900/20 border border-red-800 rounded-lg" style="box-shadow: 0 0 15px rgba(239, 68, 68, 0.2);">
<div class="font-semibold text-sm mb-1 flex items-center">
<div i-carbon:warning-alt text-red-400 mr-2 />
<code class="text-xs">recommendationServiceCacheFailure</code>
</div>
<div class="text-xs opacity-70">Memory leak → 1.4x exponential growth → OOM crashes</div>
</div>

<div class="p-3 bg-orange-900/20 border border-orange-800 rounded-lg" style="box-shadow: 0 0 15px rgba(249, 115, 22, 0.2);">
<div class="font-semibold text-sm mb-1 flex items-center">
<div i-carbon:warning-alt text-orange-400 mr-2 />
<code class="text-xs">paymentServiceUnreachable</code>
</div>
<div class="text-xs opacity-70">Invalid endpoint → all payment requests fail</div>
</div>

<div class="p-3 bg-amber-900/20 border border-amber-800 rounded-lg" style="box-shadow: 0 0 15px rgba(251, 146, 60, 0.2);">
<div class="font-semibold text-sm mb-1 flex items-center">
<div i-carbon:warning-alt text-amber-400 mr-2 />
<code class="text-xs">imageSlowLoad</code>
</div>
<div class="text-xs opacity-70">Fault injection → 5s image load latency</div>
</div>

</div>

</div>

</div>

<!--
The OTel demo provides realistic scenarios where flags cause issues that mimic code bugs.
This is exactly what happens in production - flags change behavior, but observability is blind to them.
-->

---
class: py-10
---

# Impact vs Root Cause

<span>Observability shows the <span class="text-red-400">symptoms</span>, but hides the <span class="text-purple-400">diagnosis</span></span>

<div class="grid grid-cols-2 gap-6">
<div v-click border="1.5 solid red-500" rounded-lg overflow-hidden bg="red-900/20" backdrop-blur-sm style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38);">
  <div flex items-center px-4 py-3 style="background: linear-gradient(135deg, rgba(127, 29, 29, 0.4), rgba(153, 27, 27, 0.5));">
    <div i-carbon:chart-line text-red-300 text-xl mr-3 />
    <h3 class="text-red-300 font-semibold">What Your Dashboard Shows</h3>
  </div>
  <div px-4 py-4>

  <div class="space-y-3">
    <div class="border border-red-500/30 rounded-lg overflow-hidden" style="box-shadow: 0 4px 20px rgba(239, 68, 68, 0.15);">
      <img src="/response-time-spike.png" class="w-full" alt="Response time spike chart" />
    </div>
    <div class="p-2 bg-red-900/30 border border-red-800 rounded-lg text-xs" style="box-shadow: 0 0 15px rgba(239, 68, 68, 0.2);">
      <div class="text-xs space-y-1">
        <div>🚨 Clear Impact: Response time spiked from 145ms to 600ms!</div>
        <div>❓ Unknown Cause: No code changes, no deploys...</div>
      </div>
    </div>
  </div>
  </div>
</div>

<div v-click border="1.5 solid purple-500" rounded-lg overflow-hidden bg="purple-900/20" backdrop-blur-sm style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38);">
  <div flex items-center px-4 py-3 style="background: linear-gradient(135deg, rgba(141, 141, 255, 0.32), rgba(183, 185, 255, 0.18));">
    <div i-carbon:flag text-purple-300 text-xl mr-3 />
    <h3 class="text-purple-300 font-semibold">What's Actually Happening</h3>
  </div>
  <div px-4 py-4>

<div class="space-y-3">


<img src="/audit-log.png" class="w-full rounded-lg" style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);" />


<div class="mt-3 p-3 bg-purple-900/30 border border-purple-800 rounded-lg" style="box-shadow: 0 0 15px rgba(141,141,255,0.2);">
<div class="text-xs font-semibold mb-2 text-purple-300">The Hidden Truth</div>
<div class="text-xs space-y-1">
<div>✅ Quick fix: Toggle flag off → problem gone</div>
<div>⏱️ Gives time to debug properly</div>
<div>🎯 But monitoring tools can't see this connection</div>
</div>
</div>

</div>

  </div>
</div>

</div>

<div v-click class="mt-6 p-4 bg-amber-900/20 border border-amber-800 rounded-lg" style="box-shadow: 0 0 15px rgba(251, 146, 60, 0.2);">
<div class="text-center">
<div class="text-lg mb-2">
<span class="text-amber-300 font-bold">The Mitigation Problem:</span>
</div>
<div class="text-sm opacity-90">
Hours spent restarting pods, rolling back code, and debugging — when a <span class="font-bold text-green-400">30-second flag toggle</span> would have stopped the bleeding
</div>
</div>
</div>

<!--
This is the key insight - you can see something is wrong, but you can't see the easiest fix.
The flag toggle is RIGHT THERE but invisible to your observability tools.
-->

---
class: py-10
---

# Why This Matters

<span>The hidden cost of invisible feature flags</span>

<div mt-8 />

<div grid grid-cols-3 gap-6>

<div v-click class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden h-full style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center justify-center class="bg-gradient-purple" px-4 py-6>
    <div i-carbon:time text-6xl text-red-300 style="filter: drop-shadow(0 0 8px rgba(248,113,113,0.6));" />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>Slower Recovery</div>
    <div text-sm opacity-70>MTTR increases when the easiest fix is invisible</div>
  </div>
</div>

<div v-click class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden h-full style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center justify-center class="bg-gradient-purple" px-4 py-6>
    <div i-carbon:search text-6xl text-amber-300 style="filter: drop-shadow(0 0 8px rgba(252,211,77,0.6));" />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>Wrong Direction</div>
    <div text-sm opacity-70>Debugging code that isn't actually broken</div>
  </div>
</div>

<div v-click class="bg-gradient-card" border="1.5 solid purple-light" rounded-lg overflow-hidden h-full style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08);">
  <div flex items-center justify-center class="bg-gradient-purple" px-4 py-6>
    <div i-carbon:fire text-6xl text-orange-300 style="filter: drop-shadow(0 0 8px rgba(253,186,116,0.6));" />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>Escalation</div>
    <div text-sm opacity-70>Simple toggle becomes all-hands incident</div>
  </div>
</div>

</div>

<div v-click mt-6 flex justify-center>
  <div
    class="bg-gradient-purple" border="1.5 solid purple-bright"
    rounded-lg px-6 py-3 flex items-center gap-3
    style="box-shadow: 0 0 25px rgba(109, 118, 255, 0.35);"
  >
    <div i-carbon:idea text-purple-bright text-2xl />
    <span text-lg class="text-purple-bright">We need feature flags as <span class="font-bold">first-class concept</span> in observability</span>
  </div>
</div>

<!--
This is why solving flag observability matters - it's not just nice to have, it's critical for modern operations.
-->

---
layout: section
---

# Feature Flag Observability

<span class="opacity-80">A progressive approach for teams looking to gain visibility into flags</span>

<div class="mt-16 grid grid-cols-5 gap-4">

<div
  border="1.5 solid purple-200" rounded-lg overflow-hidden p-4 text-center
  class="animate-fade-in opacity-0"
  style="animation-delay: 0.1s; animation-fill-mode: forwards; background: linear-gradient(135deg, rgba(224, 224, 255, 0.15), rgba(203, 203, 255, 0.15)); box-shadow: 0 0 20px rgba(224, 224, 255, 0.2);"
>
  <div class="text-3xl mb-2">🙈</div>
  <div class="font-bold mb-1">Level 0</div>
  <div class="text-sm opacity-70">Flying Blind</div>
</div>

<div
  border="1.5 solid purple-300" rounded-lg overflow-hidden p-4 text-center
  class="animate-fade-in opacity-0"
  style="animation-delay: 0.25s; animation-fill-mode: forwards; background: linear-gradient(135deg, rgba(183, 185, 255, 0.2), rgba(161, 163, 255, 0.2)); box-shadow: 0 0 20px rgba(183, 185, 255, 0.25);"
>
  <div class="text-3xl mb-2">📢</div>
  <div class="font-bold mb-1">Level 1</div>
  <div class="text-sm opacity-70">Broadcast Blast</div>
</div>

<div
  border="1.5 solid purple-400" rounded-lg overflow-hidden p-4 text-center
  class="animate-fade-in opacity-0"
  style="animation-delay: 0.4s; animation-fill-mode: forwards; background: linear-gradient(135deg, rgba(161, 163, 255, 0.25), rgba(141, 141, 255, 0.25)); box-shadow: 0 0 20px rgba(161, 163, 255, 0.3);"
>
  <div class="text-3xl mb-2">✍️</div>
  <div class="font-bold mb-1">Level 2</div>
  <div class="text-sm opacity-70">Manual Events</div>
</div>

<div
  border="1.5 solid purple-500" rounded-lg overflow-hidden p-4 text-center
  class="animate-fade-in opacity-0"
  style="animation-delay: 0.55s; animation-fill-mode: forwards; background: linear-gradient(135deg, rgba(141, 141, 255, 0.32), rgba(121, 121, 255, 0.32)); box-shadow: 0 0 20px rgba(141, 141, 255, 0.35);"
>
  <div class="text-3xl mb-2">🤖</div>
  <div class="font-bold mb-1">Level 3</div>
  <div class="text-sm opacity-70">Auto Mapping</div>
</div>

<div
  border="1.5 solid purple-600" rounded-lg overflow-hidden p-4 text-center
  class="animate-fade-in opacity-0"
  style="animation-delay: 0.7s; animation-fill-mode: forwards; background: linear-gradient(135deg, rgba(121, 121, 255, 0.38), rgba(93, 93, 255, 0.4)); box-shadow: 0 0 20px rgba(121, 121, 255, 0.4);"
>
  <div class="text-3xl mb-2">🎯</div>
  <div class="font-bold mb-1">Level 4</div>
  <div class="text-sm opacity-70">Trace-Level</div>
</div>

</div>

---
class: py-10
---

# Level 0: Flying Blind 🙈

<div class="text-lg mb-6 opacity-80">You see something is wrong, but have no idea why</div>

<div class="grid grid-cols-2 gap-8">

<div>

<div v-click>

### The Problem

<div class="mt-4 space-y-3">
<div class="flex items-center gap-2">
  <div class="i-carbon:view-off text-red-400" />
  <span class="text-sm">No visibility into flag changes</span>
</div>
<div class="flex items-center gap-2">
  <div class="i-carbon:unknown text-amber-400" />
  <span class="text-sm">Pure guesswork during incidents</span>
</div>
<div class="flex items-center gap-2">
  <div class="i-carbon:time text-orange-400" />
  <span class="text-sm">Hours of manual hunting</span>
</div>
</div>

</div>

<div v-click="2" class="mt-6">

### What You See

<div class="p-4 mt-4 bg-red-900/20 border border-red-800 rounded-lg text-sm" style="box-shadow: 0 0 15px rgba(239, 68, 68, 0.2);">
<div class="font-semibold text-red-400 mb-2">🚨 Failure rate spiking!</div>
<div class="opacity-80">But what changed? No deployments, no code changes...</div>
<div class="mt-2 italic opacity-70">Time to check logs, metrics, Slack, coffee machine...</div>
</div>

</div>

</div>

<div v-click>

<ServiceMetricChart 
  service-name="Checkout Service"
  metric="failure-rate"
  :show-annotation="false"
  :spike-click="1"
  :has-issue="true"
  :height="240"
  :width="400"
/>

<div v-click="3" class="mt-3 p-3 bg-amber-900/20 border border-amber-800 rounded-lg text-sm" style="box-shadow: 0 0 15px rgba(251, 146, 60, 0.2);">
<div class="text-amber-300 font-semibold mb-1">⚠️ The Reality</div>
<div class="opacity-80">A feature flag was toggled 30 seconds ago, but you have no way to know that.</div>
</div>

</div>

</div>

---
class: py-8
---

# Level 1: Broadcast Blast 📢

<span>Same flag change annotated on all services... but only one is actually using the flag</span>

<div class="grid grid-cols-3 gap-4">

<div>
<ServiceMetricChart 
  service-name="Checkout service"
  metric="failure-rate"
  :show-annotation="true"
  :annotation-click="1"
  :spike-click="1"
  :has-issue="true"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

<div>
<ServiceMetricChart 
  service-name="Payment service"
  metric="failure-rate"
  :show-annotation="true"
  :annotation-click="1"
  :spike-click="999"
  :has-issue="false"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

<div>
<ServiceMetricChart 
  service-name="Recommendation service"
  metric="failure-rate"
  :show-annotation="true"
  :annotation-click="1"
  :spike-click="999"
  :has-issue="false"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

</div>

<div v-click="2" class="mt-6 p-4 bg-red-900/20 border border-red-800 rounded-lg" style="box-shadow: 0 0 15px rgba(239, 68, 68, 0.2);">
<div class="flex items-start gap-3">
  <div class="i-carbon:warning-alt text-red-400 text-2xl mt-1" />
  <div>
    <div class="font-semibold text-red-300 mb-2">The Red Herring Problem</div>
    <div class="text-sm opacity-90">
      When you manually configure events, you might send them to services that <span class="font-bold">don't use the flag</span>.
      This creates noise during incidents and can send investigations in the wrong direction.
    </div>
    <div class="text-sm mt-2 italic opacity-75">
      "We see the flag changed at the same time, but these services look fine... maybe it's not the flag?"
    </div>
  </div>
</div>
</div>

<!--
The visualization shows the key problem: flag annotations appear on all services,
but only checkout-service actually uses the flag and experiences issues.
This manual approach doesn't scale and creates misleading signals.
-->

---
class: py-8
---

# Level 2: Manual Change Events ✍️

<span>Send events to specific services... but requires manual mapping and can become outdated</span>

<div v-click class="grid grid-cols-3 gap-4">

<div>
<ServiceMetricChart 
  service-name="checkout-service"
  metric="failure-rate"
  :show-annotation="true"
  :annotation-click="1"
  :spike-click="1"
  :has-issue="true"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

<div>
<ServiceMetricChart 
  service-name="payment-service"
  metric="failure-rate"
  :show-annotation="true"
  :annotation-click="1"
  :spike-click="999"
  :has-issue="false"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

<div>
<ServiceMetricChart 
  service-name="recommendation-service"
  metric="failure-rate"
  :show-annotation="false"
  :spike-click="999"
  :has-issue="false"
  :height="180"
  :width="280"
  :max-data-points="30"
/>
</div>

</div>

<div v-click="2" class="mt-6 p-4 bg-amber-900/20 border border-amber-800 rounded-lg" style="box-shadow: 0 0 15px rgba(251, 146, 60, 0.2);">
<div class="flex items-start gap-3">
  <div class="i-carbon:warning-alt text-amber-400 text-2xl mt-1" />
  <div>
    <div class="font-semibold text-amber-300 mb-2">The Manual Mapping Problem</div>
    <div class="text-sm opacity-90">
      You can <span class="font-bold">manually configure</span> which services receive flag change events, but this requires maintaining a mapping.
      As your system evolves, these mappings become <span class="font-bold">outdated</span> — events might go to services that no longer use the flag,
      or miss new services that started using it.
    </div>
    <div class="text-sm mt-2 italic opacity-75">
      "We configured this 6 months ago... did we update it when we refactored?"
    </div>
  </div>
</div>
</div>

---
class: py-8
---

# Level 3: Automatic Event Mapping 🤖

<span>Telemetry-driven routing eliminates manual configuration</span>

<div class="grid grid-cols-2 gap-12 px-16">

<div v-click>

### Intelligent Routing

<div class="mt-4">

```mermaid {scale: 0.65}
graph TD
  A[Flag Platform] -->|Telemetry| B[Detect Usage]
  B -->|Map| C[checkout-service]
  B -->|Map| D[payment-service]
  C -->|Events| E[Observability]
  D -->|Events| E
  style A fill:#8D8DFF,color:#fff,stroke:#5D5DFF,stroke-width:2px
  style B fill:#55595F,color:#fff,stroke:#707D86,stroke-width:2px
  style C fill:#ABABFF,color:#222,stroke:#8D8DFF,stroke-width:2px
  style D fill:#ABABFF,color:#222,stroke:#8D8DFF,stroke-width:2px
  style E fill:#6B7DFF,color:#fff,stroke:#5D5DFF,stroke-width:2px
```
</div>

</div>

<div v-click="2">

### Benefits

<div class="mt-4 space-y-4 text-base">

<div class="flex items-start gap-3">
  <div class="i-carbon:checkmark text-green-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Automatic discovery</div>
    <div class="text-sm opacity-70">Detects where flags are evaluated</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:checkmark text-green-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Smart routing</div>
    <div class="text-sm opacity-70">Events only to affected services</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:checkmark text-green-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Zero maintenance</div>
    <div class="text-sm opacity-70">Stays current as code evolves</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:warning-alt text-amber-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Correlation ≠ Causation</div>
    <div class="text-sm opacity-70">Good indicator, but can't measure exact impact</div>
  </div>
</div>

</div>

</div>

</div>

<div v-click="3" class="mt-6 text-center text-lg opacity-90">
Solves the scaling problem, but requires a more complicated setup. How can it be improved further?
</div>

---
layout: default
class: py-8
clicks: 4
---

# Level 4: Trace-Level Observability 🎯

<span class="opacity-80">Feature flags as a first-class observability concept</span>

<div class="grid grid-cols-2 gap-12 px-4">

<div v-click>

### Fine-Grained Insights

<div class="mt-4 space-y-4 text-base">

<div class="flex items-start gap-3">
  <div class="i-carbon:view text-blue-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">See flag evaluations</div>
    <div class="text-sm opacity-70">Every trace shows which flags were evaluated</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:transform-code text-orange-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Realtime mapping of flag presence per service</div>
    <div class="text-sm opacity-70">Filter by traces with specific flag presence</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:filter text-purple-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Filter by flag presence</div>
    <div class="text-sm opacity-70">Isolate requests that used a specific flag</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:chart-line text-green-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Compare variants</div>
    <div class="text-sm opacity-70">See performance differences in real-time</div>
  </div>
</div>

<div class="flex items-start gap-3">
  <div class="i-carbon:flash text-amber-400 text-xl mt-1 flex-shrink-0" />
  <div>
    <div class="font-semibold">Instant root cause</div>
    <div class="text-sm opacity-70">Immediately identify which variant caused issues</div>
  </div>
</div>

</div>

</div>

<div v-click="2">


<VariantComparisonChart />

<div class="mt-2 px-3 py-2 bg-purple-900/20 border border-purple-800/50 rounded text-xs" style="box-shadow: 0 0 10px rgba(141,141,255,0.15);">
  <div class="text-purple-300 text-center">
    <span v-if="$clicks >= 2 && $clicks < 3">📊 All traffic - flag impact barely noticeable (165-225ms)</span>
    <span v-else-if="$clicks >= 3 && $clicks < 4">🔍 Flag traffic only - clear spike visible (1600-2000ms)</span>
    <span v-else-if="$clicks >= 4">🎯 Split by variant - "on" variant shows 3200-3800ms! Root cause identified.</span>
    <span v-else>&nbsp;</span>
  </div>
</div>

</div>

</div>

---
layout: center
---

<div class="text-center text-3xl mt-12">

Trace-level observability makes

feature flags a <span class="text-blue-400">first-class citizens</span>

in your monitoring stack

</div>


<!--
This is the transformation we're enabling with OpenFeature and OpenTelemetry.
-->

---
layout: section
---

# <span class="text-4xl">Feature Flag Observability Standards</span>

<span class="opacity-80">Bridging feature flags and observability through open standards</span>

---
layout: default
---

# OpenFeature 🤝 OpenTelemetry

<div class="grid grid-cols-2 gap-8 mt-8">

<div v-click class="border border-blue-500 rounded-lg p-6" style="background: linear-gradient(135deg, rgba(30, 64, 175, 0.6), rgba(29, 78, 216, 0.7)); box-shadow: 0 0 25px rgba(96, 165, 250, 0.35);">

<div class="flex items-center mb-6">
  <div class="i-carbon:flag text-blue-300 text-3xl mr-3" />
  <h3 class="text-2xl font-bold text-blue-100">OpenFeature</h3>
</div>

<div class="space-y-3 text-base">
  <div class="flex items-start gap-3">
    <div class="i-carbon:settings-adjust text-blue-200 text-xl mt-0.5 flex-shrink-0" />
    <span>Open standard for <span class="font-bold">feature flagging</span></span>
  </div>
  <div class="flex items-start gap-3">
    <div class="i-carbon:plug text-blue-200 text-xl mt-0.5 flex-shrink-0" />
    <span>Vendor-neutral SDK</span>
  </div>
  <div class="flex items-start gap-3">
    <div class="i-carbon:api text-blue-200 text-xl mt-0.5 flex-shrink-0" />
    <span><span class="font-bold">Hook system</span> for extensibility</span>
  </div>
</div>

</div>

<div v-click class="border border-purple-500 rounded-lg p-6" style="background: linear-gradient(135deg, rgba(109, 40, 217, 0.6), rgba(124, 58, 237, 0.7)); box-shadow: 0 0 25px rgba(168, 85, 247, 0.35);">

<div class="flex items-center mb-6">
  <div class="i-carbon:chart-line text-purple-300 text-3xl mr-3" />
  <h3 class="text-2xl font-bold text-purple-100">OpenTelemetry</h3>
</div>

<div class="space-y-3 text-base">
  <div class="flex items-start gap-3">
    <div class="i-carbon:dashboard text-purple-200 text-xl mt-0.5 flex-shrink-0" />
    <span>Open standard for <span class="font-bold">observability</span></span>
  </div>
  <div class="flex items-start gap-3">
    <div class="i-carbon:data-vis-1 text-purple-200 text-xl mt-0.5 flex-shrink-0" />
    <span>Metrics, traces, and logs</span>
  </div>
  <div class="flex items-start gap-3">
    <div class="i-carbon:tag text-purple-200 text-xl mt-0.5 flex-shrink-0" />
    <span><span class="font-bold">Semantic conventions</span> for consistency</span>
  </div>
</div>

</div>

</div>


<div v-click class="mt-12">

```mermaid {scale: 0.75}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Events| D[Observability Platform]
    C -->|Metrics| D
    C -->|Traces| D

    style A fill:#6B7DFF,stroke:#93C5FD,stroke-width:3px,color:#fff
    style B fill:#8D8DFF,stroke:#ABABFF,stroke-width:2px,color:#fff
    style C fill:#ABABFF,stroke:#B7B9FF,stroke-width:3px,color:#222
    style D fill:#8D8DFF,stroke:#B7B9FF,stroke-width:2px,color:#fff
```

</div>


---
layout: default
---

# Feature Flag Semantic Convention

<span class="opacity-80">Standardized attributes that make flag evaluations observable</span>

<div class="grid grid-cols-2 gap-6 mt-6">

<div v-click>

### Obvious Attributes

<div class="text-sm space-y-3 mt-3">

<div class="p-2 bg-white/5 rounded">
<code class="text-blue-300">feature_flag.key</code>
<div class="text-xs opacity-70 mt-1">The flag identifier (e.g., "new-checkout")</div>
</div>

<div class="p-2 bg-white/5 rounded">
<code class="text-blue-300">feature_flag.result.variant</code>
<div class="text-xs opacity-70 mt-1">Which variant was served (e.g., "on", "treatment")</div>
</div>

<div class="p-2 bg-white/5 rounded">
<code class="text-blue-300">feature_flag.result.value</code>
<div class="text-xs opacity-70 mt-1">The value returned by the flag (e.g., true, false, "blue")</div>
</div>

<div class="p-2 bg-white/5 rounded">
<code class="text-blue-300">feature_flag.provider.name</code>
<div class="text-xs opacity-70 mt-1">Flag provider (e.g., "flagd", "launchdarkly", "devcycle")</div>
</div>

<div class="p-2 bg-white/5 rounded">
<code class="text-blue-300">feature_flag.result.reason</code>
<div class="text-xs opacity-70 mt-1">Why this variant? (e.g., "targeting_match", "default")</div>
</div>

</div>

</div>

<div v-click="2">

### Nuanced Attributes

<div class="text-sm space-y-3 mt-3">

<div class="p-3 bg-purple-900/20 border border-purple-500/30 rounded">
<code class="text-purple-300 font-semibold">feature_flag.set.id</code>
<div class="text-xs opacity-90 mt-2">
<span class="font-semibold">Human-readable logical identifier</span> for where this flag is managed
</div>
<div class="text-xs opacity-70 mt-1 font-mono">
"acme-org/web-app/production"
</div>
</div>

<div class="p-3 bg-amber-900/20 border border-amber-500/30 rounded">
<code class="text-amber-300 font-semibold">feature_flag.context.id</code>
<div class="text-xs opacity-90 mt-2">
<span class="font-semibold">Provider's context identifier</span> (fallback: targeting key)
</div>
<div class="text-xs opacity-70 mt-1">
Used by providers like DevCycle to lookup user evaluations and simulation
</div>
</div>

<div class="p-3 bg-green-900/20 border border-green-500/30 rounded">
<code class="text-green-300 font-semibold">feature_flag.version</code>
<div class="text-xs opacity-90 mt-2">
<span class="font-semibold">Ruleset version</span> at evaluation time
</div>
<div class="text-xs opacity-70 mt-1">
Track which configuration was active (number, hash, etc.)
</div>
</div>

</div>

</div>

</div>



---
layout: default
---

# How It Works: OpenFeature Hooks

```typescript {all|1-2,5-6|1,3,8-9|10-24|all}{maxHeight: '420px'}
import { OpenFeature } from '@openfeature/server-sdk';
import { MyProvider } from 'my-flag-provider';
import { EventHook } from '@openfeature/open-telemetry-hooks';

// Initialize OpenFeature with your flag provider
await OpenFeature.setProviderAndWait(new MyProvider());

// Register the OpenTelemetry hook
OpenFeature.addHooks(new EventHook());
const client = OpenFeature.getClient();

// Prepare evaluation context
const context = {
  targetingKey: 'user_123',
  tier: 'premium',
  environment: 'production'
};

// Evaluate flag - automatically traced!
const variant = await client.getStringValue(
  'new-checkout-flow',
  'control',
  context
);
```

---
layout: default
class: py-8
---

# Seeing It in Action

<span class="opacity-80">Feature flag evaluation captured in distributed tracing</span>

<div class="mt-8 p-4 rounded-xl" style="background: linear-gradient(135deg, rgba(141, 141, 255, 0.08), rgba(109, 118, 255, 0.08)); border: 1.5px solid rgba(141, 141, 255, 0.25); box-shadow: 0 0 30px rgba(109, 118, 255, 0.2);">
  <img src="/traces.png" class="w-full rounded-lg" style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);" />
</div>


---
layout: default
class: py-8
---

# Why Standards Matter

<div class="grid grid-cols-3 gap-8 mt-24">

<div v-click class="text-center">
<div class="text-4xl mb-4">🔄</div>
<div class="text-xl font-bold mb-2">Interoperability</div>
<div class="text-sm opacity-70">Works across vendors and tools</div>
</div>

<div v-click class="text-center">
<div class="text-4xl mb-4">📈</div>
<div class="text-xl font-bold mb-2">Consistency</div>
<div class="text-sm opacity-70">Same attributes everywhere</div>
</div>

<div v-click class="text-center">
<div class="text-4xl mb-4">🚀</div>
<div class="text-xl font-bold mb-2">Adoption</div>
<div class="text-sm opacity-70">Easy to implement and adopt</div>
</div>
</div>

<div v-click class="mt-12 text-center">

```mermaid {scale: 0.8}
graph LR
    A[Any Flag Provider] --> B[OpenFeature SDK]
    B --> C[OpenTelemetry Hook]
    C --> D[Any Observability Backend]

    style A fill:#707D86,color:#fff
    style B fill:#8D8DFF,color:#fff
    style C fill:#ABABFF,color:#222
    style D fill:#6B7DFF,color:#fff
```

</div>


---
layout: section
---

# <span class="text-4xl">Progressive Delivery with Observability</span>

Transforming theory into practice

---
layout: default
---

# What Is Progressive Delivery?

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### Concepts

<div class="mt-4 text-base">

<v-clicks>

- Gradually expose customers to new features
- Monitor impact at each step
- Roll back instantly if problems arise
- Minimize risk, maximize confidence

</v-clicks>

</div>

</div>

<div v-click>

### Rollout Stages

<div class="mt-4" />

```mermaid {scale: 0.65}
graph TD
    A[0% - Dark Launch] --> B[1% - Canary]
    B --> C[10% - Early Adopters]
    C --> D[50% - Broader Rollout]
    D --> E[100% - Full Release]

    B -.->|Issues?| F[Instant Rollback]
    C -.->|Issues?| F
    D -.->|Issues?| F

    style A fill:#55595F,color:#fff
    style B fill:#FB923C,color:#222
    style C fill:#8D8DFF,color:#fff
    style D fill:#6B7DFF,color:#fff
    style E fill:#4ADE80,color:#222
    style F fill:#EF4444,color:#fff
```

</div>

</div>

<!--
Progressive delivery is the new standard for feature releases, but it requires observability.
-->

---
class: px-8 py-6
---

# Rollout targeting setup

<div class="flex items-center justify-center mt-4">
  <img src="/rollout-targeting.png" class="rounded-lg" style="max-height: 55vh; max-width: 90%; object-fit: contain; box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);" />
</div>

---
layout: default
class: px-8 py-6
clicks: 3
---

# Observability Enables Progressive Delivery

<span class="opacity-80">Variant-level visibility reveals the root cause</span>

<div class="mt-2">
  <ProgressiveDeliveryDashboard />
</div>

---
layout: center
---

# Key Benefits

<div class="grid grid-cols-2 gap-8 mt-12 px-12">

<div v-click>
  <div
    border="1.5 solid amber-600"
    rounded-lg
    overflow-hidden
    bg="amber-900/20"
    backdrop-blur-sm
    style="box-shadow: 0 8px 32px 0 rgba(217, 119, 6, 0.25);"
  >
    <div
      flex items-center
      px-5 py-4
      style="background: linear-gradient(135deg, rgba(217, 119, 6, 0.25), rgba(146, 64, 14, 0.25));"
    >
      <div class="i-carbon:close-filled text-amber-300 text-xl mr-3" />
      <h3 class="text-amber-200 font-semibold text-lg">Without Observability</h3>
    </div>
    <div px-5 py-5>
      <div class="space-y-3 text-base">
        <div flex items-start gap-3>
          <div class="i-carbon:view-off text-amber-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Blind rollouts</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:time text-amber-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Slow to detect issues</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:touch-1 text-amber-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Manual rollback decisions</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:unknown text-amber-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Can't compare variants</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:warning-alt text-amber-400 text-lg mt-0.5 flex-shrink-0" />
          <span>High-risk deployments</span>
        </div>
      </div>
    </div>
  </div>
</div>

<div v-click>
  <div
    border="1.5 solid purple-light"
    rounded-lg
    overflow-hidden
    class="bg-gradient-card"
    backdrop-blur-sm
    style="box-shadow: 0 8px 32px 0 rgba(109, 118, 255, 0.35);"
  >
    <div
      flex items-center
      px-5 py-4
      class="bg-gradient-purple"
    >
      <div class="i-carbon:checkmark-filled text-purple-bright text-xl mr-3" />
      <h3 class="text-purple-bright font-semibold text-lg">With Observability</h3>
    </div>
    <div px-5 py-5>
      <div class="space-y-3 text-base">
        <div flex items-start gap-3>
          <div class="i-carbon:data-vis-1 text-blue-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Data-driven rollouts</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:flash text-blue-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Instant issue detection</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:workflow-automation text-blue-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Automated rollback triggers</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:chart-line text-blue-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Real-time variant comparison</span>
        </div>
        <div flex items-start gap-3>
          <div class="i-carbon:security text-blue-400 text-lg mt-0.5 flex-shrink-0" />
          <span>Low-risk deployments</span>
        </div>
      </div>
    </div>
  </div>
</div>

</div>

<div v-click class="mt-12 flex justify-center">
  <div
    class="bg-gradient-purple"
    border="1.5 solid purple-bright"
    rounded-lg
    px-8 py-4
    style="box-shadow: 0 0 25px rgba(109, 118, 255, 0.35);"
  >
    <div class="text-2xl text-center">
      Observability is the <span class="text-purple-bright font-bold">key</span> to progressive delivery
    </div>
  </div>
</div>

---
layout: default
class: py-8
---

# Challenges and Opportunities

<span class="opacity-80">The journey isn't complete</span>

<div class="grid grid-cols-2 gap-8 mt-8">

<div v-click class="flex">
  <div
    border="1.5 solid amber-600"
    rounded-lg
    overflow-hidden
    bg="amber-900/20"
    backdrop-blur-sm
    class="flex-1"
    style="box-shadow: 0 8px 32px 0 rgba(217, 119, 6, 0.25);"
  >
    <div
      flex items-center
      px-5 py-4
      style="background: linear-gradient(135deg, rgba(217, 119, 6, 0.25), rgba(146, 64, 14, 0.25));"
    >
      <div class="i-carbon:warning-alt text-amber-300 text-xl mr-3" />
      <h3 class="text-amber-200 font-semibold text-lg">Current Limitations</h3>
    </div>
    <div px-5 py-5>
      <div class="space-y-4 text-base">
        <div>
          <div flex items-start gap-3 class="mb-2">
            <div class="i-carbon:scale text-amber-400 text-lg mt-0.5 flex-shrink-0" />
            <span class="font-semibold">Scaling Challenges</span>
          </div>
          <div class="text-sm opacity-80 ml-8">
            Users must handle deduplication, sampling, and aggregation strategies themselves
          </div>
        </div>
        <div>
          <div flex items-start gap-3 class="mb-2">
            <div class="i-carbon:plug text-amber-400 text-lg mt-0.5 flex-shrink-0" />
            <span class="font-semibold">Provider Dependency</span>
          </div>
          <div class="text-sm opacity-80 ml-8">
            Requires OpenFeature providers to include semantic convention attributes
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<div v-click="2" class="flex">
  <div
    border="1.5 solid purple-light"
    rounded-lg
    overflow-hidden
    class="bg-gradient-card flex-1"
    backdrop-blur-sm
    style="box-shadow: 0 8px 32px 0 rgba(109, 118, 255, 0.35);"
  >
    <div
      flex items-center
      px-5 py-4
      class="bg-gradient-purple"
    >
      <div class="i-carbon:rocket text-purple-bright text-xl mr-3" />
      <h3 class="text-purple-bright font-semibold text-lg">Opportunities Ahead</h3>
    </div>
    <div px-5 py-5>
      <div class="space-y-4 text-base">
        <div>
          <div flex items-start gap-3 class="mb-2">
            <div class="i-carbon:chart-line text-blue-400 text-lg mt-0.5 flex-shrink-0" />
            <span class="font-semibold">Metrics Convention</span>
          </div>
          <div class="text-sm opacity-80 ml-8">
            Standardize flag evaluation metrics for consistent monitoring
          </div>
        </div>
        <div>
          <div flex items-start gap-3 class="mb-2">
            <div class="i-carbon:event text-blue-400 text-lg mt-0.5 flex-shrink-0" />
            <span class="font-semibold">Change Events</span>
          </div>
          <div class="text-sm opacity-80 ml-8">
            Define standard format for flag configuration changes
          </div>
        </div>
        <div>
          <div flex items-start gap-3 class="mb-2">
            <div class="i-carbon:data-structured text-blue-400 text-lg mt-0.5 flex-shrink-0" />
            <span class="font-semibold">Flags as Entities</span>
          </div>
          <div class="text-sm opacity-80 ml-8">
            Model feature flags as first-class entities in observability platforms
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

</div>

<!--
We've made great progress, but there's still work to do.
The semantic convention for events is just the beginning.
We need your help to define metrics, change events, and entity models.
-->

---
layout: center
class: text-center
---

# Let's Build the Future Together

<div class="mt-12 text-xl leading-relaxed space-y-3">

<v-click>

Feature flags are powerful tools for modern software delivery

</v-click>

<v-click>

But they need to be <span v-mark.underline.purple="2" class="text-purple-400">visible</span> in our observability stack

</v-click>

<v-click>

OpenFeature + OpenTelemetry make this possible with <span v-mark.underline.blue="3" class="text-blue-400">open standards</span>

</v-click>

<v-click>

Join us in transforming how we understand and deploy software

</v-click>

</div>

<div v-click class="mt-16 text-3xl">
From <span class="font-serif">"What Broke"</span> to <span class="font-serif">"What Changed"</span>
</div>

<!--
Together, we can make feature flags first-class citizens in observability.
-->

---
layout: center
class: text-center
---

<div class="flex flex-col items-center justify-center h-full">

<h1 class="text-6xl font-bold mb-8">
  <span class="text-purple-400" style="text-shadow: 0 0 30px rgba(224, 224, 255, 0.5);">Thank You!</span>
</h1>

<div class="text-3xl text-purple-400 mb-12" style="text-shadow: 0 0 20px rgba(183, 185, 255, 0.4);">
  Questions?
</div>

<div class="text-lg text-purple-light opacity-90 mb-12">
  Find us after the talk or online:
</div>

<div class="grid grid-cols-2 gap-8 text-left max-w-3xl w-full px-8">

<div
  class="bg-gradient-card"
  border="1.5 solid purple-light"
  rounded-xl
  overflow-hidden
  style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08), 0 0 20px rgba(109, 118, 255, 0.25);"
>
  <div
    flex items-center
    px-5 py-4
    class="bg-gradient-purple"
  >
    <div
      class="w-12 h-12 rounded-lg flex items-center justify-center mr-3"
      style="background: linear-gradient(135deg, rgba(141, 141, 255, 0.4), rgba(183, 185, 255, 0.25)); box-shadow: 0 2px 12px rgba(141,141,255,0.2);"
    >
      <div class="i-carbon:link text-purple-bright text-2xl" style="filter: drop-shadow(0 0 8px rgba(224, 224, 255, 0.6));" />
    </div>
    <h3 class="text-xl font-bold text-purple-bright">Resources</h3>
  </div>
  <div px-5 py-5>
    <div flex flex-col gap-4 text-base class="text-purple-light">
      <div flex items-center>
        <div class="i-carbon:flag mr-3 text-lg" />
        <span>openfeature.dev</span>
      </div>
      <div flex items-center>
        <div class="i-carbon:logo-github mr-3 text-lg" />
        <span class="text-sm">github.com/open-openfeature/</span>
      </div>
      <div flex items-center>
        <div class="i-carbon:chart-line mr-3 text-lg" />
        <span>opentelemetry.io</span>
      </div>
    </div>
  </div>
</div>

<div
  class="bg-gradient-card"
  border="1.5 solid purple-light"
  rounded-xl
  overflow-hidden
  style="box-shadow: 0 8px 32px 0 rgba(60,66,110,0.38), 0 0 0 2px rgba(141,141,255,0.08), 0 0 20px rgba(109, 118, 255, 0.25);"
>
  <div
    flex items-center
    px-5 py-4
    class="bg-gradient-purple"
  >
    <div
      class="w-12 h-12 rounded-lg flex items-center justify-center mr-3"
      style="background: linear-gradient(135deg, rgba(141, 141, 255, 0.4), rgba(183, 185, 255, 0.25)); box-shadow: 0 2px 12px rgba(141,141,255,0.2);"
    >
      <div class="i-carbon:user-multiple text-purple-bright text-2xl" style="filter: drop-shadow(0 0 8px rgba(224, 224, 255, 0.6));" />
    </div>
    <h3 class="text-xl font-bold text-purple-bright">Connect</h3>
  </div>
  <div px-5 py-5>
    <div flex flex-col gap-4 text-base class="text-purple-light">
      <div flex items-center>
        <div class="i-carbon:logo-slack mr-3 text-lg" />
        <span>CNCF Slack: #openfeature</span>
      </div>
      <div flex items-center>
        <div class="i-carbon:logo-slack mr-3 text-lg" />
        <span>CNCF Slack: #opentelemetry</span>
      </div>
      <div flex items-center>
        <div class="i-carbon:logo-twitter mr-3 text-lg" />
        <span>@openfeature / @opentelemetry</span>
      </div>
    </div>
  </div>
</div>

</div>

</div>
