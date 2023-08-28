What is Chlore
---

### Chlore tries to be a language that can be defined without any ambiguities ###

You ever program in your favorite programming language and wonder whether your program will run on that one graphing calculator you have laying around that's interally using a sign and magnitude representation for signed integers, has a 4-bit word length, and is being powered with potatoes?

Well, for _most_ programming languages in use in the modern age (including that _one programming language that shall not be named_), the answer is a simple "No, it won't", unless you emulate most of the behavior the language mandates in software, which might be too expensive for some devices.

This is because most modern programming languages either aren't built with the goal of having a clearly defined language, or their specifications are so poorly written that they're not even worthy of being called one.

Now you might be asking "But trap-representation, what on earth do you mean by _poorly written_?".

You see, not every little detail about the execution environment needs to be specified by the standard. For example, chances are the average programmer will never write a program that assumes the number of bits that constitute an object of type T, but once the language mandates that an object of type T must have p bits, there's no going back. An implementation of that language _has_ to behave as if such an object has exactly p bits-- nothing more, nothing less. While this may not sound too bad, in certain cases, it can result in huge performance hits if the execution environment cannot access an exact of p bits.

It's not to say that such details should be entirely omitted or kept hidden from the programmer though; rather these should be left for implementations to define. Any language that deviates from this philosophy is what I like to call _poorly written_.

### Chlore puts simplicity before anything ###

Simplicity (in context of programming languages), even though it often conveys different meaning to different people, Chlore measures it in terms of how simple it is to write an implementation of Chlore. Anything that might be complicated to define or needs a sophisticated mechanism to implement, is never added to the language. In fact, one of the very first implementations of Chlore I wrote (Ehre) was only ~6k lines of C code.

You will be able to learn more about Chlore's design-- especially what it expects from implementations and such-- in the next chapters.

[Hello, World! (next)](./hello_world.md)

[Chloring for the win (previous)](./chloring_for_the_win.md)
