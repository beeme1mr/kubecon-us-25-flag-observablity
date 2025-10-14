---
layout: center
highlighter: shiki
css: unocss
colorSchema: dark
transition: fade-out
title: From "What Broke" to "What Changed"
exportFilename: KubeCon NA 2025 - Feature Flags as First-Class Signals in Observability
lineNumbers: false
drawings:
  persist: false
fonts:
  sans: 'Inter'
  serif: 'Architects Daughter'
  mono: 'Fira Code'
mdc: true
clicks: 0
preload: false
glowSeed: 229
routerMode: hash
info: |
  ## Feature Flags as First-Class Signals in Observability
  KubeCon + CloudNativeCon 2025

  Learn how to integrate feature flag observability into your workflow using OpenFeature and OpenTelemetry standards.
---

<div translate-x--14>
  <h1>
    From <span v-mark="{ type: 'crossed-off', color: 'red' }" class="font-serif">"What Broke"</span> to <span v-mark="{ type: 'circle' }" class="font-serif">"What Changed"</span>
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
        <span class="opacity-70">Software engineer</span>
      </div>
      <div text-sm flex items-center justify-center gap-2 mt-4>
        <div i-ri:github-fill /><span underline decoration-dashed font-mono decoration-zinc-300>suthar26</span>
      </div>
    </div>
  </div>
</div>

---
clicks: 3
---

<div
  class="mermaid-container transition-all duration-500"
  :class="{
    'scale-300 translate-x-200': $clicks === 1,
    'scale-200 translate-x-40 translate-y-10': $clicks === 2,
    'scale-50': $clicks === 0
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
class: text-center
---

# Imagine This...

<span class="opacity-80">The firefighting scenario every team knows</span>

<v-clicks>

<div class="text-2xl mb-8 mt-8">
🚨 Your monitoring dashboards are <span class="text-red-500 font-bold">flashing red</span>
</div>

<div class="text-xl mb-8">
📈 Error rates have <span class="text-red-500 font-bold">skyrocketed</span>
</div>

<div class="text-xl mb-8">
⏰ Your team has spent <span class="text-orange-500 font-bold">hours</span> digging through code
</div>

<div class="text-2xl mt-12">
Hours later, you discover... <span class="text-blue-400 font-bold">it wasn't a bug</span>
</div>

<div class="text-3xl mt-8 font-serif text-blue-400 font-bold">
It was a feature flag change 🚩
</div>

</v-clicks>

<!--
[IMAGE PLACEHOLDER: Chaotic monitoring dashboard showing red alerts, spike in errors, and elevated latency graphs]
This scenario plays out in engineering teams every day. Let's talk about why.
-->

---

# The Problem

<div class="text-center text-2xl leading-relaxed mt-16">
<v-click>

Feature flags are <span class="font-serif text-red-400">hidden</span> from observability tools

</v-click>
<v-click>

making it <span class="font-serif text-orange-400">difficult</span> to pinpoint changes

</v-click>
<v-click>

as the <span class="font-serif text-blue-400">root cause</span> of incidents

</v-click>

</div>
<div class="mt-12 flex justify-center opacity-90">
  <img v-click class="w-lg" src="/failures.png" />
</div>

<!--
This is the core problem we're solving today. Feature flags operate in the shadows.
-->

---
class: py-10
---

# What Are Feature Flags?

<span>Runtime configuration for modern software delivery</span>

<div mt-6 />

<div grid grid-cols-2 gap-6>

<div>
<v-clicks>

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-4 mb-3>
  <div flex items-center mb-2>
    <div i-carbon:settings-adjust text-blue-300 text-lg mr-2 />
    <span font-semibold>Toggle Control</span>
  </div>
  <div text-sm opacity-70>Turn features on/off without deploying code</div>
</div>

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-4 mb-3>
  <div flex items-center mb-2>
    <div i-carbon:rocket text-green-300 text-lg mr-2 />
    <span font-semibold>Progressive Rollouts</span>
  </div>
  <div text-sm opacity-70>Gradual releases and canary deployments</div>
</div>

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-4 mb-3>
  <div flex items-center mb-2>
    <div i-carbon:chart-multitype text-purple-300 text-lg mr-2 />
    <span font-semibold>A/B Testing</span>
  </div>
  <div text-sm opacity-70>Experiments and variant testing</div>
</div>

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-4 mb-3>
  <div flex items-center mb-2>
    <div i-carbon:user-access text-amber-300 text-lg mr-2 />
    <span font-semibold>Access Control</span>
  </div>
  <div text-sm opacity-70>Permissions and targeting rules</div>
</div>

</v-clicks>
</div>

<div v-click="5">

<div class="bg-white/5 backdrop-blur-sm border border-white/10 rounded-lg p-2">

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

<div v-click border="2 solid blue-500/30" rounded-lg overflow-hidden bg="blue-900/20" backdrop-blur-sm>
  <div flex items-center bg="blue-800/30" backdrop-blur px-4 py-3>
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

<div v-click border="2 solid red-500/30" rounded-lg overflow-hidden bg="red-900/20" backdrop-blur-sm>
  <div flex items-center bg="red-800/30" backdrop-blur px-4 py-3>
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

<div v-click class="mt-6 text-center opacity-70">
[IMAGE PLACEHOLDER: Screenshot of trace or dashboard with NO reference to feature flags]
</div>

<!--
Observability tools show us the symptoms, but not the cause when flags are involved.
-->

---
layout: default
---

# Real-World Example: OpenTelemetry Demo

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

<v-clicks>

### The Scenario

A feature flag enables a **product recommendation** feature

The feature has a **hidden performance bug**

Users experience:
- 🐌 Slow page loads
- ⏰ Request timeouts
- 💥 Increased errors

</v-clicks>

</div>

<div v-click>

### What Teams See

```yaml
# Dashboard
metrics:
  - response_time: ⬆️ 3000ms (150ms)
  - error_rate: ⬆️ 15% (0.1%)
  - timeouts: ⬆️ 40%

traces:
  - status: ERROR
  - duration: 5000ms
  - span: recommendation_service
```

<div class="mt-2 text-red-400 text-sm">
❓ No indication of feature flag involvement
</div>

</div>

</div>

<div v-click class="mt-4 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: OTel Demo dashboard showing error spike]
</div>

<!--
In the OTel demo, we can simulate this exact scenario. The problem is invisible.
-->

---
class: py-10
glowSeed: 175
---

# Why This Matters

<span>The real cost of hidden feature flags</span>

<div mt-8 />

<div grid grid-cols-3 gap-6>

<div v-click border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm h-full>
  <div flex items-center justify-center bg="white/10" backdrop-blur px-4 py-6>
    <div i-carbon:time text-6xl text-red-300 />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>Longer MTTR</div>
    <div text-sm opacity-70>Mean Time to Recovery increases when root cause is hidden</div>
  </div>
</div>

<div v-click border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm h-full>
  <div flex items-center justify-center bg="white/10" backdrop-blur px-4 py-6>
    <div i-carbon:currency-dollar text-6xl text-amber-300 />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>Wasted Time</div>
    <div text-sm opacity-70>Engineers spend hours debugging code that isn't broken</div>
  </div>
</div>

<div v-click border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm h-full>
  <div flex items-center justify-center bg="white/10" backdrop-blur px-4 py-6>
    <div i-carbon:warning-alt text-6xl text-orange-300 />
  </div>
  <div px-4 py-4 text-center>
    <div text-xl font-bold mb-2>All-Hands Incidents</div>
    <div text-sm opacity-70>What should be a quick rollback becomes a major incident</div>
  </div>
</div>

</div>

<div v-click mt-6 flex justify-center>
  <div
    border="2 solid white/5" bg="white/5" backdrop-blur-sm
    rounded-lg px-6 py-3 flex items-center gap-3
  >
    <div i-carbon:idea text-yellow-300 text-2xl />
    <span text-lg class="font-serif">Feature flag observability transforms incident response</span>
  </div>
</div>

<!--
The impact is real - longer incidents, wasted engineering time, and unnecessary stress.
-->

---
layout: section
glowSeed: 200
---

# The Evolution of Feature Flag Observability

<span class="opacity-80">How teams have tried to solve this problem</span>

---
class: py-10
glowSeed: 140
clicks: 4
---

# Stage 1: Flying Blind 🙈

<span>The starting point for most teams</span>

<div mt-6 />

<div grid grid-cols-2 gap-6>

<div v-click border="2 solid red-500/30" rounded-lg overflow-hidden bg="red-900/20" backdrop-blur-sm>
  <div flex items-center bg="red-800/30" backdrop-blur px-4 py-3>
    <div i-carbon:view-off text-red-300 text-xl mr-3 />
    <h3 class="text-red-400 font-semibold">The Reality</h3>
  </div>
  <div px-4 py-4>
    <div flex flex-col gap-3>
      <div flex items-center>
        <div i-carbon:misuse text-red-400 mr-2 />
        <span text-sm>No visibility into flag state</span>
      </div>
      <div flex items-center>
        <div i-carbon:edit text-orange-400 mr-2 />
        <span text-sm>Manual correlation attempts</span>
      </div>
      <div flex items-center>
        <div i-carbon:unknown text-amber-400 mr-2 />
        <span text-sm>Guesswork during incidents</span>
      </div>
      <div flex items-center>
        <div i-carbon:time text-red-400 mr-2 />
        <span text-sm>Slow troubleshooting</span>
      </div>
    </div>
  </div>
</div>

<div v-click="2">
  <div text-sm font-semibold mb-3 opacity-70>The Manual Hunt Process</div>
  <div class="bg-white/5 backdrop-blur-sm border border-white/10 rounded-lg p-3">

```mermaid {scale: 0.35}
graph LR
    A[🚨 Incident] --> B[Logs]
    B --> C[Metrics]
    C --> D[Deploys]
    D --> E{Fixed?}
    E -->|No| F[Slack]
    F --> G[Maybe?]
    
    style A fill:#f66
    style G fill:#6f6
```

  </div>
  <div text-xs mt-3 opacity-70 text-center italic>
    Hours of manual correlation across systems
  </div>
</div>

</div>

<!--
This is where most teams start. It's painful and inefficient.
-->

---
layout: default
---

# Stage 2: Manual Change Events

<div class="mt-4">

### The Improvement

<div v-click class="mb-4">
Send change events from feature flag platform → observability tool
</div>

<div class="grid grid-cols-2 gap-6 mt-4">

<div v-click>

```typescript {monaco-line-height:16}
// Manual event tracking
flagManager.on('flag.changed', (event) => {
  observability.recordEvent({
    type: 'feature_flag_change',
    flag: event.flagKey,
    oldValue: event.previousValue,
    newValue: event.currentValue,
    timestamp: Date.now()
  });
});
```

</div>

<div v-click>

### Limitations

- ❌ Manual setup per flag
- ❌ No trace correlation
- ❌ Difficult to scale
- ❌ No per-request impact
- ❌ Error-prone config

</div>

</div>

</div>

<div v-click class="mt-4 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Manual event configuration UI]
</div>

<!--
Manual events are better than nothing, but they don't scale and lack granularity.
-->

---
layout: default
---

# Stage 3: Automatic Event Mapping

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### The Evolution

<div v-click>
Systems that **automatically** understand:
</div>

<v-clicks>

- Where flags are evaluated
- Which services are affected  
- Correct entity mapping
- Send events without manual work

</v-clicks>

</div>

<div v-click>

```mermaid {scale: 0.65}
graph LR
    A[Flag Platform] -->|Auto-detect| B[Flag Evaluation]
    B -->|Map to| C[Service Entity]
    C -->|Send Event| D[Observability Platform]
    
    style A fill:#4a9eff
    style D fill:#6f6
```

</div>

</div>

<div v-click class="mt-4">

### Still Missing

<div class="grid grid-cols-2 gap-3 mt-2">
<div class="border border-red-500 border-dashed p-3 rounded text-sm">
<span class="text-red-400">❌ Per-request visibility</span>
<div class="text-xs mt-1">Can't see which requests were affected</div>
</div>

<div class="border border-red-500 border-dashed p-3 rounded text-sm">
<span class="text-red-400">❌ Variant-level insights</span>
<div class="text-xs mt-1">Can't compare performance across variants</div>
</div>
</div>

</div>

<div v-click class="mt-4 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Automatic event mapping flowchart]
</div>

<!--
Automatic mapping is better, but we need trace-level observability for the full picture.
-->

---
layout: center
class: text-center
---

# Stage 4: Trace-Level Observability 🎯

<div class="text-2xl mt-8 mb-8">
The **Gold Standard**
</div>

<v-clicks>

<div class="text-xl mb-4">
🔍 See flag evaluations in **every trace**
</div>

<div class="text-xl mb-4">
📊 Slice and dice by **flag** and **variant**
</div>

<div class="text-xl mb-4">
⚡ **Instantly** correlate flags to request performance
</div>

<div class="text-xl mb-4">
🎚️ Compare variants in **real-time**
</div>

</v-clicks>

<div v-click class="mt-12 text-center opacity-70">
[IMAGE PLACEHOLDER: Annotated screenshot showing trace with feature flag attributes highlighted]
</div>

<!--
This is what we're building towards - flags as first-class citizens in observability.
-->

---
layout: default
---

# Trace-Level Example

<div class="grid grid-cols-2 gap-8 mt-8">

<div>

### Traditional Trace

```yaml
trace_id: abc123
spans:
  - name: checkout_service
    duration: 5000ms
    status: ERROR
    attributes:
      http.method: POST
      http.status_code: 500
```

<div v-click class="mt-4 text-red-400">
❓ Why did this fail?
</div>

</div>

<div v-click>

### With Flag Observability

```yaml {all|7-11|all}
trace_id: abc123
spans:
  - name: checkout_service
    duration: 5000ms
    status: ERROR
    attributes:
      feature_flag.key: new-payment-processor
      feature_flag.variant: treatment
      feature_flag.provider_name: flagd
      feature_flag.context.user_id: user_456
      feature_flag.context.tier: premium
      http.method: POST
      http.status_code: 500
```

<div v-click class="mt-4 text-green-400">
✅ Flag variant "treatment" caused the error!
</div>

</div>

</div>

<!--
With trace-level data, the root cause becomes immediately obvious.
-->

---
layout: center
---

# Key Takeaway

<div class="text-3xl text-center mt-16 leading-relaxed">

Trace-level observability makes feature flags

<span class="text-blue-400 font-bold">first-class citizens</span>

in your monitoring stack

</div>

<div v-click class="mt-16 text-xl text-center opacity-80">
No more guessing. No more manual correlation.
</div>

<!--
This is the transformation we're enabling with OpenFeature and OpenTelemetry.
-->

---
layout: section
---

# Feature Flag Observability Standards

How OpenFeature & OpenTelemetry defined the solution

---
layout: default
clicks: 8
---

# OpenFeature 🤝 OpenTelemetry

<div class="grid grid-cols-2 gap-8 mt-8">

<div v-click>

### OpenFeature

- 🚩 Open standard for **feature flagging**
- 🔌 Vendor-neutral SDK
- 🎣 **Hook system** for extensibility
- 🌐 Multi-language support

</div>

<div v-click>

### OpenTelemetry

- 📊 Open standard for **observability**
- 📈 Metrics, traces, and logs
- 🏷️ **Semantic conventions** for consistency
- 🔗 End-to-end visibility

</div>

</div>

<div class="mt-8 text-center">

<div v-if="$clicks >= 2 && $clicks < 3">

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Traces| D[Observability Platform]
```

</div>

<div v-else-if="$clicks >= 3 && $clicks < 4">

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Traces| D[Observability Platform]
    
    style A fill:#4a9eff,stroke:#4a9eff,stroke-width:4px
```

</div>

<div v-else-if="$clicks >= 4 && $clicks < 5">

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Traces| D[Observability Platform]
    
    style A fill:#4a9eff
    style B fill:#9f9fff,stroke:#9f9fff,stroke-width:4px
```

</div>

<div v-else-if="$clicks >= 5 && $clicks < 6">

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Traces| D[Observability Platform]
    
    style A fill:#4a9eff
    style C fill:#f5a623,stroke:#f5a623,stroke-width:4px
```

</div>

<div v-else-if="$clicks >= 6">

```mermaid {scale: 0.8}
graph LR
    A[OpenFeature SDK] -->|Hooks| B[Flag Evaluation]
    B -->|Semantic Convention| C[OpenTelemetry]
    C -->|Traces| D[Observability Platform]
    
    style A fill:#4a9eff
    style C fill:#f5a623
    style D fill:#6f6,stroke:#6f6,stroke-width:4px
```

</div>

</div>

<div v-click class="mt-4 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: OpenFeature and OpenTelemetry logos]
</div>

<!--
Two communities came together to solve this problem with open standards.
-->

---
layout: default
---

# Anatomy of a Feature Flag Evaluation

### What We Track

<div class="grid grid-cols-2 gap-6 mt-4">

<div v-click>

#### Core Attributes

```yaml
feature_flag.key: "new-checkout"
feature_flag.variant: "treatment"
feature_flag.provider_name: "flagd"
```

</div>

<div v-click>

#### Context Data

```yaml
feature_flag.context.user_id: "user_123"
feature_flag.context.environment: "prod"
feature_flag.context.tier: "premium"
```

</div>

</div>

<div v-click class="mt-4">

#### Full Evaluation Event

```json {*|2-3|4-5|6-11|*}{at:3}
{
  "timestamp": "2025-10-10T14:30:00Z",
  "event_type": "feature_flag_evaluation",
  "trace_id": "abc123",
  "span_id": "def456",
  "attributes": {
    "feature_flag.key": "new-checkout",
    "feature_flag.variant": "treatment",
    "feature_flag.provider_name": "flagd",
    "feature_flag.context.user_id": "user_123"
  }
}
```

</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Diagram breaking down flag evaluation]
</div>

<!--
These attributes give us everything we need to correlate flags with outcomes.
-->

---
layout: default
---

# How It Works: OpenFeature Hooks

```typescript {all|1-3|5-7|9-16|18-24|all}
import { OpenFeature } from '@openfeature/server-sdk';
import { OtelHook } from '@openfeature/otel-hook';

// Register the OpenTelemetry hook
OpenFeature.addHooks(new OtelHook());
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

// Flag evaluation is now in the trace with attributes:
//   - feature_flag.key: "new-checkout-flow"
//   - feature_flag.variant: "treatment"
//   - feature_flag.context.tier: "premium"
```

<!--
The hook system makes this seamless - no manual instrumentation needed.
-->

---
layout: default
---

# Flag Evaluation in a Trace

### Distributed Trace View

```yaml {all|3-6|8-14|16-21|all}
Trace ID: 7f8a9b2c3d4e5f6a

Frontend Service (120ms)
  ├─ GET /checkout
  ├─ feature_flag.key: "new-checkout-flow"
  └─ feature_flag.variant: "treatment"

Checkout Service (890ms)
  ├─ POST /api/checkout
  ├─ feature_flag.key: "payment-processor-v2"
  ├─ feature_flag.variant: "enabled"
  ├─ feature_flag.context.user_id: "user_123"
  └─ feature_flag.context.tier: "premium"

Payment Service (2500ms) ⚠️
  ├─ POST /process-payment
  ├─ feature_flag.key: "payment-processor-v2"
  ├─ feature_flag.variant: "enabled"
  ├─ status: ERROR
  └─ error: "Connection timeout to payment gateway"
```

<div v-click class="mt-4 p-3 bg-blue-900 bg-opacity-30 rounded text-sm">
<span class="text-blue-400">✨ Insight:</span> The "payment-processor-v2" flag's "enabled" variant correlates with the timeout error
</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Visual trace waterfall with flag attributes]
</div>

<!--
Now we can see exactly which flags contributed to the failure, across services.
-->

---
layout: center
---

# Why Standards Matter

<div class="grid grid-cols-3 gap-8 mt-8">

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

<div v-click class="mt-12">

```mermaid {scale: 0.8}
graph LR
    A[Any Flag Provider] --> B[OpenFeature SDK]
    B --> C[OpenTelemetry Hook]
    C --> D[Any Observability Backend]
    
    style B fill:#4a9eff
    style C fill:#f5a623
```

</div>

<div v-click class="mt-6 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Interoperability diagram]
</div>

<!--
Standards enable an ecosystem where everything works together seamlessly.
-->

---
layout: section
---

# Progressive Delivery with Observability

Transforming theory into practice

---
layout: default
---

# What Is Progressive Delivery?

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### The Concept

<v-clicks>

- Gradually expose customers to new features
- Monitor impact at each step
- Roll back instantly if problems arise
- Minimize risk, maximize confidence

</v-clicks>

</div>

<div v-click>

### Rollout Stages

```mermaid {scale: 0.75}
graph TD
    A[0% - Dark Launch] --> B[1% - Canary]
    B --> C[10% - Early Adopters]
    C --> D[50% - Broader Rollout]
    D --> E[100% - Full Release]
    
    B -.->|Issues?| F[Instant Rollback]
    C -.->|Issues?| F
    D -.->|Issues?| F
    
    style A fill:#666
    style B fill:#f5a623
    style C fill:#4a9eff
    style D fill:#6f6
    style E fill:#0f6
    style F fill:#f66
```

</div>

</div>

<div v-click class="mt-4 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Visual diagram of gradual rollout percentages]
</div>

<!--
Progressive delivery is the new standard for feature releases, but it requires observability.
-->

---
layout: default
---

# Observability Enables Progressive Delivery

### The Feedback Loop

```typescript {all|1-5|7-13|15-19|21-25|all}
// 1. Start rollout at 1%
await flagProvider.updateRollout('new-feature', {
  percentage: 1,
  targeting: { tier: 'internal' }
});

// 2. Monitor observability data
const metrics = await observability.query({
  feature_flag_key: 'new-feature',
  feature_flag_variant: 'enabled',
  timeRange: 'last_30m'
});

if (metrics.errorRate > 0.5) {
  // Problem detected! Rollback
  await flagProvider.updateRollout('new-feature', { percentage: 0 });
  alert('Rollback triggered - error rate exceeded threshold');
}

// 3. Gradually increase if healthy
if (metrics.errorRate < 0.1 && metrics.p95Latency < 500) {
  await flagProvider.updateRollout('new-feature', { percentage: 10 });
  console.log('Rollout increased to 10%');
}

// 4. Compare variants
const comparison = await observability.compare({
  variant_a: 'control', variant_b: 'enabled', metric: 'conversion_rate'
});
```

<!--
With flag observability, progressive delivery becomes automated and data-driven.
-->

---
layout: default
---

# Real Example: OpenTelemetry Demo App

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### The Scenario

<div v-click class="text-sm">
New product recommendation algorithm with unknown performance impact
</div>

<div v-click class="mt-4">

### Rollout Plan

1. **0%** - Dark launch
2. **1%** - Internal users
3. **5%** - Monitor 24hrs
4. **25%** - Expand gradually
5. **100%** - Full rollout

</div>

</div>

<div v-click>

### Monitoring Dashboard

```yaml
# At 5% rollout
Variant: recommendation-v2

Error Rate:
  Control:   0.1%
  Treatment: 0.12% ✅

P95 Latency:
  Control:   145ms
  Treatment: 168ms ⚠️

Conversion:
  Control:   2.3%
  Treatment: 3.1% 🎉 +34%
```

<div v-click class="mt-2 p-2 bg-green-900 bg-opacity-30 rounded text-sm">
<span class="text-green-400">Decision:</span> Continue - conversion gains outweigh latency
</div>

</div>

</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: OTel Demo controlled rollout dashboard]
</div>

<!--
Real-time observability data drives rollout decisions with confidence.
-->

---
layout: center
---

# Key Benefits

<div class="grid grid-cols-2 gap-12 mt-12">

<div v-click>

### Without Flag Observability

- ❌ Blind rollouts
- ❌ Slow to detect issues
- ❌ Manual rollback decisions
- ❌ Can't compare variants
- ❌ High-risk deployments

</div>

<div v-click>

### With Flag Observability

- ✅ Data-driven rollouts
- ✅ Instant issue detection
- ✅ Automated rollback triggers
- ✅ Real-time variant comparison
- ✅ Low-risk deployments

</div>

</div>

<div v-click class="mt-16 text-center text-2xl">
Feature flag observability transforms progressive delivery from <span class="text-red-400">theory</span> to <span class="text-green-400">practice</span>
</div>

<!--
This is the power of combining feature flags with observability.
-->

---
layout: section
---

# Call to Action

Join us in building the future of observability

---
layout: default
---

# 1. Contribute to the Standards

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### OpenTelemetry Semantic Convention

<v-clicks>

- Help expand the feature flag spec
- Add new attributes and use cases
- Review and provide feedback

</v-clicks>

<div v-click class="mt-3">

```bash
# Clone the repo
git clone https://github.com/\
  open-telemetry/semantic-conventions

cd semantic-conventions
```

</div>

</div>

<div v-click>

### OpenFeature

<v-clicks>

- Implement hooks for new platforms
- Add provider integrations
- Share your use cases

</v-clicks>

<div v-click class="mt-3">

```bash
# Explore OpenFeature
git clone https://github.com/\
  open-feature/spec

cd spec
```

</div>

</div>

</div>

<div v-click class="mt-4 text-center">

<div class="inline-flex gap-3 text-sm">
<div class="px-3 py-2 bg-blue-900 bg-opacity-40 rounded">
🔗 github.com/open-telemetry/semantic-conventions
</div>
<div class="px-3 py-2 bg-blue-900 bg-opacity-40 rounded">
🔗 github.com/open-feature/spec
</div>
</div>

</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: GitHub repos with contribution guidelines]
</div>

<!--
The standards are evolving - your input shapes the future.
-->

---
layout: default
---

# 2. Adopt in Your Workflow

### Getting Started

<div class="grid grid-cols-2 gap-6 mt-4">

<div v-click>

**Step 1: Install**

```bash
npm install @openfeature/server-sdk
npm install @openfeature/otel-hook
```

</div>

<div v-click>

**Step 2: Configure Provider**

```typescript
import { OpenFeature } from '@openfeature/server-sdk';
import { FlagdProvider } from '@openfeature/flagd-provider';

await OpenFeature.setProvider(
  new FlagdProvider()
);
```

</div>

</div>

<div v-click class="mt-4">

**Step 3: Add OpenTelemetry Hook**

```typescript {all|1-2|4-5|7-12|all}
import { OtelHook } from '@openfeature/otel-hook';
import { trace } from '@opentelemetry/api';

// Register the hook globally
OpenFeature.addHooks(new OtelHook());

// Now all flag evaluations are automatically traced!
const client = OpenFeature.getClient();
const showNewFeature = await client.getBooleanValue(
  'new-feature', false, { userId: 'user_123', tier: 'premium' }
);
// ✨ This evaluation is now visible in your traces
```

</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: Implementation guide]
</div>

<!--
Implementation is straightforward - just a few lines of code.
-->

---
layout: default
---

# 3. Try It Yourself

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### OpenTelemetry Demo App

<v-clicks>

- Fully instrumented reference app
- Feature flags + observability built-in
- See the integration in action

</v-clicks>

<div v-click class="mt-4">

```bash
# Clone and run the demo
git clone https://github.com/\
  open-telemetry/opentelemetry-demo

cd opentelemetry-demo
docker compose up

# Open the app
open http://localhost:8080
```

</div>

</div>

<div v-click>

### Experiment & Learn

<div class="space-y-3 text-sm">

<div class="p-3 bg-blue-900 bg-opacity-30 rounded">
🚩 Toggle feature flags in the UI
</div>

<div class="p-3 bg-blue-900 bg-opacity-30 rounded">
📊 See flag data in traces
</div>

<div class="p-3 bg-blue-900 bg-opacity-30 rounded">
🔍 Correlate flags to errors
</div>

<div class="p-3 bg-blue-900 bg-opacity-30 rounded">
📈 Compare variant performance
</div>

</div>

</div>

</div>

<div v-click class="mt-2 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: OTel Demo app with feature flag UI]
</div>

<!--
The demo is the best way to see this in action and learn the patterns.
-->

---
layout: default
---

# 4. Share Your Feedback

<div class="grid grid-cols-3 gap-6 mt-8">

<div v-click class="text-center">

### 💬 Community

Join the conversation

<div class="mt-4 space-y-2 text-sm">
<div>CNCF Slack</div>
<div>#openfeature</div>
<div>#opentelemetry</div>
</div>

</div>

<div v-click class="text-center">

### 🐛 Issues

Report bugs & features

<div class="mt-4 space-y-2 text-sm">
<div>GitHub Issues</div>
<div>Feature requests</div>
<div>Bug reports</div>
</div>

</div>

<div v-click class="text-center">

### 📝 Blog

Share your experience

<div class="mt-4 space-y-2 text-sm">
<div>Use cases</div>
<div>Integration guides</div>
<div>Lessons learned</div>
</div>

</div>

</div>

<div v-click class="mt-8 text-center">

<div class="inline-flex gap-6 text-lg">
<div>🔗 openfeature.dev</div>
<div>🔗 opentelemetry.io</div>
</div>

</div>

<div v-click class="mt-6 text-center opacity-70 text-sm">
[IMAGE PLACEHOLDER: QR code to feedback form]
</div>

<!--
Your feedback helps us improve the standards and tooling for everyone.
-->

---
layout: center
class: text-center
---

# The Transformation

<div class="grid grid-cols-2 gap-16 mt-16 text-left">

<div v-click>

### Before

<div class="mt-6 space-y-4 text-lg">
<div>🔥 Firefighting incidents</div>
<div>❓ "What broke?"</div>
<div>⏰ Hours of debugging</div>
<div>😰 All-hands emergencies</div>
<div>🎲 Risky deployments</div>
</div>

</div>

<div v-click>

### After

<div class="mt-6 space-y-4 text-lg">
<div>📊 Proactive monitoring</div>
<div>✅ "What changed!"</div>
<div>⚡ Instant root cause</div>
<div>😌 Quick rollbacks</div>
<div>🚀 Confident deployments</div>
</div>

</div>

</div>

<div v-click class="mt-16 text-2xl">
From reactive to <span class="text-blue-400 font-bold">proactive</span> 
</div>

<div v-click class="mt-4 text-2xl">
From chaos to <span class="text-green-400 font-bold">confidence</span>
</div>

<!--
This is the future we're building together.
-->

---
layout: center
class: text-center
---

# Let's Build the Future Together

<div class="mt-16 text-xl leading-relaxed">

<v-click>

Feature flags are powerful tools for modern software delivery

</v-click>

<v-click>

But they need to be <span class="text-blue-400 font-bold">visible</span> in our observability stack

</v-click>

<v-click>

OpenFeature + OpenTelemetry make this possible with <span class="text-green-400 font-bold">open standards</span>

</v-click>

<v-click>

Join us in transforming how we understand and deploy software

</v-click>

</div>

<div v-click class="mt-16 text-3xl font-bold text-blue-400">
From "What broke?" to "What changed!"
</div>

<div v-click class="mt-8 text-center opacity-70">
[IMAGE PLACEHOLDER: Inspiring visual of a calm, well-monitored system with happy engineers]
</div>

<!--
Together, we can make feature flags first-class citizens in observability.
-->

---
layout: center
class: text-center
glowSeed: 300
---

# <span class="font-serif">Thank You! 🙏</span>

<div class="mt-12 text-xl space-y-4">
<div class="font-serif text-2xl">Questions?</div>
<div class="mt-8 opacity-80">Find us after the talk or online:</div>
</div>

<div class="mt-12 grid grid-cols-2 gap-8 text-left max-w-2xl mx-auto">

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-6>
  <div flex items-center mb-4>
    <div i-carbon:link text-blue-300 text-2xl mr-3 />
    <h3 class="text-lg font-semibold">Resources</h3>
  </div>
  <div flex flex-col gap-3 text-sm>
    <div flex items-center>
      <div i-carbon:flag mr-2 text-purple-400 />
      <span>openfeature.dev</span>
    </div>
    <div flex items-center>
      <div i-carbon:chart-line mr-2 text-amber-400 />
      <span>opentelemetry.io</span>
    </div>
    <div flex items-center>
      <div i-carbon:logo-github mr-2 text-green-400 />
      <span>github.com/open-telemetry/opentelemetry-demo</span>
    </div>
  </div>
</div>

<div border="2 solid white/5" rounded-lg overflow-hidden bg="white/5" backdrop-blur-sm p-6>
  <div flex items-center mb-4>
    <div i-carbon:user-multiple text-green-300 text-2xl mr-3 />
    <h3 class="text-lg font-semibold">Connect</h3>
  </div>
  <div flex flex-col gap-3 text-sm>
    <div flex items-center>
      <div i-carbon:chat mr-2 text-blue-400 />
      <span>CNCF Slack: #openfeature</span>
    </div>
    <div flex items-center>
      <div i-carbon:chat mr-2 text-amber-400 />
      <span>CNCF Slack: #opentelemetry</span>
    </div>
    <div flex items-center>
      <div i-carbon:logo-twitter mr-2 text-blue-300 />
      <span>@openfeature / @opentelemetry</span>
    </div>
  </div>
</div>

</div>

<div class="abs-br m-6 flex items-center gap-2 opacity-50">
  <div i-carbon:presentation-file />
  <span text-sm>Slides: github.com/open-feature/presentations</span>
</div>

<!--
Thank you all for your time! We're excited to work with you on this.
-->
