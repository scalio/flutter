![Bazel at Scalio](assets/BazelAtScalioCrop.jpg)

# Overview

At Scalio, a primary goal for 2019 is to move 100% of our application builds over to Bazel - every language & platform. This work has already begun for many of the platforms we ship code for, and this document tracks the status of this effort here at Scalio, as well as serves as an example of our typical strategy when moving organizations over to Bazel.

# Playbook

We execute this process using a standard playbook both internally here on our projects and when engaged by clients to help with this effort.

The workflow consists of 3 stages, each of which consists of a few steps. The stages align to our level of support and adoption of Bazel for a given platform or language.

## Status Workflow

- 🔴 **Canary** - _proof of concept phase_
  - evaluate existing support for Bazel
  - initial creation of toolchains, build rules drafts
  - initial definition of knowledgebase and internal best practices
  - begin onboarding the platform team "early adopters"
- 💛 **Development** - _building out full Bazel support_
  - refine build rules
  - define boilerplate project workspaces (BUILD files) for projects on this platform
  - build all new projects on this platform with bazel
  - continue migration process
  - finalize best practices and knowledgebase
  - platform team fully onboarded
  - PRs to Bazel OSS repos with necessary changes to the tool itself, build rules, and docs
  - implement remote cache integration
  - implement rbe integration
- 💚 **Stable** - _builds with Bazel are fully supported and the norm for the platform_
  - 100% of projects building on Bazel
  - all current use cases supported

In practice, the Bazel rollout is tracked using Kanban and the above workflow, where each platform is a task, each item above is a checklist item, and each Status is a column.

![Kanban Example](assets/kanban.jpg)

# Bazel Support Status at Scalio

## 💚 Golang

All Golang applications are built with Bazel and the platform teams are fully onboarded and up to speed on our best practices.

### Issues

- [Missing $GOPATH with pure="on" enabled](https://github.com/bazelbuild/rules_go/issues/1987)

### Links and Resources

Scalio's contributions to Golang + Bazel will be publicly listed here soon.

## 💚 NodeJS (Nest)

New NestJS apps are built with Bazel, and the team is being onboarded rapidly.

### Issues

- [Building linux amd64 nodejs_image on Mac OS](https://github.com/bazelbuild/rules_nodejs/issues/396) | [related pull request](https://github.com/bazelbuild/rules_nodejs/pull/827)

### Links and Resources

- [NestJS starter repo](https://github.com/scalio/bazel-nestjs-starter)

## 💛 Angular 8

Angular builds on Bazel are being evaluated and the tooling implemented.

### Links and Resources

Scalio's contributions to Angular + Bazel will be publicly listed here soon.

## 💚 Docker

Docker and Bazel support is in use for all Golang apps and some NestJS apps. Rollout is ongoing to legacy applications.

### Links and Resources

Scalio's contributions to Docker + Bazel will be publicly listed here soon.

## 💛 Android

We've implemented Kotlin 1.3 support by custom rules. But we are blocked on [Android DataBinding support](https://github.com/bazelbuild/bazel/issues/2694).

### Links and Resources

- [Bazel-Android-Starter-Kotlin](https://github.com/scalio/Android-Bazel-Starter-Kotlin) - A Bazel & Android starter project running latest Kotlin (1.3.+) with coroutines support.
- [Bazel-Android-Starter-Java](https://github.com/scalio/Android-Bazel-Starter-Java) - A Bazel & Android starter project running Java.
- [Kotlin Build Rules](https://github.com/mig35/rules_kotlin) - our updated Kotlin build rules with full support for Kotlin 1.3, Coroutines, and Annotations.

## 🔴 iOS

Our iOS support is in canary phase as we continue to work on tooling around package managers (SPM, CocoaPods) etc.

While we got some samples sucessfully built with Bazel, we still met some pitfalls, which we need to workaround first to start ours projects migration to Bazel.

Now we are working on possible best practices for iOS development process with Bazel and formalizing our contribution suggestions.

### Links and Resources

[PodToBuild by Pinterest](https://github.com/pinterest/PodToBUILD) - A promising and useful tool for making use of CocoaPods within Bazel builds, however there's still room for improvement here, and SPM should be preferred wherever possible.
[XCHammer by Pinterest](https://github.com/pinterest/xchammer) - XCHammer generates Xcode projects from a Bazel Workspace.

## 💚 C++

Bazel is used for all new C++ projects, all meaningful legacy projects have been updated, and the team is onboarded.

C++ has great out of the box support in Bazel. 
We use C/C++ to share code between mobile platforms and command line tools. Bazel c++ rules crosscompilation was sucessfully tested to be integrated with JVM, Ios, MacOS X (swift) and Android platforms rules.

Previously we used Buck build system, but found no drawbacks while migration to Bazel and now can consider it as main build system for C/C++ code.

Also, we have few suggestions about cmake projects generation from Bazle rules and now working to formalize it.

### 💛 Djinni

As we use C/C++ libraries for cross platform development, we need good tooling for faster cross language communication prototyping.

And we have very positive experience with Djinni - cross-language type declarations and interface bindings code generation tool.

Bazel has proven support of all Djinni oriented platforms, so we want to consider creation of Bazel build rules for Djinni interface definition files.

While we intially met certain limitations, now we got it workaround and have working proof of concept build rules with the power of Bazel's  [repository_rules](https://docs.bazel.build/versions/master/skylark/repository_rules.html)

#### Links and Resources

[Djinni by dropbox](https://github.com/dropbox/djinni) - Djinni is a tool for generating cross-language type declarations and interface bindings

Scalio's contributions to C++ & Djinni + Bazel will be publicly listed here soon.

## 🔴 Rust

We got Rust Bazel rules tested with some sample projects, but we met problems with Cargo integration, which we need to resolve first to consider Bazel as main build system for ours Rust utilities.

More detailed problems descriptions and contribution suggestions are coming.

### Links and Resources

Scalio's contributions to Rust + Bazel will be publicly listed here soon.

## 💛 .NET Core

Scalio's .NET Core team is happy with the results we've seen with Bazel so far and full production-ready Bazel support is coming soon.

### Links and Resources

- [.NET Core starter repo](https://github.com/scalio/bazel-dotnetcore-starter)

## 💛 JVM

Java rules are supported out of the box in Bazel and solid Scala/Kotlin rules are also present there.

We got sample Java/Scala/Kotlin projects built with Bazel and now going to evaluate Bazel conformance for big JVM-based frameworks (like Spring, Spartk, etc.)

### Links and Resources

Scalio's contributions to JVM + Bazel will be publicly listed here soon.
