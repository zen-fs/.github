# Contributing

This document covers how you can contribute to ZenFS and how to work with the project's tooling.

## Discussions, issues and pull requests

When opening an issue or discussion, write a short descriptive name for the title. For example, putting the first line of an error stack trace is _not_ a descriptive title. You do not need to triage the issue or PR, a maintainer will give it the applicable tags.

Please copy logs, terminal output, and code into a code block in the issue or PR. Do not include screenshots since those are very difficult to debug.

### Issues vs Discussions

Issues are used to track bugs and features. For anything else you probably want to open a discussion.

### Bug Reports

When submitting a bug report, you must submit a [Minimal reproducible example](https://en.wikipedia.org/wiki/Minimal_reproducible_example) that does not depend on third party code. Failing to provide one may lead to delays in resolving the issue or outright closure.

### LLM Policy

LLM-generated contributions are permitted under the following rules:

1. You must disclose _which model_ you used.
    - Contributions that do not disclose AI usage will be closed without review
    - In an issue, you might include a short sentence like "Generated with `claude-opus-5`".
    - For PRs, a `Co-Authored-By` with the model information is sufficient (and will be added to the squash commit if you don't include it). Always include the model ID, like `claude-opus-5` or `gpt-5.6-sol`.
2. You, the human, are responsible for what the LLM outputs.
3. Often times, LLM-generated contributions include exceedingly long descriptions and such that do not add value. While including details and context is important, a 2000 word issue or PR description is probably not needed.
    1. LLMs may include information like "all tests passing", "format clean", etc. This is completely useless since PRs run through CI/CD workflows that check that stuff. Do not include this kind of information in your PR.
    2. In issues, LLMs may over-explain things like the minimal reproduction. Please don't include this since it doesn't help maintainers.
4. Agent-driven contributions, especially bulk ones are not allowed. Maintainers can already use LLMs, acting as a proxy is worse than us using the tools ourselves.
5. In PRs, avoid massive rewrites and rebases when making additional changes. For example, if a maintainer asks for a small change there is no reason to rebase the whole PR.

In general: use common sense and respect maintainers' time.

## Code Style

#### Nesting

- Avoid [callback hell](http://callbackhell.com/)— this is why ZenFS uses `async`/`await` a lot.
- Use [guard clauses](<https://en.wikipedia.org/wiki/Guard_(computer_science)>) to reduce indentation
- If you're more of a visual learner, this video is helpful: [Why You Shouldn't Nest Your Code](https://youtu.be/CFRhGnuXG-4)

#### Naming things

- Don't use single letter variable names, with the exception of traditions like `i` in `for` loops
- Don't abbreviate in variable names
- Don't put types in variable names, it already has a type
- Don't put units in your variable names, but do include units in documentation if the type does not abstract the unit
    - Example #1: A variable `time: Date` doesn't need a unit because `Date` encapsulates units
    - Example #2: A variable `time: number` will need a unit in documentation, since it could be seconds, minutes, etc.
- Don't put types in types, for example prefixing an interface name with "I"
- Don't name a class "Base" or "Abstract"

The [Naming Things in Code](https://youtu.be/-J3wNP6u5YU) video covers everything, though you should keep in mind:

- Units will go into documentation if they are needed
- Bend the utils recommendation since some code can't be attributed to some other piece of code, it really is just a utility.

#### Comments

- A self-explanatory type or symbol name needs no doc comment.
- Inline comments should _never_ describe what the code is doing; that should be self-explanatory.
- When a comment is required, write one short sentence describing behavior.
- **Never** include history, rationale, or examples.
- Don't add interface-level JSDoc that just restates the interface name.

## NPM vs 3rd party package managers

ZenFS uses `npm` rather than `pnpm` or `yarn` since it makes it easier for new contributors and simplifies tooling.

## Building

You can build the project with `npm run build` or simply `npx tsc`.
Run watch mode with `npm run dev`.
ZenFS builds using `tsc` to keep tooling simple.

## Formatting

You can automatically run formatting with the `npm run format` command

Tabs are used in formatting since they take up less space in files, in addition to making it easier to work with.
You can't accidentally click the wrong space then have to move around trying to delete the single tab width of indentation.

Trailing commas are used to reduce the amount of individual line changes in commits, which helps to improve clarity and commit diffs. For example:

```diff
const someObject = {
	a: 1,
	b: 2,
+	c: 3,
}

```

instead of

```diff
const someObject = {
	a: 1,
-	b: 2
+	b: 2,
+	c: 3
}

```

ZenFS' styling is aimed at improving developer experience.
If you make changes to formatting, make sure they improve the development experience.

## Tests

You can run tests with the `npm test` command.

Tests are located in the `tests` directory. They are written in Typescript to catch type errors, and test step-by-step using Node's native testing.
Suite names are generally focused around a set of features (directories, links, permissions, etc.) rather than specific functions or classes.

Tests are run using the `zenfs-test` command, which comes from @zenfs/core's `scripts/test.js`.
This makes it as easy as possible to change the configuration used for tests.
Run `npx zenfs-test --help` to see all the options.

@zenfs/core's `common.ts` provides the framework used for testing.
It copies files from `tests/data` to the virtual file system.
These files probably aren't needed on their own, and could be generated at test runtime, though they work fine at the time of writing.
I think the time spent making those changes could be better spent on actual features.
`common.ts` also exports an `fs` module used by all the tests.
