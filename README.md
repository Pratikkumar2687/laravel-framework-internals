# Laravel Framework Internals

A source-driven exploration of Laravel's framework internals, undocumented APIs, framework behavior, and the gap between Laravel's source code and its official documentation.

## About This Project

This project is for Laravel developers who already use Laravel in production and want to understand the framework beyond its everyday APIs.

It is **not**:

* A Laravel beginner tutorial
* A general PHP tutorial
* A collection of productivity tips
* A guide to shipping applications faster

Instead, the goal is to develop a deeper mental model of Laravel by reading the framework's own source code alongside its official documentation.

> **The goal is not to use Laravel better by memorizing more APIs. The goal is to understand what is happening underneath the APIs you already use.**

## The "Open the Hood" Approach

Most Laravel developers know how to:

* Route a request
* Create migrations
* Define models
* Dispatch jobs
* Use queues
* Register service providers
* Work with collections
* Use facades and contracts

This project takes the next step:

**What actually happens inside the framework?**

Understanding the implementation can make unfamiliar behavior less mysterious and make framework-level decisions easier to reason about.

The metaphor is simple:

> Laravel is the car you already drive. This project opens the hood.

## Research Methodology

Every investigation follows the same basic principle:

**Read the Laravel source code and the matching Laravel documentation together.**

For this project, the reference versions are:

```text
laravel/framework  → v13.22.0
laravel/docs       → 13.x
```

The exact version is less important than keeping the source and documentation versions aligned.

When researching another Laravel release, always compare:

```text
Framework release/tag
        +
Matching documentation branch
```

Do not compare an old framework tag with documentation from another release.

## Two Levels of Research

### 1. Class-Level Comparison

When Laravel provides documentation for a class, the process is:

```text
Laravel Source Class
        │
        ▼
List public methods
        │
        ▼
Laravel Documentation
        │
        ▼
List documented methods
        │
        ▼
Compare the two lists
        │
        ▼
Investigate mismatches
```

For example, a class such as:

* `Str`
* `Collection`
* `Gate`

can be examined by comparing every public method in the source with the methods explicitly documented on its corresponding documentation page.

A method that exists in the source but does not appear on the relevant documentation page becomes a **candidate for investigation**.

A mismatch is not automatically considered a finding.

It must pass additional checks.

### 2. Component-Level Research

Some Laravel framework classes do not have dedicated documentation pages.

For those cases, research starts with the component's root classes:

```text
Illuminate/<Component>/
```

The investigation asks:

> Does this class appear anywhere in the official Laravel documentation?

Classes that are clearly internal framework infrastructure are filtered out.

Examples include:

* Internal drivers
* Service providers used only for framework wiring
* Exception classes
* Thin interface implementations
* Storage-layer infrastructure
* Framework-only plumbing

The goal is to identify classes and APIs that could realistically be useful to Laravel application developers.

## What Does "Undocumented" Mean?

This project uses a deliberately narrow definition.

**Undocumented** means:

> The exact method or class name does not appear on the relevant official Laravel documentation page, checked against the matching documentation branch.

It does **not** mean:

* Poorly explained
* Missing examples
* Briefly mentioned
* Difficult to find
* Documented on another related page

Those are different problems.

Keeping this definition narrow prevents the research from turning ordinary documentation criticism into claims of undocumented functionality.

## Candidate Evaluation

Finding a public method missing from the documentation is only the beginning.

Each candidate is investigated using several signals.

### Public Visibility

The method must be realistically usable by an application.

A method being technically public does not automatically make it an intended application API.

### Naming

Names can provide clues about intended usage.

Methods with names suggesting internal framework operations should be treated cautiously.

For example:

```text
assembleInternalState()
registerDriverHook()
```

A public method can still represent an implementation detail.

### Test Coverage

The framework's test suite provides another useful signal.

A method deliberately exercised by meaningful tests provides evidence that its behavior is intentionally maintained.

A method that is never exercised deserves additional investigation.

### Changelog

The framework's changelog can reveal:

* When the method was introduced
* Whether it was intended to be public
* Whether it represents a recent change
* Whether documentation was expected to follow

### Pull Requests

When available, the original pull request can provide valuable context.

It may reveal whether the documentation omission was:

* Accidental
* Intentional
* Temporary
* Related to an evolving API
* A consequence of framework internals

## The Three Rules

Every chapter follows three core rules.

### Rule 1 — Public and Realistically Usable

The API must be realistically usable by an application.

Internal plumbing that happens to be reachable is not automatically considered a useful discovery.

### Rule 2 — Genuinely Undocumented

The exact method or class name must be absent from the relevant official documentation.

The source and documentation must correspond to the same Laravel release.

### Rule 3 — Not Merely a Trivial Alias

A candidate should provide meaningful information beyond an already documented API.

If it is essentially an alias or duplicate, the relationship must be explicitly explained.

## Important: Findings Are Provisional

Passing all of the checks does not automatically prove that an API is intended for application developers.

A candidate may still turn out to be:

* Framework plumbing
* An implementation detail
* An API intentionally excluded from documentation
* Recently introduced functionality
* Behavior that may change in a future release

Therefore, every finding should be treated as **provisional until the implementation, tests, history, and surrounding framework behavior have been examined carefully.**

## Example Investigation

A simplified investigation looks like this:

```text
1. Select a Laravel class
        ↓
2. Open the source code
        ↓
3. List its public methods
        ↓
4. Open the matching documentation page
        ↓
5. List documented methods
        ↓
6. Compare both lists
        ↓
7. Identify mismatches
        ↓
8. Check visibility and intended usage
        ↓
9. Read tests
        ↓
10. Check changelog
        ↓
11. Investigate the original pull request
        ↓
12. Decide whether the candidate is worth documenting
```

No specialized tooling is required.

Basic tools such as:

* `grep`
* Git
* Side-by-side diffs
* Laravel source code
* Official documentation
* Test suites
* Changelogs
* GitHub pull requests

are sufficient for much of the research.

## Repository Structure

```text
.
├── README.md
│
├── chapters/
│   ├── 01-str/
│   ├── 02-collection/
│   ├── 03-gate/
│   └── ...
│
├── research/
│   ├── source-vs-docs/
│   ├── changelog/
│   └── pull-requests/
│
└── scripts/
    └── README.md
```

Each chapter should document:

1. The Laravel component
2. The source file
3. The matching documentation
4. Candidate APIs
5. Why the candidate is interesting
6. Test coverage
7. Framework history
8. Practical application use
9. Limitations and risks
10. Whether the API should be considered stable

## Version Awareness

Laravel changes continuously.

A method that is undocumented in one release may become documented in another.

Likewise, a method that exists in one release may change or disappear later.

Therefore, every research entry should record the framework version being investigated.

Example:

```text
Laravel Framework: v13.22.0
Laravel Documentation: 13.x
Research Date: 2026
```

When updating an investigation for a new Laravel release, repeat the source/documentation comparison rather than assuming the previous result remains valid.

## Why This Matters

Understanding framework internals can help developers reason about:

* Unexpected framework behavior
* Performance characteristics
* Extension points
* API design
* Service container behavior
* Collections
* Routing
* Authentication
* Authorization
* Queues
* Events
* Database abstractions
* Framework lifecycle
* Application architecture

The objective is not to encourage developers to depend on undocumented internals blindly.

Quite the opposite.

Understanding the difference between:

```text
Documented API
        vs.
Public but undocumented API
        vs.
Internal implementation detail
```

is itself an important engineering skill.

## Open Source Contributions

This repository is intentionally open.

If you find:

* An undocumented Laravel API
* A missing explanation
* An interesting framework implementation
* A useful source-code discovery
* A newer Laravel release that changes an existing finding

open an issue or submit a pull request.

Please include:

* Laravel version
* Source file
* Exact class/method name
* Relevant documentation page
* Evidence supporting the finding
* Tests or framework history where applicable
* Practical explanation

The goal is collaborative framework research rather than a collection of unverified API discoveries.

## Disclaimer

An undocumented public method is **not automatically a recommended API**.

Before using an undocumented framework method in production, consider:

* API stability
* Framework upgrade risk
* Intended visibility
* Test coverage
* Maintainer intent
* Availability of documented alternatives

This project is intended to improve understanding of Laravel internals, not encourage unnecessary dependency on unstable implementation details.

## References

* Laravel Framework: https://github.com/laravel/framework
* Laravel Documentation: https://github.com/laravel/docs
* Laravel Documentation: https://laravel.com/docs

## Author

**Pratikkumar Patel**

Senior Cloud Engineer & Backend Architect

Areas of interest:

* Laravel
* PHP
* Backend Engineering
* Software Architecture
* Cloud Engineering
* AWS
* Framework Internals
* PostgreSQL
* DevOps

---

⭐ If you find an interesting Laravel framework behavior, feel free to open an issue or contribute a research finding.

