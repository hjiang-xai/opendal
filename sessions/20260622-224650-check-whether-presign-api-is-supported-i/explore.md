## Loaded memory entries
(no relevant entries)

## Verdict
OpenDAL's Go binding does support the presign API for the simple read, write, delete, and stat operations.

The supported Go surface is not a 1:1 exposure of the Rust core/facade presign API. Rust also exposes option-bearing presign variants (`presign_*_with` and `presign_*_options`) for read, write, stat, and delete. The Go binding currently exposes only simple `PresignRead`, `PresignWrite`, `PresignDelete`, and `PresignStat` methods with `path` and `expire`, returning `*http.Request`.

## Relevant locations
- `core/core/src/types/operator/operator.rs:1975` — Rust async `Operator::presign_stat` delegates to `presign_stat_with`.
- `core/core/src/types/operator/operator.rs:1994` — Rust exposes `presign_stat_with`, whose future supports extra options.
- `core/core/src/types/operator/operator.rs:2041` — Rust exposes `presign_stat_options` with explicit `options::StatOptions`.
- `core/core/src/types/operator/operator.rs:2102` — Rust async `Operator::presign_read` delegates to `presign_read_with`.
- `core/core/src/types/operator/operator.rs:2129` — Rust exposes `presign_read_with`.
- `core/core/src/types/operator/operator.rs:2176` — Rust exposes `presign_read_options`.
- `core/core/src/types/operator/operator.rs:2240` — Rust async `Operator::presign_write` delegates to `presign_write_with`.
- `core/core/src/types/operator/operator.rs:2271` — Rust exposes `presign_write_with`.
- `core/core/src/types/operator/operator.rs:2320` — Rust exposes `presign_write_options`.
- `core/core/src/types/operator/operator.rs:2349` — Rust begins the delete presign section; docs say simple `presign_delete` wraps `presign_delete_with`.
- `core/core/src/types/operator/operator_futures.rs:131` — Rust defines `FuturePresignStat` and option setters for override headers and ETag conditions.
- `core/core/src/types/operator/operator_futures.rs:177` — Rust defines `FuturePresignRead` and option setters for override headers and ETag conditions.
- `core/core/src/types/operator/operator_futures.rs:215` — Rust defines `FuturePresignWrite` and option setters for content headers.
- `core/core/src/raw/ops.rs:237` — Raw `OpPresign` stores expiration and a `PresignOperation`.
- `core/core/src/raw/ops.rs:271` — `PresignOperation` variants include stat, read, and write; subsequent lines include the rest of the operation enum.
- `core/core/src/raw/rps.rs:36` — Raw `RpPresign` wraps a `PresignedRequest`.
- `core/core/src/raw/rps.rs:54` — `PresignedRequest` stores HTTP method, URI, and headers.
- `core/core/src/raw/accessor.rs:235` — Raw `Service::presign` requires `Capability::presign` and the matching operation capability.
- `core/core/src/blocking/operator.rs:192` — Rust blocking operator exposes `presign_stat`.
- `core/core/src/blocking/operator.rs:199` — Rust blocking operator exposes `presign_read`.
- `core/core/src/blocking/operator.rs:206` — Rust blocking operator exposes `presign_write`.
- `core/core/src/blocking/operator.rs:213` — Rust blocking operator exposes `presign_delete`.
- `bindings/c/src/presign.rs:100` — C binding exposes `opendal_operator_presign_read`.
- `bindings/c/src/presign.rs:131` — C binding exposes `opendal_operator_presign_write`.
- `bindings/c/src/presign.rs:162` — C binding exposes `opendal_operator_presign_delete`.
- `bindings/c/src/presign.rs:192` — C binding exposes `opendal_operator_presign_stat`.
- `bindings/c/src/presign.rs:223` — C binding exposes accessors for presigned request method, URI, headers, header length, and free.
- `bindings/go/presign.go:34` — Go exposes `(*Operator).PresignRead(path string, expire time.Duration) (*http.Request, error)`.
- `bindings/go/presign.go:39` — Go exposes `(*Operator).PresignWrite(path string, expire time.Duration) (*http.Request, error)`.
- `bindings/go/presign.go:44` — Go exposes `(*Operator).PresignDelete(path string, expire time.Duration) (*http.Request, error)`.
- `bindings/go/presign.go:49` — Go exposes `(*Operator).PresignStat(path string, expire time.Duration) (*http.Request, error)`.
- `bindings/go/presign.go:54` — Go converts `time.Duration` to whole seconds before calling FFI.
- `bindings/go/presign.go:69` — Go builds a `net/http.Request` from method, URI, and headers returned by C.
- `bindings/go/presign.go:100` — Go binds `opendal_operator_presign_read`.
- `bindings/go/presign.go:124` — Go binds `opendal_operator_presign_write`.
- `bindings/go/presign.go:148` — Go binds `opendal_operator_presign_delete`.
- `bindings/go/presign.go:172` — Go binds `opendal_operator_presign_stat`.
- `bindings/go/operator_info.go:300` — Go exposes `Capability.Presign()`.
- `bindings/go/operator_info.go:304` — Go exposes `Capability.PresignRead()`.
- `bindings/go/operator_info.go:308` — Go exposes `Capability.PresignStat()`.
- `bindings/go/operator_info.go:312` — Go exposes `Capability.PresignWrite()`.
- `bindings/go/operator_info.go:316` — Go exposes `Capability.PresignDelete()`.
- `bindings/go/types.go:201` — Go FFI capability layout includes `presign` and per-operation presign flags.
- `bindings/go/tests/behavior_tests/presign_test.go:33` — Go behavior tests include presign tests gated by capability flags.
- `bindings/go/tests/behavior_tests/presign_test.go:54` — Go behavior test covers presigned write.
- `bindings/go/tests/behavior_tests/presign_test.go:76` — Go behavior test covers presigned read.
- `bindings/go/tests/behavior_tests/presign_test.go:95` — Go behavior test covers presigned stat.
- `bindings/go/tests/behavior_tests/presign_test.go:115` — Go behavior test covers presigned delete.
- `bindings/go/tests/behavior_tests/opendal_test.go:75` — Go behavior test suite includes `testsPresign(cap)`.
- `bindings/go/reader.go:63` — Go read convention uses variadic functional options and switches to `opendal_operator_read_with` when options are present.
- `bindings/go/reader.go:187` — Go already defines read override header options marked "presign only".
- `bindings/go/stat.go:66` — Go stat convention uses variadic functional options and switches to `opendal_operator_stat_with` when options are present.
- `bindings/go/stat.go:127` — Go already defines stat override header options marked meaningful only with presign.
- `bindings/go/writer.go:58` — Go write convention uses variadic functional options and switches to `opendal_operator_write_with` when options are present.
- `bindings/go/writer.go:84` — Go already defines write content header options that correspond to Rust write presign options.
- `bindings/go/delete.go:92` — Go delete convention uses variadic functional options and switches to `opendal_operator_delete_with` when options are present.
- `bindings/go/README.md:6` — Go binding is native Go over `opendal-c` via purego/libffi, without CGO.
- `bindings/go/Makefile:61` — Go binding builds the C library with selected service features for tests/service packages.
- `bindings/go/Makefile:70` — Go service packages are generated/compressed around the C library.

## Existing conventions
- Public Go operator methods are synchronous/blocking methods on `*Operator`; I found no `AsyncOperator` or async public Go operator surface in `bindings/go`.
- Go methods return Go-native types instead of exposing raw FFI objects. Presign returns `*http.Request`; stat returns `*Metadata`; read returns `[]byte`; operations return `error`.
- Option-bearing Go operations use variadic functional options such as `opts ...WithReadFn`, `opts ...WithStatFn`, `opts ...WithWriteFn`, and `opts ...WithDeleteFn`.
- When no options are provided, Go methods call the simple C symbol, for example `opendal_operator_read`; when options are provided, they allocate C options and call the matching `_with` symbol.
- FFI wrappers are declared with `newFFI(ffiOpts{sym: ..., rType: ..., aTypes: ...}, adapter)` and convert Go strings to byte pointers before calling C symbols.
- C binding presign functions currently call Rust blocking simple methods only: `op.presign_read`, `op.presign_write`, `op.presign_delete`, and `op.presign_stat`.
- Presigned request ownership is C-side until Go builds `*http.Request`; Go defers `opendal_presigned_request_free` after converting method, URI, and headers.
- Capability flags are exposed in Go through `Capability` methods and are used to gate behavior tests.
- Go behavior tests are service-driven through `OPENDAL_TEST`, generated `scheme_test.go`, and companion `opendal-go-services/<service>` packages.

## Support assessment
- Supported: `PresignRead(path, expire)`, `PresignWrite(path, expire)`, `PresignDelete(path, expire)`, and `PresignStat(path, expire)` in Go.
- Supported result fields: HTTP method, URL/URI, and headers are available on the returned `*http.Request`.
- Supported capability checks: global `Presign()` plus per-operation `PresignRead()`, `PresignStat()`, `PresignWrite()`, and `PresignDelete()`.
- Covered by tests: Go behavior tests exercise presigned read/write/stat/delete when backend capabilities allow them.
- Not exposed in Go: Rust option-bearing presign variants (`presign_read_with/options`, `presign_write_with/options`, `presign_stat_with/options`, and likely `presign_delete_with/options`) are not present in the Go public API or C presign bridge.
- Partial mismatch: Go already has read/stat override header options documented as "presign only", but the current Go presign methods do not accept those options, so those existing option helpers cannot affect presign calls.
- Precision detail: Go truncates `time.Duration` to whole seconds before FFI; C reconstructs `Duration::from_secs`.

## Minimal impact surface if extending Go presign option support
- `bindings/c/src/presign.rs` — add option-bearing C presign functions, or replace simple internals with functions that accept option pointers; this is the primary bridge gap.
- `bindings/c/include/opendal.h` — update exported C declarations if C header generation is not automatic for the added symbols.
- `bindings/go/presign.go` — add variadic options to `PresignRead`, `PresignWrite`, `PresignStat`, and possibly `PresignDelete`, or add separate `Presign*With` methods matching existing Go style.
- `bindings/go/reader.go` — reuse existing `WithReadFn` parsing and `newOpendalReadOptions`; no new read option type appears necessary for read presign.
- `bindings/go/stat.go` — reuse existing `WithStatFn` parsing and `newOpendalStatOptions`; no new stat option type appears necessary for stat presign.
- `bindings/go/writer.go` — reuse existing `WithWriteFn` parsing and `newOpendalWriteOptions`; no new write option type appears necessary for write presign.
- `bindings/go/delete.go` — reuse existing `WithDeleteFn` parsing if delete presign options are intended to match Rust delete options.
- `bindings/go/tests/behavior_tests/presign_test.go` — add focused tests for option-bearing presign where backend capabilities support the relevant option flags.
- `bindings/go/README.md` or website Go binding docs — update if adding or clarifying option-bearing presign API.

## Unknowns / checks not run
- I did not run tests, per Azusa explorer role.
- I did not inspect generated release artifacts in `opendal-go-services`; the source binding and Makefile show how service packages are generated around the C library.
- I did not verify whether `bindings/c/include/opendal.h` is generated or manually maintained in this workflow. The source and header both currently contain simple presign symbols.

## Orchestrator handoff
- **Verdict**: done
- **Artifact**: `sessions/20260622-224650-check-whether-presign-api-is-supported-i/explore.md`
- **Next**: spawn Mugi
- **Note**: Go supports simple presign today; only option-bearing presign parity needs planning if desired.
