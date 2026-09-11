# EESAST Summer Training (.NET Track) Learning Project

## Introduction

This learning and training project was created for the .NET track (also known as the team-based programming track) of the [Tsinghua University Department of Electronic Engineering Student Association of Science and Technology](https://eesast.com) Summer Training program (referred to below as “Summer Training”). By building a simple application, students will learn the .NET technology stack and other technologies used during the following year's [Team Programming Contest](https://eesast.com/#/contest/list).

Summer Training is short, covers a broad range of topics, and assigns each participant responsibility for one portion of the material. Every instructor naturally wants to share as much as possible and help students become productive quickly. In the past, this encouraged a knowledge-dense teaching style that could feel like cramming or an impossibly fast speed-reading exercise. Technical subjects—especially programming—contain many scattered concepts. Even when instructors focus on the main thread, lessons can still turn into long lists of facts that frustrate students and do little to teach practical application.

To address this problem, [FranGuam](https://github.com/FranGuam) created [web-workshop](https://github.com/eesast/web-workshop) for the web track of Summer Training and greatly improved student engagement. Building on that success, the team-based programming track created this learning project for its own curriculum.

**Project website:** [https://eesast.github.io/dotnet-workshop/](https://eesast.github.io/dotnet-workshop/)  

**Reference solution:** [https://github.com/eesast/dotnet-workshop-solution](https://github.com/eesast/dotnet-workshop-solution) (not `public`; for instructors' internal use only)  
**Demo:** [https://eesast.github.io/dotnet-workshop/demo/](https://eesast.github.io/dotnet-workshop/demo/)

## What You Will Learn

In this project, you will implement a simple cloud-service log analysis system and learn the following topics:

1. **C\# and object-oriented programming.** .NET is an open-source software development framework and runtime developed by [Microsoft](https://www.microsoft.com/), and C\# is the preferred language for developing software on .NET. C\# and the .NET SDK also provide excellent support for object-oriented programming. This project introduces the basic .NET development environment, the fundamentals of C\# syntax, and several object-oriented programming concepts.
2. **Concurrent programming with multiple threads.** A process is the operating system's abstraction for time-sharing and its basic unit of resource allocation; a thread is the corresponding time-sharing abstraction within a process. Programs often need to run several tasks concurrently instead of one after another, which requires threads to cooperate. Although modern applications seldom work with threads directly—thread pools, asynchronous programming, parallel computing frameworks, and similar abstractions usually wrap them—understanding how threads work is still valuable. This project introduces multithreading and the synchronization and mutual-exclusion problems that arise in multithreaded programs.
3. **Asynchronous programming and communication.** Networked applications are ubiquitous, and communication between applications over a network is routine. Asynchronous programming is a model designed to reuse threads during network communication and to address problems such as callback hell. In this project, you will learn [gRPC](https://grpc.io/), Google's **remote procedure call (RPC)** framework, and use its asynchronous APIs to build a networked application.
4. **Avalonia UI.** [Avalonia UI](https://avaloniaui.net/) is an open-source, cross-platform framework for developing graphical user interfaces (GUIs). It is based on .NET and serves as a cross-platform counterpart to Microsoft's official GUI framework, [WPF](https://learn.microsoft.com/en-us/dotnet/desktop/wpf/overview/). In this project, you will use Avalonia UI to design a simple graphical interface.
5. **Unity.** Unity is a popular game engine that primarily uses C\# and is also part of the team-based programming track. Unity has not yet been included in this project because doing so is difficult, particularly because Unity projects are hard to host effectively on GitHub. For more about the reasons, see the Unity discussion in the [development log](./DEVLOG.md). Whether Unity can be added in the future is left to later contributors.

## Getting Started

All project documentation is in the `docs/` directory. Begin with the documents in `docs/00-prepare` to set up your environment, understand the background, and learn how to submit assignments. Then work through the numbered sections in order, reading each section's guide (`guidance.md`) and completing its tasks (`tasks.md`).

+ `00-prepare`
  + [Guidance](./docs/00-prepare/guidance.md)
  + [Tasks](./docs/00-prepare/tasks.md)
+ `01-basic`
  + [Guidance](./docs/01-basic/guidance.md)
  + [Tasks](./docs/01-basic/tasks.md)
+ `02-multithreading`
  + [Guidance](./docs/02-multithreading/guidance.md)
  + [Tasks](./docs/02-multithreading/tasks.md)
+ `03-async-grpc`
  + [Guidance](./docs/03-async-grpc/guidance.md)
  + [Tasks](./docs/03-async-grpc/tasks.md)
+ `04-avalonia`
  + [Guidance](./docs/04-avalonia/guidance.md)
  + [Tasks](./docs/04-avalonia/tasks.md)
+ `05-advanced`
  + [Guidance](./docs/05-advanced/guidance.md)
  + [Tasks](./docs/05-advanced/tasks.md)
+ `appendix`
  + [Appendix A: Glossary of Selected Terms](./docs/appendix/appendix-a-glossary.md)

### About Vibe Coding

As coding agents have advanced rapidly, state-of-the-art large language models can now complete most of this project with ease as of July 2026. Requiring students to complete every assignment manually would consume too much time and be difficult to supervise. Summer Training permits AI-assisted work, subject to the following restrictions:

- First use AI assistance to understand the project's overall architecture, then carefully read the code in the parts you consider important.
- Do not make wishes with a brief prompt. Write a sufficiently detailed prompt that clearly states the behavior and implementation you want. Discuss the task with the model over multiple turns to clarify the requirements and refine the prompt, ensuring that you fully understand the project's details.
- Every line of AI-generated code must be reviewed by a person. This is essential to understanding what you learn.

We believe students attend Summer Training to improve their development skills, not merely to finish assignments. By the end of the program, you should have the **taste** expected of a **developer**—the judgment that will guide you through the broad world of software development.

> In the age of AI, most simple requirements can be implemented quickly with AI. Real software systems, however, face complex business logic, changing requirements, teamwork, and long-term maintenance.
> A common example is an AI rapidly generating a feature module filled with unnecessary condition checks and exception-handling logic. The resulting code is verbose and difficult to understand—an instance of excessive defensive programming. If this is not reviewed and improved, the entire system will eventually become an unmaintainable mess.
> Good code taste is what enables developers to implement complex business requirements simply and efficiently while keeping code readable and extensible over the long term.

## Contributing

If you develop the project's code framework, read the [contribution guide](./CONTRIBUTING.md) carefully before making changes.

## Feedback

If you have questions or encounter problems with the software or the design of its learning curve, follow the guidance in [Issue #69](https://github.com/eesast/dotnet-workshop/issues/69) to send us feedback.

## Afterword

This project was inspired by [web-workshop](https://github.com/eesast/web-workshop), created by [FranGuam](https://github.com/FranGuam), and some of its task design draws on [MIT 6.5840: Spring 2023](http://nil.csail.mit.edu/6.5840/2023/index.html). We gratefully acknowledge both sources.

For reflections on the development process, behind-the-scenes stories, and other anecdotes, read the [development log](./DEVLOG.md).

## Licenses

All source code in this project is released under the [MIT License](../../LICENSE-code.txt) (`SPDX-License-Identifier: MIT`). All documentation is released under the [Creative Commons Attribution-ShareAlike 4.0 International License](../../LICENSE-document.txt) (`SPDX-License-Identifier: CC-BY-SA-4.0`).
