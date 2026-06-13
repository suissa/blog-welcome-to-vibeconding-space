<img width="1916" height="821" alt="image" src="https://github.com/user-attachments/assets/75bf89aa-cf48-4886-a4a5-5ca4725f90e3" />


# Choreographed Claude Dynamic Workflows

When I read about Claude Dynamic Workflows, I noticed that the workflow patterns themselves are not limited to a single agentic coding environment. The interesting part is not only that an AI can classify, fan out, verify, filter, compare and loop. The interesting part is how those patterns are coordinated.

In a Claude-style Dynamic Workflow, the system usually feels highly orchestrated. There is a central intelligence deciding what to do next, which subtask to run, which tool or agent to invoke, how to merge results and when the final answer is ready.

That model is powerful because orchestration gives the system:

* a central point of control
* a clear execution path
* a predictable plan
* explicit branching
* easier user-facing progress
* a single place to synthesize the final result

But my architecture naturally pushes these same patterns toward choreography.

In a choreographed workflow, there is not always a single controller telling every component what to do. Instead, each semantic behavior reacts to events. A behavior does not need to understand the whole system. It only needs to understand:

* the semantic contract it validates
* the event it subscribes to
* the event it emits
* the payload shape it accepts
* the error shape it enriches
* the terminal states it can produce

This creates a hybrid model.

The high-level intent can still be orchestrated. But the execution can be choreographed.

In this model:

* `IntentGraph / graflow` defines the macro-intention.
* `QuarkBehavior` defines the smallest semantic behavior.
* `AtomicBehavior` composes semantic behaviors.
* `SubAgents` react to events.
* `ProofAgents` verify correctness.
* `HealingAgents` repair invalid states.
* `BenchmarkAgents` measure cost, latency and execution behavior.
* `Governor` decides whether the final result is acceptable.

The orchestrator says what must be achieved.

The choreographed agents discover how to continue by reacting to semantic events.

That is the core idea behind `Choreographed Claude Dynamic Workflows`: take the same workflow patterns that appear in AI coding agents and reinterpret them as event-driven semantic behaviors.

## 1. Classify-And-Act

`Classify-And-Act` is the most basic pattern in AI systems.

A user sends a message. The system classifies the intent. Then the runtime decides which behavior should handle that payload.

In a traditional orchestrated version, the flow looks like this:

```graph
IntentResolver -> Router -> Behavior -> Response
```

In a choreographed semantic version, the flow becomes:

```graph
received.message -> classified.intent -> requested.behavior -> validated.behavior -> ended.behavior.in.success | ended.behavior.in.error
```

The difference is subtle but important.

In the orchestrated version, the router owns the sequence.

In the choreographed version, each stage emits a semantic event, and the next behavior reacts to that event.

A message can be classified as:

* `CreateEntityIntent`
* `UpdateEntityIntent`
* `FindEntityIntent`
* `SendMessageIntent`
* `ValidateFieldIntent`
* `HealInvalidPayloadIntent`
* `GenerateBehaviorIntent`
* `VerifyGeneratedCodeIntent`

After classification, the system does not merely call a function.

It selects a semantic behavior, validates the payload, extracts primitive values, converts types when needed and emits one of the terminal events:

* `ended.{Behavior}.in.success`
* `ended.{Behavior}.in.error`

This makes the runtime more modular because every stage can be replaced, observed, healed, benchmarked or formally verified without rewriting the whole flow.

## 2. Fanout-And-Synthesize

`Fanout-And-Synthesize` is the pattern where one input is distributed to multiple specialized workers, and the system later synthesizes their outputs into one accepted result.

In a Claude-style workflow, this often looks like a central agent deciding to split the problem into subtasks, send those subtasks to parallel workers and merge the results.

In a choreographed semantic workflow, fanout can happen through event subscription.

When a payload reaches a subscriber, the runtime can automatically call the `forge` function of the corresponding `Semantic QuarkBehavior Type`. That behavior validates the input, extracts the primitive value, tries type conversion if needed and emits its own error event if validation fails.

This is fanout because one incoming payload can be observed by multiple independent semantic behaviors:

* type validator
* primitive extractor
* schema validator
* semantic validator
* language-specific compiler
* proof checker
* benchmark runner
* self-healing pipeline

The synthesis step is not just a text merge.

It is a semantic reduction.

The runtime synthesizes:

* which value is valid
* which conversion succeeded
* which behavior failed
* which language-specific type rule caused the failure
* which healing function should be added or reused
* which generated artifact is acceptable

For code generation, this becomes even stronger.

The same semantic intention can be distributed across multiple specialized agents:

* one agent generates TypeScript
* one agent generates Zig
* one agent generates Haskell
* one agent generates Prolog rules
* one agent generates tests
* one agent verifies semantic equivalence
* one agent benchmarks execution cost
* one agent synthesizes the final accepted behavior

This is not simply parallel generation.

It is fanout over semantic responsibility.

## 3. Adversarial Verification

`Adversarial Verification` is the workflow where a generated behavior, function, translation or implementation is not immediately trusted.

Another agent receives the candidate with one explicit goal:

* prove that it is wrong

The adversarial agent should not behave like a friendly reviewer. It should behave like:

* attacker
* compiler
* type checker
* property-based tester
* malicious user
* runtime failure simulator
* semantic equivalence breaker

In a choreographed implementation, the generator does not need to directly call every adversarial verifier.

It only publishes the fact that a candidate was generated:

```graph
DSL.pub("generated.candidate")

generated.candidate -> request.adversarialVerification.for.candidate
```

After that, multiple verifiers can subscribe to the candidate event:

* `TypeAdversaryAgent`
* `SecurityAdversaryAgent`
* `SemanticEquivalenceAdversaryAgent`
* `LinearUsageAdversaryAgent`
* `RuntimeCrashAdversaryAgent`
* `PayloadCorruptionAdversaryAgent`
* `PromptInjectionAdversaryAgent`

Each verifier emits a result:

* `passed.verification`
* `found.counterexample`
* `found.securityViolation`
* `found.semanticDrift`
* `found.runtimeFailure`

The graph can be represented as:

```graph
generated.candidate -> request.adversarialVerification.for.candidate

request.adversarialVerification.for.candidate
  -> fanout(TypeAdversaryAgent, SecurityAdversaryAgent, SemanticEquivalenceAdversaryAgent, LinearUsageAdversaryAgent)

TypeAdversaryAgent -> [
  Ok:pub("passed.verification.by.TypeAdversaryAgent"),
  Error:pub("found.counterexample.by.TypeAdversaryAgent")
]

SecurityAdversaryAgent -> [
  Ok:pub("passed.verification.by.SecurityAdversaryAgent"),
  Error:pub("found.securityViolation.by.SecurityAdversaryAgent")
]

SemanticEquivalenceAdversaryAgent -> [
  Ok:pub("passed.verification.by.SemanticEquivalenceAdversaryAgent"),
  Error:pub("found.semanticDrift.by.SemanticEquivalenceAdversaryAgent")
]

LinearUsageAdversaryAgent -> [
  Ok:pub("passed.verification.by.LinearUsageAdversaryAgent"),
  Error:pub("found.linearViolation.by.LinearUsageAdversaryAgent")
]
```

When one of these agents finds a failure, it should not merely return “failed”.

It should return a counterexample.

That counterexample becomes input for the self-healing pipeline:

```graph
found.counterexample -> execute.selfHealingPipeline
found.securityViolation -> execute.selfHealingPipeline
found.semanticDrift -> execute.selfHealingPipeline
found.linearViolation -> execute.selfHealingPipeline
```

This is where the workflow becomes truly choreographed. The generator does not need to know which verifier exists. It only emits a candidate. Any verifier that understands that event can subscribe, attack and enrich the system with new knowledge.

## 4. Generate-And-Filter

`Generate-And-Filter` is a less aggressive version of `Adversarial Verification`.

Instead of generating one answer and trusting it, the system generates multiple candidates and filters them through deterministic gates.

For example, when generating a new `QuarkBehavior`, the system can produce several candidates:

* minimal implementation
* strict implementation
* fast implementation
* safe implementation
* linear implementation
* language-native implementation

Then the filter removes anything that fails:

* schema validation
* type validation
* unit tests
* property-based tests
* semantic equivalence tests
* compilation
* linting
* runtime benchmark
* security policy
* linear consumption rule
* ZeroTrust rule

The key principle is simple:

* generation is cheap
* acceptance must be strict

In graph form:

```graph
generated.candidate -> filtered.candidate -> accepted.candidate | rejected.candidate
```

A more complete pipeline can look like this:

```graph
accepted.intent -> generate.candidates

generate.candidates -> generated.candidate.1
generate.candidates -> generated.candidate.2
generate.candidates -> generated.candidate.3

generated.candidate.1 -> execute.filterPipeline
generated.candidate.2 -> execute.filterPipeline
generated.candidate.3 -> execute.filterPipeline

execute.filterPipeline -> [
  Ok:pub("accepted.candidate"),
  Error:pub("rejected.candidate")
]
```

The filter pipeline itself can be decomposed into independent semantic gates:

```graph
execute.filterPipeline -> execute.compileCheck
execute.compileCheck -> execute.unitTests
execute.unitTests -> execute.propertyTests
execute.propertyTests -> execute.semanticEquivalenceCheck
execute.semanticEquivalenceCheck -> execute.securityPolicyCheck
execute.securityPolicyCheck -> execute.linearUsageCheck
execute.linearUsageCheck -> execute.benchmarkCheck
execute.benchmarkCheck -> pub("accepted.candidate")
```

Or it can be fully choreographed:

```graph
ready.candidate.for.filtering -> filter.candidate.with.CompileFilterAgent
ready.candidate.for.filtering -> filter.candidate.with.TestFilterAgent
ready.candidate.for.filtering -> filter.candidate.with.SecurityFilterAgent
ready.candidate.for.filtering -> filter.candidate.with.LinearFilterAgent
ready.candidate.for.filtering -> filter.candidate.with.SemanticFilterAgent
ready.candidate.for.filtering -> filter.candidate.with.BenchmarkFilterAgent
```

Each filter publishes its own decision:

```graph
CompileFilterAgent -> pub("passed.filter.compile")
TestFilterAgent -> pub("passed.filter.tests")
SecurityFilterAgent -> pub("passed.filter.security")
LinearFilterAgent -> pub("passed.filter.linear")
SemanticFilterAgent -> pub("passed.filter.semantic")
BenchmarkFilterAgent -> pub("passed.filter.benchmark")
```

When all required filters pass, the `Governor` can promote the artifact:

```graph
passed.allRequiredFilters -> accept.candidate.with.Governor
accept.candidate.with.Governor -> pub("promoted.artifact")
```

This makes the workflow deterministic.

The AI can generate possibilities, but the runtime decides what is allowed to become part of the system.

## 5. Tournament

`Tournament` is the workflow I would use when there is no obvious best implementation.

Instead of asking for one solution, the system generates competing candidates and scores them against the same rubric.

A tournament can compare:

* different languages
* different type encodings
* different serialization formats
* different healing strategies
* different sharding strategies
* different event aggregation windows
* different SQL-to-graflow translation plans
* different implementations of the same `QuarkBehavior`

A tournament should produce a scorecard, not just a winner.

The scorecard can measure:

* test success
* semantic equivalence
* compilation success
* CPU cost
* memory usage
* allocation count
* healing cost
* traceability
* generated code size
* proof difficulty
* ZeroTrust compliance
* linear consumption safety

The tournament graph can look like this:

```graph
defined.problem -> open.tournament
open.tournament -> generate.candidate.with.TypeScriptAgent
open.tournament -> generate.candidate.with.ZigAgent
open.tournament -> generate.candidate.with.HaskellAgent
open.tournament -> generate.candidate.with.PrologAgent
open.tournament -> generate.candidate.with.RustAgent
```

Or, for the same language with different strategies:

```graph
defined.problem -> generate.candidate.with.minimalStrategy
defined.problem -> generate.candidate.with.strictStrategy
defined.problem -> generate.candidate.with.fastStrategy
defined.problem -> generate.candidate.with.safeStrategy
defined.problem -> generate.candidate.with.provableStrategy
defined.problem -> generate.candidate.with.distributedStrategy
```

Each candidate enters the tournament with the same intention but a different strategy.

The winner is not selected because it looks better.

It is selected by measurement.

A full tournament workflow can be represented as:

```graph
opened.tournament -> generate.candidates
generated.candidates -> request.benchmark.for.candidates
generated.candidates -> request.proof.for.candidates
generated.candidates -> request.adversarialVerification.for.candidates
generated.candidates -> request.semanticEquivalence.for.candidates

request.benchmark.for.candidates -> pub("scored.benchmark")
request.proof.for.candidates -> pub("scored.proof")
request.adversarialVerification.for.candidates -> pub("scored.adversarialVerification")
request.semanticEquivalence.for.candidates -> pub("scored.semanticEquivalence")

scored.benchmark, scored.proof, scored.adversarialVerification, scored.semanticEquivalence
  -> build.tournamentScoreboard

build.tournamentScoreboard -> select.tournamentWinner
select.tournamentWinner -> pub("selected.tournamentWinner")
```

The tournament pattern turns opinion into measurement.

It prevents the system from trusting the first good answer.

It also allows different kinds of winners:

* best implementation for production
* best implementation for formal proof
* best implementation for benchmark
* best implementation for documentation
* best implementation for portability
* best implementation for security

This is where the graph becomes a higher level of abstraction. The entire process of generation, validation, scoring and promotion can be represented as a semantic workflow.

## 6. Loop-Until-Done

`Loop-Until-Done` is the clearest expression of `Intent-based Healing`.

Most systems treat an error as the end of the flow.

In this model, an error is another event.

When a `QuarkBehavior` receives an invalid payload, it should not immediately return a final error. It should emit an error event with enough context for the system to attempt recovery.

The error payload should contain:

* original payload
* last known payload state
* invalid value
* expected primitive type
* expected `SemanticType`
* semantic behavior that failed
* internal trace/span
* failed function
* previous conversion attempt
* healing history
* rejection reason

From there, the behavior or a specialized healing agent can try to repair the payload.

The canonical workflow is:

```graph
AtomicBehavior.pub("execute.{QuarkBehavior}")

execute.{QuarkBehavior}
  -> QuarkBehavior.forge
  -> [
    Ok:pub("ended.{QuarkBehavior}.in.success"),
    Error:pub("failed.{QuarkBehavior}.at.validation")
  ]

failed.{QuarkBehavior}.at.validation
  -> execute.selfHealingPipeline
  -> [
    Ok:pub("ended.{QuarkBehavior}.in.success"),
    Error:pub("failed.{QuarkBehavior}.at.selfHealingPipeline")
  ]

failed.{QuarkBehavior}.at.selfHealingPipeline
  -> unwrapRecursiveToPrimitive
  -> [
    Ok:pub("ended.{QuarkBehavior}.in.success"),
    Error:pub("failed.{QuarkBehavior}.at.unwrapRecursiveToPrimitive")
  ]

failed.{QuarkBehavior}.at.unwrapRecursiveToPrimitive
  -> convertTypePipeline
  -> [
    Ok:pub("ended.{QuarkBehavior}.in.success"),
    Error:pub("failed.{QuarkBehavior}.at.convertTypePipeline")
  ]

failed.{QuarkBehavior}.at.convertTypePipeline
  -> convertSemanticData
  -> [
    Ok:pub("ended.{QuarkBehavior}.in.success"),
    Error:pub("ended.{QuarkBehavior}.in.error")
  ]

AtomicBehavior.sub("ended.{QuarkBehavior}.in.success")
AtomicBehavior.sub("ended.{QuarkBehavior}.in.error")
```

This loop is not blind retry.

Blind retry repeats the same operation and hopes for a different result.

Intent-based healing is different because every new attempt must enrich the state.

If the payload is an object, the system tries to extract the primitive value.

If the primitive value is a string, the system tries to convert it to the expected type.

If the type conversion works but the semantic validation fails, the system tries to repair the semantic value.

If the value cannot be safely repaired, the system can request user collaboration.

If the user collaborates, the system can learn a new correction strategy.

If the correction becomes recurrent, it can become a new reusable healing behavior.

A more explicit healing workflow can be described as:

```graph
detected.error -> enrich.error
enrich.error -> request.primitiveExtraction -> [
  Ok:request.typeConversion,
  Error:pub("ended.healing.in.error")
]

request.typeConversion -> [
  Ok:request.semanticValidation,
  Error:request.semanticConversion
]

request.semanticConversion -> [
  Ok:request.semanticValidation,
  Error:request.userCollaboration
]

request.semanticValidation -> [
  Ok:pub("ended.healing.in.success"),
  Error:execute.selfHealingPipeline
]

request.userCollaboration -> [
  Ok:pub("created.learningEvent"),
  Error:pub("ended.intent.in.error")
]

created.learningEvent -> create.healingFunction
create.healingFunction -> execute.selfHealingPipeline
```

The important rule is that the loop must have memory.

Each iteration should add information:

* which conversion failed
* which primitive was extracted
* which semantic rule rejected the value
* which healing function was attempted
* which language-specific type rule was involved
* which candidate repair was produced
* which repair should never be retried

Without memory, it is a retry loop.

With memory, it becomes `Intent-based Healing`.

## Why Choreography Matters

The main difference between an orchestrated Dynamic Workflow and a choreographed one is where responsibility lives.

In an orchestrated workflow, the central controller owns the sequence.

In a choreographed semantic workflow, each behavior owns its local contract, emits semantic events and reacts to the events it understands.

That changes the system design.

The workflow becomes:

* easier to extend
* easier to observe
* easier to test
* easier to benchmark
* easier to verify
* easier to heal
* easier to distribute

A new verifier can subscribe to `generated.candidate`.

A new filter can subscribe to `ready.candidate.for.filtering`.

A new healing behavior can subscribe to `failed.{QuarkBehavior}.at.convertTypePipeline`.

A new governor can subscribe to `passed.allRequiredFilters`.

The graph does not need to be rewritten every time a new behavior exists.

That is why I call this idea `Choreographed Claude Dynamic Workflows`.

It is not a rejection of orchestration.

> **It is a reinterpretation of Claude Dynamic Workflows as semantic, event-driven, composable and self-healing behavior graphs.**

