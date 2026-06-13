<img width="1916" height="821" alt="image" src="https://github.com/user-attachments/assets/75bf89aa-cf48-4886-a4a5-5ca4725f90e3" />


# Choreographed Claude Dynamic Workflows

When I read about Dynamic Workflows, I noticed that I was already using most of these patterns before naming them. The difference is that my implementation does not start from a single highly orchestrated script. It starts from semantic behaviors that publish and subscribe to events.

In a traditional orchestrated workflow, a central controller knows the whole sequence: classify, call tool, wait, branch, retry, merge, finish. This is useful when the system needs predictable visibility and centralized policy enforcement. But in my architecture, many flows are naturally choreographed. A `QuarkBehavior` does not need to know the whole system. It only needs to know the semantic contract it validates, the events it emits, and the events it subscribes to.

This creates a hybrid model:

- `IntentGraph / graflow` defines the macro-intention.
- `QuarkBehavior` defines the smallest semantic unit.
- `AtomicBehavior` composes semantic behaviors.
- `SubAgents` react to events.
- `ProofAgents` verify correctness.
- `HealingAgents` repair invalid states.
- `BenchmarkAgents` measure cost, latency and execution behavior.
- `Governor` decides whether the result is acceptable.

In this model, orchestration is used for high-level intent, while choreography is used for local adaptation. The orchestrator says what must be achieved. The agents discover how to continue by reacting to semantic events.

## 1. Classify-And-Act

This is the first pattern I ever used with AI, even before I had a formal name for it. A user sends a message, the system classifies the intent, and the runtime routes the payload to the correct behavior.

In my architecture, this is not just “intent classification”. It is semantic routing.

A message can be classified as:

- `CreateEntityIntent`
- `UpdateEntityIntent`
- `FindEntityIntent`
- `SendMessageIntent`
- `ValidateFieldIntent`
- `HealInvalidPayloadIntent`
- `GenerateBehaviorIntent`
- `VerifyGeneratedCodeIntent`

After classification, the system does not merely call a function. It selects a semantic behavior, validates the payload, extracts primitive values, converts types when needed, and emits either `success` or `error`.

Orchestrated version:

- `IntentResolver -> Router -> Behavior -> Response`

Choreographed version:

- `message.received -> intent.classified -> behavior.requested -> behavior.validated -> success | error`

The choreographed version is more aligned with my runtime, because every stage can be replaced, observed, healed, benchmarked or formally verified without changing the whole flow.

## 2. Fanout-And-Synthesize

This is already embedded in the way my runtime treats `sub` functions.

When a payload reaches a subscriber, the runtime automatically calls the `forge` function of the corresponding Semantic QuarkBehavior Type. The behavior validates the input, extracts the primitive value, tries type conversion if needed, and emits its own error event if validation fails.

This is a form of fanout because one incoming payload can be tested by multiple independent semantic behaviors:

- type validator
- primitive extractor
- schema validator
- semantic validator
- language-specific compiler
- proof checker
- benchmark runner
- self-healing pipeline

The synthesis step is not only a text merge. It is a semantic reduction.

The runtime synthesizes:

- which value is valid
- which conversion succeeded- 
which behavior failed
- which language-specific type rule caused the failure
- which healing function should be added or reused
- which generated artifact is acceptable

In a VibeCoding workflow, this becomes even stronger:- 
one agent generates TypeScript
- one agent generates Zig
- one agent generates Haskell
- one agent generates Prolog rules
- one agent generates tests
- one agent verifies semantic equivalence
- one agent benchmarks cost
- one agent synthesizes the final accepted behavior

This is not simply parallel generation. It is fanout over semantic responsibility.

## 3. Adversarial Verification

This is the workflow I should formalize more explicitly.

Adversarial Verification means that after a behavior, function, DSL translation or generated implementation is produced, another agent receives the output with the explicit goal of proving it wrong.

The adversarial agent should not behave like a reviewer trying to be helpful. It should behave like an attacker, compiler, type checker, property-based tester, malicious user and runtime failure simulator.

In my architecture, this can be implemented as:

`candidate.generated -> adversarial.verification.requested`

Then multiple verifiers subscribe:

- `TypeAdversaryAgent`
- `SecurityAdversaryAgent`
- `SemanticEquivalenceAdversaryAgent`
- `LinearUsageAdversaryAgent`
- `RuntimeCrashAdversaryAgent`
- `PayloadCorruptionAdversaryAgent`
- `PromptInjectionAdversaryAgent`

Each one emits either:

- `verification.passed`
- `counterexample.found`
- `security.violation.found`
- `semantic.drift.found`

`runtime.failure.found`

This is a natural choreographed workflow. The generator does not need to call every verifier directly. It only emits a candidate. Any verifier that understands that event can subscribe and attack it.

For my system, this is essential because I generate code in many languages from one semantic DSL. If TypeScript, Zig, Haskell and Prolog implementations claim to represent the same behavior, an adversarial verifier must try to find where they diverge.

## 4. Generate-And-Filter

Generate-And-Filter is the less aggressive version of Adversarial Verification.

In this pattern, the system generates multiple possible solutions and then filters them using deterministic gates.

For example, when generating a new `QuarkBehavior`, the system can produce several candidates:

- minimal implementation
- strict implementation
- fast implementation
- safe implementation
- linear implementation
- language-native implementation

Then the filters remove anything that fails:

- schema validation
- type validation
- unit tests
- property-based tests
- semantic equivalence tests
- compilation
- linting
- runtime benchmark
- security policy
- linear consumption rule
- zero-trust rule

This is already close to how I work manually during VibeCoding: I ask for variations, compare them, keep the useful parts, discard what breaks the architecture, and convert the winning pattern into a reusable behavior.

In my runtime, the filter should not be subjective. It should be a pipeline of executable acceptance gates:

- `candidate.generated -> candidate.filtered -> candidate.accepted | candidate.rejected`

The key idea is that generation is cheap, but acceptance must be strict.

## 5. Tournament

Tournament is the pattern I should use when there is no obvious best implementation.

Instead of generating one solution and validating it, I generate many competing solutions and score them against the same rubric.

For my architecture, a tournament can compare:

- different languages
- different type encodings
- different serialization formats
- different healing strategies
- different sharding strategies
- different event aggregation windows
- different SQL-to-graflow translation plans
- different implementations of the same QuarkBehavior

A tournament should produce a scorecard, not just a winner.

Example dimensions:
- passes tests
- preserves semantic equivalence
- compiles successfully
- has lower CPU cost
- uses less memory
- has fewer allocations
- has lower healing cost
- has better traceability
- has smaller generated code
- is easier to prove
- is safer under ZeroTrust rules
- does not violate linear consumption

The tournament workflow is naturally choreographed:

- `candidate.submitted -> benchmark.requested -> proof.requested -> score.submitted -> tournament.closed -> winner.selected`

Each candidate is independent. Each scoring agent is independent. The tournament coordinator only closes the round and selects the winner after enough scores are received.

This is exactly how I can turn VibeCoding into engineering: I do not need to trust the first output. I can create a competition between implementations and let tests, proofs, benchmarks and policies decide.

## 6. Loop-Until-Done

Loop-Until-Done is the clearest expression of Intent-based Healing.

Most systems return an error when a value does not match the expected type or semantic constraint. My system treats the error as a new event.

A failed behavior emits `error`.

The same behavior, or a specialized healing agent, subscribes to that error.

The healing pipeline receives the original payload, the last payload state, the invalid value, the behavior that rejected it, and the internal trace.

Then it tries to repair the payload.

If it succeeds, it emits `success`.

If it fails, it unwraps the value recursively to a primitive representation, tries type conversion, tries semantic conversion, and emits a new error with a richer state.

The loop is not blind repetition. It is a convergence loop.

The workflow is:

```graph
QuarkBehavior -> pub(error) -> sub(error) -> selfHealingPipeline -> [Ok:pub(success), Error:unwrapRecursiveToPrimitive]
unwrapRecursiveToPrimitive -> convertTypePipeline -> [Ok:pub(success), Error:unwrapRecursiveToPrimitive]
convertTypePipeline -> convertSemanticData -> [Ok:pub(success), pub(error)]
```

The important rule is that this loop must have memory. Every iteration must add information:

- which conversion failed
- which primitive was extracted
- which semantic rule rejected the value
- which healing function was attempted
- which language-specific type rule was involved
- which candidate repair was produced
- which repair should never be retried

Without memory, it is a retry loop. With memory, it becomes Intent-based Healing.

## Looping VibeCoding Engineering

Looping VibeCoding Engineering is the name I would give to the way I work and the way I want my agents to work.

It is not just asking an AI to code.

It is a loop of:

- classifying the intent
- generating candidate implementations
- fanning out into specialized agents
- filtering invalid outputs
- attacking accepted outputs
- benchmarking alternatives
- selecting winners
- healing failures
- turning the repair into a reusable behavior

The final goal is not to avoid errors. The final goal is to convert every error into a new semantic behavior that prevents the same error from happening again.

That is why this architecture naturally became Intent-based Healing. A user intention should only fail when there is no safe, valid or cooperative path to complete it. Every other failure is just missing knowledge waiting to become a new behavior.
