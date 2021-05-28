# Spack / Node OOM Bug w/ Array Definition

When attempting to configure Spack with an array the process hangs and eventually leads to a Javascript OOM error.

To reproduce:

* `yarn install`
* `npx spack`

```
<--- Last few GCs --->

[23032:0x630a870]   102040 ms: Mark-sweep (reduce) 3792.5 (4104.2) -> 3791.6 (4104.7) MB, 4160.4 / 0.0 ms  (average mu = 0.060, current mu = 0.002) allocation failure scavenge might not succeed
[23032:0x630a870]   105999 ms: Mark-sweep (reduce) 3792.8 (4101.7) -> 3791.9 (4102.7) MB, 3954.4 / 0.0 ms  (average mu = 0.032, current mu = 0.001) allocation failure scavenge might not succeed


<--- JS stacktrace --->

FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
 1: 0xa222f0 node::Abort() [spack]
 2: 0x96411f node::FatalError(char const*, char const*) [spack]
 3: 0xb97f1e v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [spack]
 4: 0xb98297 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [spack]
 5: 0xd52fd5  [spack]
 6: 0xd53b5f  [spack]
 7: 0xd61beb v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [spack]
 8: 0xd657ac v8::internal::Heap::AllocateRawWithRetryOrFailSlowPath(int, v8::internal::AllocationType, v8::internal::AllocationOrigin, v8::internal::AllocationAlignment) [spack]
 9: 0xd33e5b v8::internal::Factory::NewFillerObject(int, bool, v8::internal::AllocationType, v8::internal::AllocationOrigin) [spack]
10: 0x107c5af v8::internal::Runtime_AllocateInYoungGeneration(int, unsigned long*, v8::internal::Isolate*) [spack]
11: 0x1423279  [spack]
[1]    23032 abort      npx spack
```

---

```
<--- Last few GCs --->

[4419:0x623d870]    82549 ms: Mark-sweep 3836.1 (4125.9) -> 3821.9 (4130.2) MB, 3878.3 / 0.0 ms  (average mu = 0.168, current mu = 0.013) allocation failure scavenge might not succeed
[4419:0x623d870]    86926 ms: Mark-sweep 3841.3 (4130.7) -> 3827.2 (4135.4) MB, 4305.0 / 0.0 ms  (average mu = 0.089, current mu = 0.016) allocation failure scavenge might not succeed


<--- JS stacktrace --->

FATAL ERROR: MarkCompactCollector: young object promotion failed Allocation failed - JavaScript heap out of memory
 1: 0xa222f0 node::Abort() [spack]
 2: 0x96411f node::FatalError(char const*, char const*) [spack]
 3: 0xb97f1e v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [spack]
 4: 0xb98297 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [spack]
 5: 0xd52fd5  [spack]
 6: 0xd83bbe v8::internal::EvacuateNewSpaceVisitor::Visit(v8::internal::HeapObject, int) [spack]
 7: 0xd8fbf6 v8::internal::FullEvacuator::RawEvacuatePage(v8::internal::MemoryChunk*, long*) [spack]
 8: 0xd7bd8f v8::internal::Evacuator::EvacuatePage(v8::internal::MemoryChunk*) [spack]
 9: 0xd7c008 v8::internal::PageEvacuationTask::RunInParallel(v8::internal::ItemParallelJob::Task::Runner) [spack]
10: 0xd6e8e9 v8::internal::ItemParallelJob::Run() [spack]
11: 0xd91b50 void v8::internal::MarkCompactCollectorBase::CreateAndExecuteEvacuationTasks<v8::internal::FullEvacuator, v8::internal::MarkCompactCollector>(v8::internal::MarkCompactCollector*, v8::internal::ItemParallelJob*, v8::internal::MigrationObserver*, long) [spack]
12: 0xd923ec v8::internal::MarkCompactCollector::EvacuatePagesInParallel() [spack]
13: 0xd925b5 v8::internal::MarkCompactCollector::Evacuate() [spack]
14: 0xda45b1 v8::internal::MarkCompactCollector::CollectGarbage() [spack]
15: 0xd60878 v8::internal::Heap::MarkCompact() [spack]
16: 0xd62368 v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [spack]
17: 0xd657ac v8::internal::Heap::AllocateRawWithRetryOrFailSlowPath(int, v8::internal::AllocationType, v8::internal::AllocationOrigin, v8::internal::AllocationAlignment) [spack]
18: 0xd2af4d v8::internal::Factory::AllocateRaw(int, v8::internal::AllocationType, v8::internal::AllocationAlignment) [spack]
19: 0xd24dd4 v8::internal::FactoryBase<v8::internal::Factory>::AllocateRawWithImmortalMap(int, v8::internal::AllocationType, v8::internal::Map, v8::internal::AllocationAlignment) [spack]
20: 0xd25873 v8::internal::FactoryBase<v8::internal::Factory>::NewByteArray(int, v8::internal::AllocationType) [spack]
21: 0xcb3e3a v8::internal::TranslationBuffer::CreateByteArray(v8::internal::Factory*) [spack]
22: 0x1a22be7 v8::internal::compiler::CodeGenerator::GenerateDeoptimizationData() [spack]
23: 0x1a232d7 v8::internal::compiler::CodeGenerator::FinalizeCode() [spack]
24: 0x1ab4c3b v8::internal::compiler::PipelineImpl::FinalizeCode(bool) [spack]
25: 0x1ab5d07 v8::internal::compiler::PipelineCompilationJob::FinalizeJobImpl(v8::internal::Isolate*) [spack]
26: 0xc613c0 v8::internal::OptimizedCompilationJob::FinalizeJob(v8::internal::Isolate*) [spack]
27: 0xc69ceb v8::internal::Compiler::FinalizeOptimizedCompilationJob(v8::internal::OptimizedCompilationJob*, v8::internal::Isolate*) [spack]
28: 0xc8b8dd v8::internal::OptimizingCompileDispatcher::InstallOptimizedFunctions() [spack]
29: 0xd0a6cf v8::internal::StackGuard::HandleInterrupts() [spack]
30: 0x107c003 v8::internal::Runtime_StackGuard(int, unsigned long*, v8::internal::Isolate*) [spack]
31: 0x1423279  [spack]
[1]    4419 abort      npx spack
```
