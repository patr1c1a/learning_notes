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
- Sometimes an excessive number of arguments cause us to ignore some of them, and ignored code is where bugs hide.
- Flag arguments should also be avoided as they imply the function does more than one thing.
- More than two or three arguments is an indication that some of those could be wrapped into their own class. E.g.: `Circle makeCircle(double x, double y, double radius)` could be refactored into `Circle makeCircle(Point center, double radius)`.
- When there is only 1 argument, the name of the function and the argument should form a verb/noun pair (E.g.: `write(name)`).

A function should have no side effects. It only needs to do one thing and nothing else (no hidden side effects).

Functions should do something or answer something, but not both at the same time.

Avoid returning error codes, and use exceptions instead. Error codes force the caller to deal with them immediately. Also, they imply there is some class or enum where the error codes are defined and used everywhere, so programmers are reluctant to add new error codes because it implies re-compiling everything, causing them to reuse other error codes for new use cases. Exceptions allow using derivatives of the main exception class.

Error handling (by using exceptions) should be the one thing that function does. Anything inside the try-catch blocks should be in their own functions, and after the catch/finally block there should be nothing else in the function.

Edsger Dijkstra's rule for structured programming (every block of code should have one entry and one exit) is a bit outdated as it means there should be only one return statement, no break no continues and no goto statements. And that applies mostly to larger functions, but for small ones adding multiple returns, or break/continue statements can even improve readability.