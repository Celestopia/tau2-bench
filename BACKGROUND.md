# Background

This project is a personal fork of [Sierra's tau2-bench](https://github.com/sierra-research/tau2-bench), a framework for evaluating conversational customer-service agents through simulated users, domain tools, and task outcomes. The inherited checkout includes the voice and knowledge features described as τ³-bench in its README.

## Purpose and current scope

- The primary purpose of this fork is to learn the benchmark's detailed mechanisms.
- Focus on the airline domain and text mode. Voice is out of scope.
- The benchmark will also be used for personal agent experiments, but those experiments will take place outside this repository.

## Personal development workflow

- Make personal adaptations and use Git to preserve their version history.
- Keep development independent of the original repository; upstream contributions and pull requests are not part of the current goal. Do not disturb the original repository.
- The `origin` remote points to the personal fork, [Celestopia/tau2-bench](https://github.com/Celestopia/tau2-bench). Pushing to `origin` updates that fork only.
- Retain the existing Git history. Personal changes may be committed directly on `main`; feature branches are optional.
- An `upstream` remote and ongoing synchronization are unnecessary.
- Keep credentials and generated experiment outputs out of commits, and use the existing tests and formatting conventions when adapting code.

This file is local background context and should remain uncommitted.
