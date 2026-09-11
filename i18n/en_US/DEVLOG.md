# Development Notes

Developing a workshop for the team-based programming track inevitably involves plenty of problems. This is a place to talk about them and keep a record.

The workshop's original developer started these notes. Later developers who have something to share should append it. At the beginning of each section, identify yourself or record the date if useful. The original developer chose the name “Developer A”; future contributors can use the remaining letters from B through Z, then AA through ZZ, and so on.

## Table of Contents

[TOC]

## Choosing the Topic

> In this section, “the author” means the workshop's original developer, referred to below as “Developer A.” It was written during the initial development and records the considerations behind the project.

The first challenge in developing a workshop for the team-based programming track was deciding on its subject. This proved surprisingly difficult.

### Goals for the Topic

I wanted the topic to meet three requirements:

+ **R1:** The project had to be small enough for students with little more than C89 and C++98 experience to finish during their spare time in a single week. An enthusiastic student should even be able to finish each day's work that same day.
+ **R2:** It had to cover as many important concepts from the team-based programming track as possible, so students would practice what the Summer Training lectures taught.
+ **R3:** It had to resemble real development closely enough that, if a comparable requirement arose in practice, our solution might not be optimal but would at least roughly follow acceptable engineering conventions. This was essential to avoid misleading students, and it troubled me for a long time, as later sections explain.

### Reducing the Scope and Reframing the Problem

The greatest difficulty came from the breadth of the track. Summer Training covers C\# syntax, object-oriented programming, multithreading, asynchronous programming, gRPC, Avalonia, Unity, modern C++, and more. It is impossible to fit all of that into a small project that students with little more than C89 and C++98 experience can complete in one week of spare time—or one daily assignment at a time. A project involving everything, including network and cross-language communication, cannot remain small. A genuinely small project cannot include everything. The scope therefore had to be reduced.

Modern C++ was the first subject to go. Most of the remaining material can be taught through a single C\# stack; adding a C++ chapter would create trouble for little benefit. For well-known reasons, configuring an engineering-quality C++ environment is itself time-consuming and would violate **R1**. Students can learn that environment through a separate small assignment or during future team-contest development. If a C++ chapter ever becomes necessary, it would also be easy to add after the topic is settled: include a C++ command-line client alongside the Avalonia client in the communication chapter. Much like [Kubernetes](https://kubernetes.io/) has `kubectl` and APIs in many languages, such a client could be written in any language.

Avalonia practice was necessary, but it did not need to constrain the topic. Whatever kind of software we chose, we could always require a GUI client for it. Avalonia could be adapted after selecting the subject. Unity was similar: in principle, the software only needed a lively, entertaining, playable client.

C\# syntax required no special consideration; choosing C\# as the implementation language guaranteed practice. gRPC provides async APIs, so committing to gRPC naturally supplies asynchronous-programming practice. Object-oriented programming was also comparatively easy to incorporate. [Object-oriented design patterns](https://zh.wikipedia.org/wiki/设计模式_(计算机)) capture well-established experience. Although they can be abused, they remain useful when selected because they genuinely fit rather than merely for their own sake. Almost any application can use a suitable architecture to exercise object-oriented thinking. The problem was therefore reduced to finding a topic that could naturally accommodate both multithreading and gRPC.

Those two requirements alone were enough to give me a headache.

### Topics Considered and Rejected

Why was it so difficult? Begin with multithreading. In Summer Training, we want students to encounter low-level thread synchronization: managing threads and using mutexes, condition variables or monitors, and semaphores for synchronization and mutual exclusion, as well as problems such as producer-consumer. It is difficult to construct a plausible scenario for that material. Parallel computation is one of the most common uses of multithreading, but high-performance parallel libraries already solve it. Manually managing threads, synchronization, and mutual exclusion for such work is usually foolish and directly violates **R3**. Even though manual thread management is now uncommon, I still wanted a scenario that did not look completely unreasonable.

Someone might suggest a simple game: let each thread control one character. That sounds plausible but is easily dismissed, because almost no game engine works that way. Games usually control frame rate on a central thread and update every entity during each frame. Threads may contribute to specific calculations, but that is again a parallel-computing problem. Using a game this way would violate **R3**.

Why not move a thread-synchronization scenario from the actual team contest directly into the workshop? Current team-contest development uses synchronization mainly in these areas:

+ **Communication.** The server uses thread synchronization to handle gRPC requests, but multithreading is taught before communication and cannot depend on gRPC. The client also updates game state when information arrives from the server. It needs mutual exclusion to prevent races with contestant code, but this is again coupled to gRPC, and handling its many races would be far too large and complicated for a small project, seriously violating **R1**.
+ **The downloader.** It might download small files in parallel, but students would need to rent cloud storage, which is clearly unsuitable.
+ **The game engine.** As of spring 2026, the team track's game engine derives from the basic design that this workshop's original developer—also the author of this paragraph—created by extensively refactoring and modifying [THUAI3.0](https://github.com/eesast/THUAI3.0) for [THUAI4](https://github.com/eesast/THUAI4), then extended with state transitions in [THUAI6](https://github.com/eesast/THUAI6). The first two versions contain major design mistakes. THUAI3.0 created a `Timer` for every game entity; THUAI4 went further off the rails and created a `Thread` for every entity. THUAI9's developers apparently changed that to one `Task` per entity, which is an even bigger mistake. As noted above, a game should update by frame. The inexperienced person I was at the time wrote one thread per entity instead, making it a dangerously misleading example. I recognized the problem years ago, but the engine was already complete and I did not dare change it hastily and risk disrupting the contest schedule. I have wanted to drive a refactoring ever since, but for various reasons it has never happened.

After thinking without success, I held conversation after conversation with large language models such as [ChatGPT](https://chatgpt.com/) and [Google Gemini](https://gemini.google.com/). Every proposal had one or more of the problems above. One particularly abstract suggestion was a task-processing system: submit tasks at arbitrary times and process concurrent work. I rejected it because it was too abstract and had no sufficiently realistic requirement. It amounted to shooting an arrow and drawing the target around it afterward—a problem invented to fit the concepts, without the feeling of solving a need from the real world.

I then considered a simulator of everyday life, another idea proposed by models: perhaps a restaurant simulator or printer simulator, with threads representing incoming orders or print jobs. But does anyone actually need such a simulator? What purpose would it serve? If it is a game, why would it not update by frame like a game engine? I rejected this idea as well.

The first ray of hope came from parallel compilation. Like `GNU Make`, compilation can be divided into tasks that process different files in parallel. This was my first nearly viable topic, but it raised many more problems. Could a student with only C89 and C++98 fundamentals really understand all the necessary compiler concepts—lexical analysis, parsing, and so on—and implement them in so little time? If the framework supplied an entire compiler and students only called it, their part would become too small; they would write almost nothing, undermining the learning goals. How could parallel compilation plausibly involve gRPC? Local compilation needs no network; compilers normally run locally. A remote compiler like [Compiler Explorer](https://gcc.godbolt.org/) would be far too much work: its GUI would need to support multiple files and transmit them to the server for parallel compilation. Compilation does not demand GPU-scale remote computing, and a C\# GUI requires a local .NET environment and application installation. Unlike the web, it is not ready to use from a browser. At that point, why not simply install a local compiler written in C\#? The requirement felt absurd.

Perhaps, I thought, the realism goal could be relaxed slightly. A JSON parser could avoid the complexity of compiler theory: build a parallel JSON parser instead. The requirement would be even stranger, but by then I was running out of options. JSON is much simpler than a programming language. If the framework provided a complete lexer and a recursive-descent parsing skeleton, a few short explanations of LL(1) parsing would let students fill in the missing pieces.

### Settling on the Final Topic

I thought the JSON parser might be the answer, but it still felt far too contrived to present with a straight face. It was frankly ridiculous.

The breakthrough came on the evening of June 3, 2026, when a peculiar thought flashed through my mind: if we were going to parse JSON, why not parse application logs, which actually matters? Early in 2026, I had seen the problem from the [2025 International AIOps Challenge](https://competition.aiops.cn/). Given logs from a cloud-native microservice application, contestants had to reconstruct the microservice call chain, then detect and locate anomalies. Log-based analysis and anomaly detection is also a research area in computer networking. Could the workshop extract the simplest first step—application-log preprocessing—and simplify it drastically?

The logic fit perfectly. Applications produce multiple log files, and reading and parsing them in parallel is common. Reading consumes I/O resources, but parsing consumes CPU time, so using multiple threads to accelerate CPU-bound parsing is meaningful.

Introducing gRPC also felt natural. Clusters that host cloud services are remote, so controlling log analysis from a local client—and perhaps visualizing the result—is a plausible requirement. Team-contest applications already need Avalonia interfaces to display information such as file types, logs, character actions, and game events. An assignment that presents log content would therefore prepare students for future team-contest development.

That is how the cloud-service log processing system became the workshop's topic.

## Why Unity Was Not Included

> Author: Developer A; recorded during initial development.

The original version did not include Unity. I do not know whether someone will add it later, but the reasoning belongs in these notes.

The first reason is simple: I am not very familiar with Unity. I am not a complete beginner, but I do not know it well enough to design an appropriately challenging assignment. Better to leave that to someone else.

Second, the cloud-service log processing system is a tool, not a game. What should a game engine do with it? I did consider using Unity to visualize the relationships among microservices or nodes: represent each microservice as a small house, then let a character walk among the houses and open their doors. Opening a door would reveal the log files inside the represented service or node. But apart from my lack of Unity experience, the idea still felt odd. Unity would probably be better used to make a game.

Third, Unity projects are difficult to manage on GitHub. Even if one were merged into the workshop, submitting it like the other chapters would be difficult. The two parts are naturally separate, so there is little reason to force them into one workshop.

And that is that.

## File I/O in the Development Environment

> Author: Developer A.

Tests for the log-file reader exposed a problem.

When Visual Studio starts an application, the working directory is the directory containing the executable, which should be excluded by `.gitignore`. A dataset tracked by Git should not live there. Relative paths therefore create a conflict: should we repeatedly copy the dataset by hand, or add several levels of `../../../` to every test path? The former is tedious; the latter is worse. It severely harms portability and makes identical code behave differently in development and production, obstructing later work.

Fortunately, MSBuild has a solution. Add this to an `<ItemGroup>` in the project file (`.csproj`):

```xml
<None Include="..\dataset\*.log">
  <Link>dataset\%(Filename)%(Extension)</Link>
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</None>
```

During the build, MSBuild automatically copies the dataset to the destination. `Include` is the dataset path relative to the project root—the directory containing the `.csproj` file—while `<Link>` is the destination path relative to the executable directory.

Problem solved.

## Avalonia UI on the Web and Mobile

> Author: Developer A.

Avalonia UI is cross-platform, but I removed Android and iOS requirements from the basic chapters. Adapting an interface to mobile is tedious because every control must be rearranged, and Android also involves certificate trust and related setup. Neither concern is central to this project.

To run the web target, install the .NET WebAssembly tools:

```bash
dotnet workload install wasm-tools
```

When debugging with Visual Studio, install the .NET 10 WebAssembly Tools through Visual Studio Installer.

## Additional Difficulties in Building a WebAssembly Client

> Author: Developer A.

I happened to notice that Avalonia supports desktop clients (Windows, Linux, and macOS), Android, iOS, and WebAssembly in the browser. Android involves substantial signing work, and I do not own an iOS system, so I tried to support Desktop and WebAssembly together through the `LogAnalyzerClient.Browser` project. I did not expect the enormous difficulty that followed.

The first problem is that gRPC-Web differs from native gRPC. Before connecting, gRPC-Web sends an HTTP/1.1 CORS preflight `OPTIONS` request from the browser; native gRPC does not. If the Agent enables only HTTP/2, gRPC-Web cannot connect. If it enables both HTTP/1.1 and HTTP/2, the native gRPC client cannot connect:

```csharp
builder.WebHost.ConfigureKestrel(options =>
{
    options.ConfigureEndpointDefaults(listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http2; // gRPC-Web needs HttpProtocols.Http1AndHttp2
    });
});
```

Consequently, one port cannot accept both native desktop and browser clients. Two ports are required, one for each. That would create needless confusion for workshop students, so I decided not to include the browser platform. The `Browser` project remains for interested students to explore.

Browser support also requires CORS configuration, which belongs to the web track rather than the team-based programming track. As an aside, gRPC-Web support also requires these lines in the Agent:

```csharp
app.UseGrpcWeb();
app.MapGrpcService<AgentService>()
    .EnableGrpcWeb();
```

Two related difficulties consumed most of my time: Avalonia and `Grpc.AspNetCore` could not coexist in one project, and desktop and browser clients had to be created differently.

Initially, `LogAnalyzerRpc` referenced the `Grpc.AspNetCore` package. When Avalonia referenced that project, it inherited an indirect dependency on `Grpc.AspNetCore`, which is incompatible with Avalonia and caused the build to fail. After carefully separating the dependencies, `LogAnalyzerRpc` contained only `Google.Protobuf`, `Grpc.Tools`, and `Grpc.Core.Api`. Even `Grpc.Core` would not work; it absolutely had to be `.Api`. Unbelievable.

The next question was versioning: which versions would maximize compatibility with my chosen `Grpc.AspNetCore` 2.80.0? I inspected the source of [gRPC for .NET v2.80.0](https://github.com/grpc/grpc-dotnet/tree/f22747c72676120b2bc69a1ba7c8bed995e29b1b), found the versions in its [central package configuration](https://github.com/grpc/grpc-dotnet/blob/f22747c72676120b2bc69a1ba7c8bed995e29b1b/Directory.Packages.props#L35-L37), and referenced those packages:

```xml
<Project>
  <PropertyGroup>
    <GrpcDotNetPackageVersion>2.70.0</GrpcDotNetPackageVersion>
  </PropertyGroup>
  <ItemGroup>
    <PackageVersion Include="Grpc.Tools" Version="2.80.0" />
    <PackageVersion Include="Grpc.Core.Api" Version="$(GrpcDotNetPackageVersion)"/>
    <PackageVersion Include="Google.Protobuf" Version="3.31.1" />
  </ItemGroup>
</Project>
```

Desktop clients use `Grpc.Net.Client`, while browser clients additionally depend on `Grpc.Net.Client.Web`; their construction also differs:

```csharp
// Desktop
var channel = GrpcChannel.ForAddress(address);
var client = new LogAnalyzerAgentServiceClient(channel);

// Browser
var handler = new GrpcWebHandler(GrpcWebMode.GrpcWeb, new HttpClientHandler());
var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions()
    {
        HttpHandler = handler
    });
var client = new LogAnalyzerAgentServiceClient(channel);
```

I used the factory method pattern to resolve that difference.

Even after everything finally ran, some gRPC methods still failed when called, and I did not have the energy to investigate further. Plans for a working browser target are therefore completely shelved for now.

## Why the Workshop Includes Written Questions

> Author: Developer A.

If students already write code, why assign written questions? There are two main reasons.

The first is the use of large language models. Vibe coding is now extremely popular, and students can easily depend on it without thinking. Each section therefore ends with questions about AI use, encouraging students to assess the quality of model-generated work. Vibe coding is also too new for me to know its full effect on beginning programmers. The questions double as a small social survey of how large language models affect their learning.

The second reason concerns the starter code itself. It implements most input, output, and other infrastructure; the first chapter is especially complete, and students can finish it by writing relatively little code. This accommodates students who have only just encountered .NET. The amount of scaffolding decreases chapter by chapter as their experience grows. The early chapters also support everything that follows, so I wanted to minimize foundational mistakes or situations where students could not finish and were then unable to continue. The drawback is that merely completing a small missing portion does not prove that a student understood the framework. Some written questions therefore ask about its implementation and encourage students to read it carefully.
