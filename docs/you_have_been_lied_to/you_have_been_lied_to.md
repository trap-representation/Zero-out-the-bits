You have been lied to
---

Often times, I get recommended programming videos on YouTube where the guy teaching does not have any idea what he's talking about.

And what is even more frustrating is that these YouTubers are getting around millions of views, meaning millions of people, who might possibly be new to the field, are watching these videos expecting to learn something, when all they're getting is a load of incorrect information.

Unfortunately, some often justify it with a, "You're getting this information for free"; in my opinion, if one is putting out information on the internet promising people that they will become masters if they learn from the individual, that information should be correct, or at least there should be enough effort put in to make sure it is legitimate. Even though I am not paying to watch their videos, I am wasting my time watching these in the end of the day.

This text is about addressing such false claims that I've heard, not just on YouTube, but also, books, and professors of certain overhyped institutions.

Do note that I will only talk about how such false information has butchered the way people write C code.

Each of the following paragraphs starts with a quote specifying the claim, followed by why said claim is not true. In some cases, a quote from the C11 standard is included for reference.

---

### Claim 1

>Characters always use the ASCII character set.

They don't. The C11 standard does not mandate any specific values for members of the execution character set. §5.2.1 1 of the C11 specification has to say this:

>Two sets of characters and their associated collating sequences shall be defined: the set in which source files are written (the source character set), and the set interpreted in the execution environment (the execution character set). Each set is further divided into a basic character set, whose contents are given by this subclause, and a set of zero or more locale-specific members (which are not members of the basic character set) called extended characters. The combined set is also called the extended character set. **The values of the members of the execution character set are implementation-defined**.

I would add however, the C11 standard does indeed guarantee that UTF-8 string literals are encoded in UTF-8. §6.4.5 6 of the C11 standard states,

>[...] For UTF-8 string literals, the array elements have type char, and are initialized with the characters of the multibyte character sequence, as **encoded in UTF-8**. [...]"

---

### Claim 2

>Objects of type int always have a size of 4 bytes, objects of type long use 8 bytes, and so on.

This is false. The sizes of objects of types other than character types are implementation-defined. §6.5.3.4 2 of the C11 standard says,

>The sizeof operator yields the size (in bytes) of its operand, which may be an expression or the parenthesized name of a type. The size is determined from the type of the operand. The result is an integer. [...]

§6.5.3.4 5 of the same section states,

>**The value of the result of both operators is implementation-defined**, and its type (an unsigned integer type) is size_t, defined in <stddef.h> (and other headers).

The words "Both operators" in the paragraph refer to the `_Alignof` and the `sizeof` operators.

---

### Claim 3

>Using the d or x conversion specifiers (with the fprintf and similar functions) for arguments of pointer type is legal.

Footnote 92 of the C11 standard says that, most often, the result of converting an identifier that is a function designator, yields the expression that denotes the called function.

So in, for example, `fprintf(stdout, "Hi there");`, the identifier `fprintf` is the function designator, which is the expression that denotes the called function.

According to §7.1.2 1,

>Each library function is declared, with a type that **includes a prototype**, in a header, whose contents are made available by the #include preprocessing directive. [...]

The prototype of the `fprintf` function looks like so (§7.21.6.1 1):

>```
>#include <stdio.h>
>int fprintf(FILE * restrict stream,
>     const char * restrict format, ...);
>```

§6.5.2.2 7 states,

>If the expression that denotes the called function has a type that does include a prototype, the arguments are implicitly converted, as if by assignment, to the types of the corresponding parameters, taking the type of each parameter to be the unqualified version of its declared type. **The ellipsis notation in a function prototype declarator causes argument type conversion to stop after the last declared parameter. The default argument promotions are performed on trailing arguments**.

This means that, for `fprintf`, starting from the third argument (if any), only the default argument promotions are performed. §6.5.2.2 6 describes the default argument promotions as following:

>If the expression that denotes the called function has a type that does not include a prototype, the integer promotions are performed on each argument, and arguments that have type float are promoted to double. These are called the default argument promotions. [...]

And according to §7.21.6.1 9,

>[...] If any argument is not the correct type for the corresponding conversion specification, the behavior is undefined.

This not only renders the behavior of something like

```
int x = 42;
int *y = &x;
fprintf(stdout, "%x", y);
```

undefined, but also allows

```
int x = 42;
int *y = &x;
fprintf(stdout, "%p", y);
```

to launch a nuke, because §7.21.6.1 8 mandates,

>p    The argument shall be a **pointer to void**. [...]

The correct way to write the value of the pointer `y` to the output stream would be to rewrite the snippet like this:

```
int x = 42;
int *y = &x;
fprintf(stdout, "%p", (void *) y);
```

The conversion to pointer to `void` is important, because the `p` conversion specifier expects a pointer to `void`. For any other type of argument (footnote 48 allows pointers to `void` and `char` types to be interchangeable as arguments to functions, return values from functions, and members of union), the behavior would be undefined.

---

### Claim 4

>Dereferencing a null pointer always result in a segmentation fault.

It need not. It may, but it need not.

Footnote 102 presents a list of invalid values for dereferencing a pointer. Those include a null pointer, an address inappropriately aligned for the type of the object pointed to, and the address of an object after the end of its lifetime.

And according to §6.5.3.2 4,

>[...] If an invalid value has been assigned to the pointer, the behavior of the unary \* operator is undefined.

It is undefined behavior.

So not only is the program allowed to result in a segmentation violation, it can very well be the cause of a zombie outbreak.

---

### Claim 5

>Any object can be reinterpreted by accessing it with an lvalue expression of the desired type.

It's false. For example,

```
int x = 42;
int *y = &x;
float fv = *(float *)y;
printf("%f\n", fv);
```

has undefined behavior. It is already obvious that `float fv = *(float *) y` is undefined, because the object pointed to by `y` has an effective type of `int`.

(§6.5 6:

>The effective type of an object for an access to its stored value is the declared type of the object, if any. [...]

), and what is being done here is the object is being accessed with an illegal lvalue expression, since §6.5 7 mandates,

>An object shall have its stored value accessed only by an lvalue expression that has one of the following types:
>-- a type compatible with the effective of the object,
>-- a qualified version of a type compatible with the effective type of the object,
>-- a type that is the signed or unsigned type corresponding to the effective type of the object,
>-- a type that is the signed or unsigned type corresponding to a qualified version of the effective type of the object,
>-- an aggregate or union type that includes one of the aforementioned types among its members (include, recursively, a member of a subaggregate or contained union), or
>-- a character type

None of the requirements is satisfied, so a "shall" is violated. This means that the program has undefined behavior.

There is also another construct that may cause undefined behavior, which is the conversion of `y` to pointer to `float`.

§6.3.2.3 7 states,

>A pointer to an object type may be converted to a pointer to a different object type. If the resulting pointer is not correctly aligned for the referenced type, the behavior is undefined. **Otherwise, when converted back again, the result shall compare equal to the original pointer**. [...]

Even if the resulting pointer is correctly aligned for the referenced type, the only guarantee that is ever provided is that when the converted pointer (pointer to `float`) is converted back again to a pointer to the original object type (pointer to `int`), it shall compare equal to the original pointer (meaning, point to the same object). So, assuming that after the conversion the resulting pointer still points to the original object, may not be correct.
