# Chapter 1: Clean Code

Bad code impedes programmers from doing a good job. The messier the code, the lower the productivity of the team. Management tends to add more staff but that's not the solution (new staff members are not familiar with the codebase).

Programmers know the technical details of what's being built so they should be helping other roles make informed decisions, instead of just submitting to the manager's deadlines. Keeping code clean at all times is the only way to meet deadlines.


###  What is clean code?

- **Bjarne Stroustrup:** clean code is focused and efficient, and does one thing well. Bad code is ambiguous.
- **Grady Booch:** code should be readable and contain only what is necessary.
- **"Big" Dave Thomas:** clean code allows to be enhanced by other people. It is minimal and has tests.
- **Michael Feathers:** clean code is made by someone who cares.
- **Ron Jeffries:** clean code has meaningful names and no duplication (one object or method should not do more than one thing).
- **Ward Cunningham:** clean code is obvious, simple and compelling. Each module tells you what comes next.

Writing clean code today makes it easier to write good code in the future. We need to be able to read and understand the surrounding code in order to write clean code.

We should always try to improve existing code, even if we make small ammends and modifications. Try to leave the existing code better than we found it.


# Chapter 2: Meaningful Names

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