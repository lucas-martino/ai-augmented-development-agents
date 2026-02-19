---
name: clean-code
description: Pragmatic coding standards - Clean Code + SOLID - concise, direct, no over-engineering, no unnecessary comments
allowed-tools: Read, Write, Edit
priority: CRITICAL
version: 1.0
---

# Clean Code - Pragmatic AI Coding Standards

> **CRITICAL SKILL** - Be **concise, direct, and solution-focused**.

---

## Core Principles

| Principle | Rule |
|-----------|------|
| **SRP** | Single Responsibility Principle: A class/function should have only one reason to change |
| **OCP** | Open/Closed Principle: Open for extension, but closed for modification. Use polymorphism instead of long switch/case blocks |
| **LSP** | Liskov Substitution Principle: Subclasses must be substitutable for their base classes without breaking the application |
| **ISP** | Interface Segregation Principle: Create small, specific interfaces. Clients should not be forced to depend on methods they do not use |
| **DIP** | Dependency Inversion Principle: Depend on abstractions, not on concrete implementations. Use Dependency Injection (e.g., via constructor) |
| **DRY** | Don't Repeat Yourself: extract duplicates, reuse |
| **KISS** | Keep It Simple: simplest solution that works |
| **YAGNI** | You Aren't Gonna Need It: don't build unused features |
| **Boy Scout** | Leave code cleaner than you found it |

---

## Naming Rules

| Element | Convention |
|---------|------------|
| **Variables** | Reveal intent: `userCount` not `n` |
| **Booleans** | Question form: `isActive`, `hasPermission`, `canEdit` |
| **Collections** | Use plural or explicit suffix: `users` or `userList` |
| **Functions** | Verb + noun: `getUserById()` not `user()` |
| **Classes** | Noun or Noun Phrase: `Customer`, `WikiPage` |
| **Constants** | SCREAMING_SNAKE: `MAX_RETRY_COUNT` |

> **Rule:** If you need a comment to explain a name, rename it.

---

## Function Rules

| Rule | Description |
|------|-------------|
| **Small** | Max 25 lines, ideally 5-10 |
| **Line Width** | Max 150 characters per line |
| **One Thing** | Does one thing, does it well |
| **One Level** | One level of abstraction per function |
| **Few Args** | Max 3 arguments, prefer 0-2 | 
| **No Side Effects** | Don't mutate inputs unexpectedly |
| **CQS** | Command-Query Separation: A function changes state OR returns info. Never both. |

---

## Error Handling

| Rule | Description |
|------|-------------|
| **Exceptions > Codes** | Use Exceptions instead of returning error codes (e.g., `-1`, `false`) |
| **No Null** | NEVER pass `null` as an argument, NEVER return `null`. Use `Optional` or Null Object Pattern |
| **Try-Catch Isolation** | If a function has a try-catch, it should contain nothing else (separate error processing from logic) |
| **Unchecked Exceptions** | Prefer unchecked exceptions (standard runtime errors) over checked exceptions to avoid clutter |

---

## Testing Standards

| Rule | Description |
|------|-------------|
| **F.I.R.S.T.** | Fast, Independent, Repeatable, Self-Validating, Timely |
| **One Assert** | Minimize assertions per test. Test one concept per test function |
| **Readable** | Test code must be as clean as production code |
| **Coverage** | Do not consider task complete without verifying happy path AND edge cases |
| **Behavior** | Test behavior, not implementation details |

---

## Class & Object Design

| Principle | Rule |
|-----------|------|
| **Step-Down Rule** | Public methods at the top, private methods below them. Code should read like a newspaper article |
| **Law of Demeter** | Don't talk to strangers. `a.getB().getC().do()` is bad. Only talk to immediate friends |
| **Cohesion** | Classes should have a small number of instance variables. Methods should use those variables |
| **Boundaries** | Wrap 3rd-party code/generics (e.g., Map) in your own classes. Don't leak external APIs. |
| **Data vs Obj** | Objects expose behavior/hide data. Data Structures expose data/have no behavior. Don't mix them (Hybrids) |

---

## Code Structure

| Pattern | Apply |
|---------|-------|
| **Guard Clauses** | Early returns for edge cases |
| **Flat > Nested** | Avoid deep nesting (max 2 levels) |
| **Composition** | Small functions composed together |
| **Colocation** | Keep related code close |

---

## Anti-Patterns (DON'T)

| Pattern | Fix |
|-------- |-----|
| Comment every line | Delete obvious comments |
| Helper for one-liner | Inline the code |
| Factory for 2 objects | Direct instantiation |
| utils.ts with 1 function | Put code where used |
| "First we import..." | Just write code |
| Deep nesting | Guard clauses |
| Magic Numbers | Named constants: if (status == 2)? Replace 2 with STATUS.READY |
| Selector Args | render(true)? Stop. Make renderPage() and renderSnippet() |
| God functions | Split by responsibility |
| Feature Envy | Method relies too much on another class? Move it there |
| Dead Code | Commented-out code? Delete it immediately (Git handles history) |
| Inconsistent Level | Don't mix high-level logic with low-level I/O in the same function |

---

## AI Coding Style

| Situation | Action |
|-----------|--------|
| User asks for feature | Write it directly |
| User reports bug | Fix it, don't explain |
| No clear requirement | Ask, don't assume |

---

## Before Editing ANY File (THINK FIRST!)

**Before changing a file, ask yourself:**

| Question | Why |
|----------|-----|
| **What imports this file?** | They might break |
| **What does this file import?** | Interface changes |
| **What tests cover this?** | Tests might fail |
| **Is this a shared component?** | Multiple places affected |

**Quick Check:**
```
File to edit: UserService.ts (or .cs/.py)
└── Who imports this? → UserController, AuthController
└── Do they need changes too? → Check function signatures
```

> **Rule:** Edit the file + all dependent files in the SAME task.
> **Never leave broken imports or missing updates.**

---

## Summary

| Do | Don't |
|----|-------|
| Write code directly | Write tutorials |
| Let code self-document | Add obvious comments |
| Fix bugs immediately | Explain the fix first |
| Inline small things | Create unnecessary files |
| Name things clearly | Use abbreviations |
| Keep functions small | Write 100+ line functions |

> **Remember: The user wants working code, not a programming lesson.**

---

## Self-Check Before Completing (MANDATORY)

**Before saying "task complete", verify:**

| Check | Question |
|-------|----------|
| **Goal met?** | Did I do exactly what user asked? |
| **Files edited?** | Did I modify all necessary files? |
| **Code works?** | Did I test/verify the change? |
| **No errors?** | Build/Compile succeeds? No syntax or linter errors? |
| **Nothing forgotten?** | Any edge cases missed? |

> **Rule:** Always READ output → If ANY check fails, fix it before completing.