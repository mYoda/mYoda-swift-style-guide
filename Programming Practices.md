## Programming Practices
Common themes among the rules in this section are: avoid redundancy, avoid ambiguity, and prefer implicitness over explicitness unless being explicit improves readability and/or reduces ambiguity.

### Compiler Warnings
Code should compile ***without** warnings* when feasible. Any warnings that are able to be removed easily by the author must be removed.

A reasonable exception is deprecation warnings, where it may not be possible to immediately migrate to the replacement API, or where an API may be deprecated for external users but must still be supported inside a library during a deprecation period.
