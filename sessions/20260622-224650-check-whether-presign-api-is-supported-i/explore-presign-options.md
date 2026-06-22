## Loaded memory entries
(no relevant entries; the referenced `skills/memory-loading` URL returned 404 during this exploration)

## Verdict
Go can expose option-bearing presign APIs, but not by only editing Go today.

Rust async `Operator` already has `presign_read_options`, `presign_write_options`, `presign_delete_options`, and `presign_stat_options`. Rust blocking `Operator` currently exposes only simple `presign_read`, `presign_write`, `presign_delete`, and `presign_stat`. The C binding owns an `opendal::blocking::Operator`, so C needs Rust blocking option-bearing presign methods first, then new C `_with` presign symbols, then Go FFI/public methods.

## Relevant locations
- `core/core/src/types/operator/operator.rs:2041` - Rust async exposes `Operator::presign_stat_options(path, expire, options::StatOptions)`.
- `core/core/src/types/operator/operator.rs:2176` - Rust async exposes `Operator::presign_read_options(path, expire, options::ReadOptions)`.
- `core/core/src/types/operator/operator.rs:2320` - Rust async exposes `Operator::presign_write_options(path, expire, options::WriteOptions)`.
- `core/core/src/types/operator/operator.rs:2433` - Rust async exposes `Operator::presign_delete_options(path, expire, options::DeleteOptions)`.
- `core/core/src/types/operator/operator.rs:2199` - Read presign converts `ReadOptions` into range plus `OpRead`, then wraps it in `OpPresign`.
- `core/core/src/types/operator/operator.rs:2343` - Write presign converts `WriteOptions` into `OpWrite`, then wraps it in `OpPresign`.
- `core/core/src/types/operator/operator.rs:2456` - Delete presign converts `DeleteOptions` into `OpDelete`, then wraps it in `OpPresign`.
- `core/core/src/blocking/operator.rs:192` - Rust blocking exposes only simple `presign_stat`.
- `core/core/src/blocking/operator.rs:199` - Rust blocking exposes only simple `presign_read`.
- `core/core/src/blocking/operator.rs:206` - Rust blocking exposes only simple `presign_write`.
- `core/core/src/blocking/operator.rs:213` - Rust blocking exposes only simple `presign_delete`.
- `core/core/src/blocking/operator.rs:296` - Rust blocking already has normal `stat_options`.
- `core/core/src/blocking/operator.rs:383` - Rust blocking already has normal `read_options`.
- `core/core/src/blocking/operator.rs:447` - Rust blocking already has normal `write_options`.
- `core/core/src/blocking/operator.rs:610` - Rust blocking already has normal `delete_options`.
- `bindings/c/src/operator.rs:40` - C `opendal_operator` owns an `opendal::blocking::Operator`.
- `bindings/c/src/presign.rs:102` - C exposes `opendal_operator_presign_read(op, path, expire_secs)` only.
- `bindings/c/src/presign.rs:133` - C exposes `opendal_operator_presign_write(op, path, expire_secs)` only.
- `bindings/c/src/presign.rs:164` - C exposes `opendal_operator_presign_delete(op, path, expire_secs)` only.
- `bindings/c/src/presign.rs:194` - C exposes `opendal_operator_presign_stat(op, path, expire_secs)` only.
- `bindings/c/include/opendal.h:2160` - Public C header declares simple `opendal_operator_presign_read`.
- `bindings/c/include/opendal.h:2167` - Public C header declares simple `opendal_operator_presign_write`.
- `bindings/c/include/opendal.h:2174` - Public C header declares simple `opendal_operator_presign_delete`.
- `bindings/c/include/opendal.h:2181` - Public C header declares simple `opendal_operator_presign_stat`.
- `bindings/c/src/types.rs:249` - C already has `opendal_delete_options` with `version` and `recursive`.
- `bindings/c/src/types.rs:340` - C already has `opendal_write_options` covering append, content headers, conditional headers, concurrency, chunking, and user metadata.
- `bindings/c/src/types.rs:542` - C converts `opendal_write_options` to Rust `options::WriteOptions`.
- `bindings/c/src/types.rs:590` - C already has `opendal_stat_options` covering version, conditional headers, timestamps, and response header overrides.
- `bindings/c/src/types.rs:736` - C converts `opendal_stat_options` to Rust `options::StatOptions`.
- `bindings/c/src/types.rs:764` - C already has `opendal_read_options` covering range, version, conditional headers, read execution hints, and response header overrides.
- `bindings/c/src/types.rs:1019` - C converts `opendal_read_options` to Rust `options::ReadOptions`.
- `bindings/go/presign.go:35` - Go exposes `PresignRead(path, expire)` without options.
- `bindings/go/presign.go:40` - Go exposes `PresignWrite(path, expire)` without options.
- `bindings/go/presign.go:45` - Go exposes `PresignDelete(path, expire)` without options.
- `bindings/go/presign.go:50` - Go exposes `PresignStat(path, expire)` without options.
- `bindings/go/presign.go:54` - Go presign helper truncates `time.Duration` to whole seconds before FFI.
- `bindings/go/presign.go:100` - Go FFI binds the simple C read presign symbol.
- `bindings/go/presign.go:124` - Go FFI binds the simple C write presign symbol.
- `bindings/go/presign.go:148` - Go FFI binds the simple C delete presign symbol.
- `bindings/go/presign.go:172` - Go FFI binds the simple C stat presign symbol.
- `bindings/go/reader.go:63` - Normal Go `Read` uses variadic `opts ...WithReadFn`.
- `bindings/go/reader.go:187` - Go already has read response override options documented as presign-only.
- `bindings/go/reader.go:239` - Go already has `newOpendalReadOptions`.
- `bindings/go/writer.go:58` - Normal Go `Write` uses variadic `opts ...WithWriteFn`.
- `bindings/go/writer.go:181` - Go already has `newOpendalWriteOptions`.
- `bindings/go/delete.go:29` - Go already has `WithDeleteFn`.
- `bindings/go/delete.go:92` - Normal Go `Delete` uses variadic `opts ...WithDeleteFn`.
- `bindings/go/stat.go:66` - Normal Go `Stat` uses variadic `opts ...WithStatFn`.
- `bindings/go/stat.go:127` - Go stat response override options are documented as meaningful with presign.
- `bindings/go/stat.go:173` - Go already has `newOpendalStatOptions`.
- `bindings/go/types.go:138` - Go FFI result type for presign is already defined.
- `bindings/go/ffi.go:52` - New Go FFI symbols are registered by adding `newFFI` values.
- `bindings/go/tests/behavior_tests/presign_test.go:33` - Go behavior presign tests are gated by presign capabilities.
- `bindings/c/tests/test_suites_presign.cpp:397` - C presign read test uses only the simple C symbol.
- `bindings/c/tests/test_suites_presign.cpp:485` - C presign write test uses only the simple C symbol.
- `bindings/c/tests/test_suites_presign.cpp:592` - C presign stat test uses only the simple C symbol.
- `bindings/c/tests/test_suites_presign.cpp:680` - C presign delete test uses only the simple C symbol.
- `bindings/go/string_ownership_test.go:676` - Go has local FFI signature tests for normal write-with symbols.
- `bindings/go/string_ownership_test.go:969` - Go has local FFI signature tests for normal read-with symbols.

## Existing conventions
- Public Go methods keep simple overloads as variadic methods, for example `Read(path, opts ...WithReadFn)` and `Write(path, data, opts ...WithWriteFn)`.
- Go chooses the simple C symbol when no options are supplied and the `_with` C symbol only when options exist.
- Go converts C presigned requests into `*http.Request`, then frees the C presigned request.
- Go option builders allocate C options via `newOpendal*Options`, hold Go byte slices alive until the FFI call returns, and free C option structs with `defer`.
- C binding normal operations accept nullable `*const opendal_*_options` and convert null to Rust default options.
- C header declarations live in `bindings/c/include/opendal.h`; the contributing docs say the header is under `include`, but this exploration did not prove whether the project expects manual updates or regeneration for each symbol.

## Minimal files likely touched
- `core/core/src/blocking/operator.rs` - add blocking `presign_read_options`, `presign_write_options`, `presign_delete_options`, and `presign_stat_options` wrappers over the async methods.
- `bindings/c/src/presign.rs` - add C option-bearing presign functions, likely `opendal_operator_presign_read_with`, `opendal_operator_presign_write_with`, `opendal_operator_presign_delete_with`, and `opendal_operator_presign_stat_with`.
- `bindings/c/include/opendal.h` - expose the new C functions in the public header, by regeneration or direct update according to binding workflow.
- `bindings/go/presign.go` - add Go variadic-option handling and FFI wrappers for new C `_with` presign functions.
- `bindings/go/tests/behavior_tests/presign_test.go` - add behavior coverage for option-bearing presign requests.
- `bindings/go/string_ownership_test.go` - add local tests for new Go FFI signatures and option-path string lifetime reuse.

Optional, only if C-level behavior tests are desired in the same change:
- `bindings/c/tests/test_suites_presign.cpp` - add C tests for `_with` presign functions.

Likely not touched:
- `bindings/go/reader.go`, `bindings/go/writer.go`, `bindings/go/delete.go`, `bindings/go/stat.go` - existing option types and C-option builders are enough to reuse.
- `bindings/c/src/types.rs` - existing C option structs and conversions are enough unless the desired presign surface deliberately excludes non-presign execution hints.

## Rust and C readiness
- Rust async has the core API needed today.
- Rust blocking does not currently have option-bearing presign methods.
- C currently does not have enough API to implement Go presign options directly, because C stores only a blocking operator and exposes only simple presign symbols.
- C already has enough option structs to represent the desired read/write/delete/stat option surfaces once blocking presign option methods exist.

Recommended dependency order:
1. Add Rust blocking `presign_*_options`.
2. Add C `opendal_operator_presign_*_with` symbols using existing `opendal_*_options` structs.
3. Add Go public/FFI surface on top of those C symbols.

## Recommended Go API shape
Preserve the existing simple methods by making them variadic, matching existing Go style:

- `func (op *Operator) PresignRead(path string, expire time.Duration, opts ...WithReadFn) (*http.Request, error)`
- `func (op *Operator) PresignWrite(path string, expire time.Duration, opts ...WithWriteFn) (*http.Request, error)`
- `func (op *Operator) PresignDelete(path string, expire time.Duration, opts ...WithDeleteFn) (*http.Request, error)`
- `func (op *Operator) PresignStat(path string, expire time.Duration, opts ...WithStatFn) (*http.Request, error)`

This preserves all existing calls because existing two-argument calls still compile. It also reuses the option names users already know from normal `Read`, `Write`, `Delete`, and `Stat`.

Implementation convention should mirror normal operations:
- no options: call existing simple C symbols.
- options present: parse existing Go option funcs, allocate existing C option structs, call new C `_with` presign symbols, keep Go string buffers alive through the call, then build `*http.Request` and free the C presigned request.

## Option surface notes
- Read presign should reuse `WithReadFn`, including range, version, conditional headers, and response header overrides. Some execution hints like concurrent/chunk/gap/content length hint are already part of `ReadOptions`; Rust async accepts the full `ReadOptions` surface.
- Write presign should reuse `WithWriteFn`, including content headers, conditional headers, if-not-exists, user metadata, and current execution options. Rust async accepts the full `WriteOptions` surface.
- Delete presign should reuse `WithDeleteFn`. Rust async accepts `DeleteOptions`; current Go has version and recursive.
- Stat presign should reuse `WithStatFn`, including version, conditional headers, timestamps, and response header overrides.

## Test strategy
- Go local FFI tests in `bindings/go/string_ownership_test.go`:
  - Assert each new `ffiOperatorPresign*With` return type is `typeResultPresign`.
  - Assert arg types are pointer, pointer, uint64, pointer.
  - Add option-path tests that exercise `newOpendalReadOptions`, `newOpendalWriteOptions`, and `newOpendalStatOptions` through presign helpers so string keepalive behavior is covered.
- Go behavior tests in `bindings/go/tests/behavior_tests/presign_test.go`:
  - `PresignRead(path, expire, ReadWithRange(offset, length))`: write content, fetch presigned URL, assert returned body equals selected range.
  - `PresignRead(path, expire, ReadWithOverrideContentDisposition("attachment; filename=..."))`: fetch presigned URL and assert response header when backend supports `read_with_override_content_disposition`.
  - `PresignWrite(path, expire, WriteWithContentType("text/plain"))`: upload through returned request, then `Stat` and assert content type when backend supports `write_with_content_type` and stat metadata reports it.
  - `PresignStat(path, expire, StatWithIfMatch(etag))`: get ETag from `Stat`, presign stat with matching ETag, perform request and assert success; add failing invalid ETag case only if backend behavior is stable under existing capability gates.
  - `PresignDelete(path, expire, DeleteWithVersion(version))`: only for version-capable backends if fixtures can create/read a version. Otherwise keep delete-with-options covered by unit/FFI tests and avoid a flaky behavior test.
- C tests in `bindings/c/tests/test_suites_presign.cpp` if C API is expanded in the same PR:
  - Add at least one `_with` smoke test per operation where backend capabilities make it stable.
  - Prioritize read range and write content type because they are observable via HTTP response/body or later stat.

## Unknowns
- I did not run tests, per explorer role.
- I did not prove the exact command used to regenerate `bindings/c/include/opendal.h`; I only confirmed the header currently contains simple presign declarations and C docs say the header lives under `include`.
- I did not inspect service-specific signing behavior deeply enough to guarantee every option is honored by every backend; tests should be capability-gated and focused on observable, already-tested option families.

## Orchestrator handoff
- **Verdict**: done
- **Artifact**: `sessions/20260622-224650-check-whether-presign-api-is-supported-i/explore-presign-options.md`
- **Next**: spawn Mugi
- **Note**: Rust async and C option structs are ready, but Rust blocking and C presign `_with` symbols are needed before Go can expose option-bearing presign.
