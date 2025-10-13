# Expanded Narrative and Flow

1. Title and Introduction (2 minutes, 2-3 slides)
Slide 1: Title Slide

Title: "From What Broke to What Changed"
Subtitle: Feature Flags as First-Class Signals in Observability
Tags: KubeCon + CloudNativeCon, 25 minutes, 2 speakers
Placeholder: Dynamic montage of dashboards, traces, and feature flags.
Slide 2: Cold Open Story

Text: “Imagine your monitoring dashboards are flashing red. Error rates have skyrocketed. Your team is scrambling to find the root cause. Hours later, you discover it wasn’t a bug—it was a feature flag change.”
Placeholder: A screenshot of a chaotic monitoring dashboard with a spike in errors and latency.
Slide 3: The Problem in One Sentence

Text: “Feature flags are hidden from observability tools, making it difficult to pinpoint changes as the root cause of incidents.”
Placeholder: A simple diagram showing a feature flag toggle leading to a spike in error rates.

2. Understanding the Problem (4 minutes, 4-5 slides)
Slide 4: What Are Feature Flags?

Text: “Feature flags allow teams to toggle features on/off without deploying code. They’re used for releases, A/B testing, and permissions.”
Placeholder: Diagram of a feature flag toggle controlling a feature rollout.
Slide 5: The Observability Gap

Text: “Feature flags operate in the background, but observability tools don’t track them. This leaves teams blind to their impact.”
Placeholder: A screenshot of a trace or dashboard with no reference to feature flags.
Slide 6: Real-World Example: OpenTelemetry Demo App

Text: “In the OpenTelemetry Demo App, feature flags are used to enable a problem internally. Observability tools only show symptoms like high response times or error spikes.”
Placeholder: Screenshot of the OpenTelemetry Demo dashboard showing a spike in errors.
Slide 7: Why This Matters

Text: “Without visibility into feature flags, teams waste time diagnosing issues, leading to longer Mean Time to Recovery (MTTR).”
Placeholder: A timeline showing MTTR with and without feature flag observability.

3. History of the Problem (5 minutes, 5-6 slides)
Slide 8: Flying Blind

Text: “Most teams start with no visibility into feature flags, relying on guesswork to diagnose issues.”
Placeholder: A cartoon of a blindfolded engineer at a dashboard.
Slide 9: Manual Change Events

Text: “Some teams manually send change events from their feature flag management system to observability tools. This works, but it doesn’t scale.”
Placeholder: Screenshot of a manual configuration UI for sending events.
Slide 10: Why Manual Events Don’t Scale

Text: “Manual setup is error-prone and doesn’t provide trace-level insights. Teams still can’t correlate changes to specific requests.”
Placeholder: Diagram showing a feature flag change disconnected from traces.
Slide 11: Automatic Event Mapping

Text: “Next step: Systems that understand where flags are evaluated and automatically send change events to the correct entity.”
Placeholder: Flowchart of automatic event mapping.
Slide 12: Trace-Level Observability

Text: “The gold standard: Trace-level observability allows teams to slice and dice requests by flag and variant to see the exact impact.”
Placeholder: Screenshot of a trace with feature flag details.
Slide 13: Key Takeaway

Text: “Trace-level observability makes feature flags first-class citizens in monitoring.”
Placeholder: Highlighted trace with feature flag attributes.

4. Feature Flag Observability Standards (5 minutes, 5-6 slides)
Slide 14: Collaboration Between OpenFeature and OpenTelemetry

Text: “OpenFeature and OpenTelemetry worked together to define what a feature flag evaluation looks like.”
Placeholder: Logos of OpenFeature and OpenTelemetry.
Slide 15: Anatomy of a Feature Flag Evaluation

Text: “We defined key attributes like flag name, variant, context, and evaluation timing.”
Placeholder: Diagram breaking down a feature flag evaluation.
Slide 16: How It Works in OpenFeature

Text: “OpenFeature uses hooks to capture evaluation data and send it to observability tools.”
Placeholder: Flowchart showing data flow from evaluation to observability.
Slide 17: Example: Flag Evaluation in a Trace

Text: “Here’s how a feature flag evaluation appears in a trace.”
Placeholder: Annotated screenshot of a trace with feature flag details.
Slide 18: Why Standards Matter

Text: “Standards ensure interoperability, making it easier for teams to adopt feature flag observability.”
Placeholder: Diagram showing interoperability between tools.

5. Progressive Delivery with Observability (5 minutes, 5-6 slides)
Slide 19: What Is Progressive Delivery?

Text: “Progressive delivery gradually exposes customers to a feature while monitoring its impact.”
Placeholder: Diagram of a gradual rollout process.
Slide 20: How Observability Enables Progressive Delivery

Text: “Feature flag observability provides granular control and immediate rollback if something goes wrong.”
Placeholder: Flowchart of a feature rollout with observability checkpoints.
Slide 21: Real-World Example: OpenTelemetry Demo App

Text: “In the OpenTelemetry Demo App, we can use observability to control a feature rollout and mitigate risk.”
Placeholder: Screenshot of a controlled feature rollout dashboard.
Slide 22: Key Benefits

Text: “Feature flag observability transforms progressive delivery from theory to practice.”
Placeholder: List of benefits (e.g., reduced risk, faster rollouts, better user experience).

6. Call to Action (4 minutes, 3-4 slides)
Slide 23: Join the Movement

Text: “Contribute to the OpenTelemetry semantic convention for feature flags and expand the observability spec.”
Placeholder: Links to GitHub repos and documentation.
Slide 24: Adopt the Standards

Text: “Implement OpenFeature and OpenTelemetry in your workflows to improve feature flag observability.”
Placeholder: Screenshot of an implementation guide or code snippet.
Slide 25: Provide Feedback

Text: “Try the standards in your tools and share feedback to help us improve.”
Placeholder: Contact information and QR code for feedback form.
Slide 26: Closing Message

Text: “With feature flag observability, we can shift from firefighting to proactive innovation. Let’s move from asking what broke to confidently answering what changed.”
Placeholder: Inspiring image of a calm, well-monitored system.