# Go Review Skill

## When to use

The user asked for a Go review (idiomatic style, error handling, goroutines, channels, context, modules) of a file, module, or PR.

## When not to use

- Architecture: `software-architecture`.
- Concurrency design beyond Go idiom: `concurrency-patterns`.
- Errors as a contract beyond Go idiom: `error-handling-patterns`.
- Microservice boundaries: `microservices-architecture`.
- Testing strategy: `testing-strategy`.

## Inputs to inspect first

- `go.mod`, `go.sum`, Go version (`go` directive), module path.
- `main.go` or library entrypoint; package layout.
- Test files (`*_test.go`), build tags, generate directives.
- Lint setup (`golangci-lint.yaml`), formatter (`gofmt`/`goimports`).
- Runtime model: HTTP server, CLI, library, gRPC service.

## How to work

1. Inspect `go.mod` and project layout.
2. Run `go vet`, `go test`, `golangci-lint run` if practical.
3. Walk Go-specific concerns: error wrapping, context propagation, goroutine leaks, channel ownership, panic vs error, interface placement (consumer-defined), zero values, slices/maps aliasing.
4. Group findings by severity. Do not modify code unless asked.

## Output

Findings cited by file and line, grouped: blockers (goroutine leaks, ignored errors, race conditions), defects (missing context propagation, weak error wrapping, interface placement), nits (gofmt/goimports drift, naming).

## Escalation

- Architecture: `software-architecture`.
- Concurrency design beyond Go idiom: `concurrency-patterns`.
- Errors beyond Go idiom: `error-handling-patterns`.
- Microservices, gRPC, sync chains: `microservices-architecture`, `integration-analysis`.
- Security: `security-review`.

---

## Purpose

Review Go code for correctness, idiomatic style, concurrency safety, and common language-specific pitfalls.

## Responsibilities

- Review package structure and naming conventions
- Assess error handling patterns and propagation
- Evaluate goroutine and channel usage for correctness and leak prevention
- Review interface design and appropriate use of composition
- Detect race conditions and shared mutable state issues
- Assess context propagation across goroutines and calls
- Review defer usage and resource cleanup
- Evaluate dependency management and module configuration

## Instructions

- Start from the package entrypoint or the changed files
- Check error handling: are errors returned and checked? are they wrapped with context (`fmt.Errorf("...: %w", err)`)?
- Check goroutines: is there a termination condition? can they leak?
- Check channels: are they closed correctly? is there a risk of send on closed channel?
- Check `context.Context`: is it the first parameter? is it propagated to all downstream calls?
- Check defer: is it used for resource cleanup? is the execution order correct (LIFO)?
- Check interfaces: are they defined at the consumer side, not the producer?
- Check for naked returns in long functions — a readability and correctness risk

## Heuristics

Treat as stronger concerns when:

- Errors ignored with `_` without comment
- Goroutines with no exit condition or cancellation mechanism
- Send on potentially closed channel
- Missing `context.Context` propagation in I/O or long-running calls
- `sync.Mutex` locked without deferred unlock
- Interface defined in the same package as the implementation (violates consumer-side interface principle)
- Global mutable state without synchronization
- `init()` functions with side effects that make testing difficult

Treat as acceptable when:

- Naked returns in very short functions where intent is obvious
- Missing context in purely computational functions with no I/O

## Rules

- Every error must be handled — `_` on error return requires a comment explaining why
- Goroutines must have a defined lifecycle and exit condition
- Context must propagate to all I/O calls
- Defer resource cleanup (Close, Unlock, Cancel) at acquisition site

## Activity Traceability

🔧 Loading skill: `go-review`
