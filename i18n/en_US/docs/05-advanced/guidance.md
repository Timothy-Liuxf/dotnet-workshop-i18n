# Guidance for Advanced Functions

## Table of Contents

[TOC]

## Learning Objective

+ Exercise your overall software-engineering ability through imagination and independent exploration.

## Background

You have now completed some of the basic features of a cloud-service log analysis system. Until this point, however, you have followed a prescribed path and implemented code from specific requirements. That approach introduces the relevant technology stack, but it does not fully develop the broader skills required in software engineering.

This chapter therefore gives you room to use your imagination, explore, and learn independently. It is the centerpiece of the workshop.

## Tasks for This Section

This chapter has two parts: guided choices and open-ended work. Choose features that interest you from the guided choices, implement any additional feature you want in the open-ended part, and finish by writing a project overview.

**Note: Some extensions in this section may require you to break an earlier, deliberately simple interface. As a result, previous unit tests might fail or might not even compile. You are not required to pass the earlier unit tests in this section.** If the solution no longer compiles because of those tests, edit `dotnet-workshop.slnx` and remove the following unit-test projects from the solution:

```xml
<Solution>
  <Project Path="test-00-prepare/test-00-prepare.csproj" />
  <Project Path="test-01-basic/test-01-basic.csproj" />
  <Project Path="test-02-multithreading/test-02-multithreading.csproj" />
  <Project Path="test-03-async-grpc/test-03-async-grpc.csproj" />
  <Project Path="TestUtils/TestUtils.csproj" />
</Solution>
```

### (S5.1) Step 1: Guided Choices

From the functional and visual topics below, choose **at least one topic from each category** to implement—that is, at least one functional topic and at least one visual topic. You may complete more, but they do not earn extra points.

#### (T5.1.a) Functional Topics

Functional topics add substantial new capabilities to the system and require the frontend and backend to work together. You will implement the relevant logic in the Agent, possibly add RPCs, and build the corresponding user interface in the Client. Only the graphical client is required; you do not need to update `LocalCli` or `RemoteCli`.

##### (T5.1.a.a) Support the Parquet File Format (Medium)

[Parquet](https://parquet.apache.org/) is a columnar storage format widely used with cloud services for both user and operational data. Your goal is to add Parquet support to the cloud-service log analysis system. Study the [Parquet format documentation](https://parquet.apache.org/) independently and use a third-party .NET implementation—such as [Parquet.Net](https://www.nuget.org/packages/Parquet.Net), [ParquetSharp](https://www.nuget.org/packages/ParquetSharp), or another implementation from the [NuGet Gallery](https://www.nuget.org/)—to provide these features:

+ Read and analyze Parquet files. The current system supports only `.log` text files in a prescribed format. Design a Parquet schema of your own and support reading log files stored in that format.
+ Generate Parquet files. Store log-analysis results in a Parquet file, allowing the client to tell the Agent which logs to save and where to save the result.

##### (T5.1.a.b) Client Authentication (Hard)

The current system has several problems. First, it has no client authentication, so anyone can control the Agent and run log analysis—an obvious security risk. Second, if several people connect to an Agent at the same time, their operations can interfere with one another.

Implement a token-based mechanism. When the Agent starts, it generates a random string to use as a token. Every client operation must carry a valid token. Different tokens identify different users, and all operations performed by different users must be completely isolated from one another.

Define two permission levels: administrator and standard. A standard token can perform log-analysis operations. An administrator token has all standard permissions and can also create and delete other tokens and raise or lower their permissions. The token generated when the Agent starts must have administrator permissions and be emitted as a log through `_logger`.

Add a corresponding administrator window to the graphical client.

##### (T5.1.a.c) Sort and Query Logs (Medium / Hard)

At present, the application displays every log in an analysis result. In practice, users may also need to sort and query those logs. Add the following features:

+ Sort displayed logs by a selected key.
+ Query logs by criteria: log type (Call, Request, or Internal), time range, originating service (such as `gateway`), severity (such as only `Warning`), or Request ID.

Implement sorting in the graphical application. Provide log-query services in the Agent, and add controls to the graphical application for specifying query criteria and displaying the results.

You may decide the details and scope of your implementation.

##### (T5.1.a.d) Infer and Display Cloud-Service Topology (Medium / Hard)

A common observability requirement is to infer the call topology of cloud services from their logs when that topology is not already known. Your goal is to infer this cloud-service topology from the available logs.

Implement the following features:

+ In the Agent, use Call logs to build a graph of calls between cloud services, infer the topology, and record which Call logs correspond to each edge.
+ In the graphical client, add a cloud-service topology view. When the user selects a file, provide a button or context-menu item that retrieves topology data from the Agent and visualizes it as a graph. Treat each cloud service as a node and each call relationship as a directed edge. Use a simple frontend layout algorithm to keep the graph legible. The user must also be able to select an edge and retrieve its corresponding logs from the Agent.

#### (T5.1.b) Visual Topics

##### (T5.1.b.a) Improve the Log Display (Medium)

The client currently displays logs in a list box. This is not especially readable because every log is presented as a single string.

Improve the display as follows:

+ Display logs in a table, with each key in its own column. Fields from the log's Message portion—such as Method, Path, Exception Name, and Target Service—must also have their own columns.
+ Highlight different log levels. Severity should be immediately recognizable because warnings and errors require special attention during diagnosis. Give the Severity cell a different background according to its level. You may fill the entire cell or put the text inside an ellipse, rounded rectangle, or similar shape. Use blue for Info, orange for Warning, and red for Error.

##### (T5.1.b.b) Make the Control Layout Responsive (Medium)

The same arrangement of controls does not work equally well on displays with different aspect ratios. Many applications and websites therefore adapt their layouts to the user's display; the difference between mobile and desktop devices is large enough that “mobile adaptation” is often a task of its own. For example, Windows Settings uses clearly different layouts at different window widths:

![Windows Settings at wide and narrow widths](./assets/win-setting-wide.png)

The left side shows the wide-window layout, while the right side shows the narrow-window layout.

Implement this behavior by using different layouts at different window widths so the application remains usable in a narrow window. For example, you could retain the existing layout at widths of 640 or more and use a new compact layout below 640.

##### (T5.1.b.c) Improve the Styling and Add Themes (Medium / Hard)

The control styles currently defined in `Styles/Controls.axaml` in the `LogAnalyzerClient` project are very basic. Improve their appearance and add support for multiple themes.

+ Polish the graphical interface with color, pressed states, outlines, gradients, and other visual details. You may also use a third-party control library such as [Material.Avalonia](https://github.com/AvaloniaCommunity/Material.Avalonia), [FluentTheme](https://docs.avaloniaui.net/docs/styling/themes#fluent), one of the [other listed third-party libraries](https://docs.avaloniaui.net/docs/styling/themes), or another library you find.
+ Support switching among multiple themes from the menu bar. Possible themes include light, dark, high contrast, sky, ocean, and starry night, though you may design any themes you like. Support at least three themes.

> [!NOTE]
>
> **Task 5.1 (T5.1)**
>
> Choose appropriate topics to complete. Implement at least one functional topic and at least one visual topic.
>

### (S5.2) Step 2: Open-Ended Work

In this step, design and implement a feature of your own. It may be an original idea, an unimplemented feature from the guided choices, or an enhancement to a feature you completed there.

The feature does not need to be exceptionally difficult. A scope comparable to a Medium assignment from the basic portion of the workshop is sufficient.

> [!NOTE]
>
> **Task 5.2 (T5.2)**
>
> Independently explore and complete one feature.

### (S5.3) Step 3: Project Overview

After completing the first two steps, write a project overview that covers:

+ How to build and run the program.
+ Which features you implemented and how to use each one. Include screenshots where helpful.
+ To what extent you used AI during implementation. If you did, which AI did you use, what prompts did you provide, and how did it make the work easier?
+ Any other experience or insights you gained during development.

> [!NOTE]
>
> **Task 5.3 (T5.3)**
>
> After completing your implementation, write the project overview in the `docs/05-advanced/report.md` text file.

> [!IMPORTANT]
>
> **Use of Large Language Models**
>
> Large language models offer many conveniences, and learning to use them will be an important skill. However, text copied wholesale from a model without thought is easy to recognize: it is vague, inflated, needlessly ornate, short on substance, and difficult to read. Make your project overview genuinely readable instead of relying on a model to pile up polished-sounding phrases without identifying what matters. Using a model thoughtfully to improve the quality of your writing is, of course, encouraged. The ability to assess and revise writing—whether produced by a person or by a model—is itself an important skill.

For task point values and related details, see [tasks.md](./tasks.md).

## Conclusion

This brings your development journey to a temporary close. We sincerely hope you learned something valuable along the way.

We also hope that when you encounter a similar requirement in future development work and do not know how to implement it or call the relevant API, you will return to see how this workshop approached the problem. That is one of the workshop's goals.

Finally, we hope you come to love code. Happy developing!

## Further Reading

None yet; to be added.

## Previous / Next

+ Previous: [Tasks in Avalonia](../04-avalonia/tasks.md)
+ Next: [Tasks in Advanced Functions](./tasks.md)
