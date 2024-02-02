# Clean Code

**Book citation:** Martin, R. C. (2010). Clean code: a handbook of agile software craftmanship. Upper Saddle River [Etc.] Prentice Hall.

## Chapter 1: Clean Code

Bad code impedes programmers from doing a good job. The messier the code, the lower the productivity of the team. Management tends to add more staff but that's not the solution (new staff members are not familiar with the codebase).

Programmers know the technical details of what's being built so they should be helping other roles make informed decisions, instead of just submitting to the manager's deadlines. Keeping code clean at all times is the only way to meet deadlines.

### What is clean code?

- **Bjarne Stroustrup:** clean code is focused and efficient, and does one thing well. Bad code is ambiguous.
- **Grady Booch:** code should be readable and contain only what is necessary.
- **"Big" Dave Thomas:** clean code allows to be enhanced by other people. It is minimal and has tests.
- **Michael Feathers:** clean code is made by someone who cares.
- **Ron Jeffries:** clean code has meaningful names and no duplication (one object or method should not do more than one thing).
- **Ward Cunningham:** clean code is obvious, simple and compelling. Each module tells you what comes next.

Writing clean code today makes it easier to write good code in the future. We need to be able to read and understand the surrounding code in order to write clean code.

We should always try to improve existing code, even if we make small ammends and modifications. Try to leave the existing code better than we found it.

## Chapter 2: Meaningful Names

Naming is everywhere in code: variables, functions, classes, directories, etc. It's good to change names when a better one is found.

Some guidelines for naming:

- Name should reveal intent: why the named entity exists, what it does and how it's used.
- Avoid words that mean something different. E.g.: don't use _accountList_ for a group of accounts if it's not an actual List.
- Avoid small variations in names, particularly when they're long names that vary only in a couple characters.
- Spell similar concepts in a similar way (consistent spelling).
- Avoid noise words that add no meaning. They're redundant. E.g.: _info_, _data_, _a_, _an_, _the_, as well as the word _variable_ in a variable name or _table_ in a table name.
- Distinct names should imply what the difference is.
- Names should be pronounceable, for easier discussion.
- Names should be easily searchable (very short words are harder to search, because they might offer too many results).
- Single letter names should be used only as local variables in short methods, where their scope is limited to that method only.
- Hungarian notation is outdated. It may cause problems when searching because many identifiers start with the same characters, and it makes it hard to choose a new name when needed (e.g.: the implementation changed and now a phone number is not a string but some PhoneNumber class, so the name _phoneString_ is now misleading). This also applies to the _accountList_ example given above.
- In the old days they used to prefix names to indicate what it was, but this is not needed anymore (E.g.: using m_ as a prefix for class members).
- Mental mapping: don't force people to re-map names to something they already know. Using _i_, _j_ and _k_ in loops is customary, so using something else might be confusing. Clarity is king.
- Class names should be nouns or noun phrases. Avoid verbs.
- Methods names should be verbs or verb phrases.
- Don't use funny names that reduce clarity and are culture-dependent. E.g.: _whack()_ instead of _kill()_.
- One word per concept. E.g.: _fetch_, _retrieve_, _get_ should not be used indistinctively for equivalent methods in different classes.
- Use technical names when they exist: computer science terms, algorithm names, math names, etc. If not possible, use a name from the problem domain and use it consistently.
- Don't add unnecesary context. Shorter names are usually better than longer ones. E.g.: a class named _CustomerAddress_ could be renamed as just _Address_.

Don't fear refactoring a name that other programmer chose, if you found a better one.

## Chapter 3: Functions

Functions should be small and try to avoid nested code blocks, having at most 2 indent levels. Blocks inside _if_, _else_, loops, etc. should be one line long (if more, it should be a function call).

The smaller a function, the easier it is to find a descriptive name for it. Names can be long if that helps them be more descriptive.

Functions should do one thing only. The instructions in a function should be the next level of abstraction. If a new level of abstraction is required, then a new function is needed. A function must be read in a top-down way, describing the steps to perform some task in the current level of abstraction.

Switch statements should be abstracted in some class that can be used or inherited whenever needed, to avoid violating the open-closed principle every time a new case needs to be added or modified.

Keep the number of arguments to a minimum:

- More arguments make it harder for the function to be tested, as more combinations of possible values are needed.
- Use arguments just for input values and only output information with _return_.
- More arguments make it harder for the function to be tested, as more combinations of possible values are needed.
- Use arguments just for input values and only output information with _return_.
- Sometimes an excessive number of arguments cause us to ignore some of them, and ignored code is where bugs hide.
- Flag arguments should also be avoided as they imply the function does more than one thing.
- More than two or three arguments is an indication that some of those could be wrapped into their own class. E.g.: `Circle makeCircle(double x, double y, double radius)` could be refactored into `Circle makeCircle(Point center, double radius)`.
- When there is only 1 argument, the name of the function and the argument should form a verb/noun pair (E.g.: `write(name)`).

A function should have no side effects. It only needs to do one thing and nothing else (no hidden side effects).

Functions should do something or answer something, but not both at the same time.

Avoid returning error codes, and use exceptions instead. Error codes force the caller to deal with them immediately. Also, they imply there is some class or enum where the error codes are defined and used everywhere, so programmers are reluctant to add new error codes because it implies re-compiling everything, causing them to reuse other error codes for new use cases. Exceptions allow using derivatives of the main exception class.

Error handling (by using exceptions) should be the one thing that function does. Anything inside the try-catch blocks should be in their own functions, and after the catch/finally block there should be nothing else in the function.

Edsger Dijkstra's rule for structured programming (every block of code should have one entry and one exit) is a bit outdated as it means there should be only one return statement, no break no continues and no goto statements. And that applies mostly to larger functions, but for small ones adding multiple returns, or break/continue statements can even improve readability.

## Chapter 4: Comments

Before adding comments, think through and see if there's a way to express the same in code. Sometimes they're necessary but they should be minimized.

Comments can't always be maintained, since code changes and sometimes chunks of code are moved, and not always comments are updated.

An inaccurate comment is worse than no comment at all.

Sometimes a function with a good name can replace a comment.

There are some good comments:

- Legal comments: e.g., copyright notice.
- Informative comments: e.g., an example to clarify a regex pattern.
- Explanation of intent: why it was decided to do something in a certain way.
- Clarification: when code that cannot be altered must be used and it's not clear enough.
- Warning of consequences: e.g., explain why a test is turned off.
- Todo: e.g., if some code must be modified after something else is implemented.
- Amplification: amplify importance of something that might be overlooked.
- Documentation (javadocs in public APIs).

And bad comments (most comments are bad comments):

- Mumbling: something that is not clear enough or forces you to go look at some other code.
- Redundant comments: those that are not more informative than the code.
- Misleading comments: those that are not accurate (e.g., they state some value is returned but it's not exactly true).
- Mandated comments: rules that force every function/variable to have documentation, adding clutter.
- Journal comments: logging every edit made (source control does this much better).
- Noise comments: providing no new information.
- Position markers: marking some section in source files.
- Closing brace comments: those indicating the code block that is ending at the closing brace. (e.g., {...} //while).
- Attributions: source control can do this.
- Commented-out code: nobody else will delete the commented out code because they don't know if it's there for a reason.
- HTML comments: adding html for some other tool to render the comment shouldn't be necessary. The tool should know how to handle the formatting.
- Nonlocal information: comments should be near the commented code.
- Too much information: discussions, descriptions or details added to code.
- Inobvious connection: a comment shouldn't need its own explanation.
- Function headers: a well chosen name replaces the need for a comment.
- Javadocs in nonpublic code: it's extra formality that's usually not needed.

## Chapter 5: Formatting

Consistency is key. Rules about how to format code should be agreed upon (no matter if it's a one person or a team project) and applied consistently throughout the project.

A source file should have a simple and explanatory name. The topmost part of the file should provide high-level concepts and detail should increase as we move downward.

Concepts that are closely related need to be close to each other, vertically. The stronger the affinity, the less distance there should be between them.

Lines of code that are related should appear vertically dense. A group of lines represents a thought, and these thoughts ought to be separated by blank lines.

Variable declaration should happen close to their usage. Instance variables should be declared at the top of the class (or at least in an agreed place).

A caller function, when possible, should be above the callee.

Horizontal formatting should be reasonable.

Use white spaces to surround assignment operators to accentuate them, as well as to separate parameters in a function. Don't use white spaces between function name and the opening parenthesis.

Indent the lines according to their hierarchy. Lines should be aligned to the left in regards to the scope they appear in. Don't collapse scopes down to one line when there's a short statement:

```
public String render() throws Exception {
    return "";
}
```

is preferred instead of:

```
public String render() throws Exception {return ""; }
```

## Chapter 6: Objects and data structures

Variables should be private. Hiding implementation is about abstractions, not about just adding getters and setters. An object should allow its users to manipulate it without having to know its implementation. Sometimes a data structure with procedures operating on them is all we need, instead of objects.

Distinction between objects and data structures:

- Objects hide data and expose behavior (through methods) to operate on that data. You can add new classes without changing existing behavior, but it's hard to add new behaviors to existing classes might need to change many classes that are related.

- Data structures have no significant behavior and expose data. You can add new behaviors to existing data structures but it's hard to add new data structures without changing existing functions.

Law of Demeter: a module should not know about the implementation of the objects it manipulates. An object shouldn't expose its internal structure by providing accessors. This code might violate this law when it calls `getScratchDir()` and `getAbsolutePath()`:

```
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

It's advisable to split the chain of calls:

```
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

A violatation of the Law of Demeter depends on where ctxt, Options and scratchDir are objects (then their internal structure should be hidden and the law is violated) or data structures (they have no behavior and naturally expose their internal structure).

A solution in this example could be to add a method to `ctxt` that handles whatever operation we were trying to achieve by obtaining the absolute path of the scratch directory. For instance: `BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);`

Hybrid structures that do significant things and also have public variables/accessors should be avoided, as it's hard to add new functions and also to add new data structures.

One well known data structure is the Data Transfer Object ("DTO"). It's a class with public variables and no functions, normally used when transferring data to or from some source. A "Bean" is a similar kind of object, with private variables manipulated by getters and setters. "Active records", which are beans DTOs with navigational methods, should be treated as data structures as well.
