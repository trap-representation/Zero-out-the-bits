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

> Characters always use the ASCII character set.

They don't. The C11 standard does not mandate any specific values for members of the execution character set. §5.2.1 1 of the C11 specification has to say this:

> Two sets of characters and their associated collating sequences shall be defined: the set in which source files are written (the source character set), and the set interpreted in the execution environment (the execution character set). Each set is further divided into a basic character set, whose contents are given by this subclause, and a set of zero or more locale-specific members (which are not members of the basic character set) called extended characters. The combined set is also called the extended character set. **The values of the members of the execution character set are implementation-defined**.

---

### Claim 2

> Objects of type int always have a size of 4 bytes, objects of type long use 8 bytes, and so on.

This is false. The sizes of objects of types other than character types are implementation-defined. Only the size of `char`, `signed char`, and `unsigned char` (and their qualified versions) are defined, and they must be exactly 1 byte. §6.5.3.4 4:

> When sizeof is applied to an operand that has type char, unsigned char, or signed char, (or a qualified version thereof) the result is 1. When applied to an operand that has array type, the result is the total number of bytes in the array When applied to an operand that has structure or union type, the result is the total number of bytes in such an object, including internal and trailing padding.

---

### Claim 3

> Objects of type char always have exactly 8 bits.

The C standard requires the number of bits in a byte to be _at least_ 8; this does not mean that an implementation is required to have `char`s with exactly 8 bits. It can have 9 bits or even 32 bits, nothing is preventing it from doing that.

§5.2.4.2.1 1

> The values given below shall be replaced by constant expressions suitable for use in #if preprocessing directives. Moreover, except for CHAR_BIT and MB_LEN_MAX, the following shall be replaced by expressions that have the same type as would an expression that is an object of the corresponding type converted according to the integer promotions. Their implementation-defined values shall be equal or greater in magnitude (absolute value) to those shown, with the same sign.
> — number of bits for smallest object that is not a bit-field (byte)
> CHAR_BIT    8
> [...]

---

### Claim 4

> Using the d or x conversion specifiers (with the fprintf and similar functions) for arguments of pointer type is legal.

The prototype of the `fprintf` function looks like so (§7.21.6.1 1):

> ```
> #include <stdio.h> 
> int fprintf(FILE * restrict stream,
>      const char * restrict format, ...);
> ```

§6.5.2.2 7 states,

> If the expression that denotes the called function has a type that does include a prototype, the arguments are implicitly converted, as if by assignment, to the types of the corresponding parameters, taking the type of each parameter to be the unqualified version of its declared type. **The ellipsis notation in a function prototype declarator causes argument type conversion to stop after the last declared parameter. The default argument promotions are performed on trailing arguments**.

This means that, for `fprintf`, starting from the third argument (if any), only the default argument promotions are performed. §6.5.2.2 6 describes the default argument promotions as following:

> If the expression that denotes the called function has a type that does not include a prototype, the integer promotions are performed on each argument, and arguments that have type float are promoted to double. These are called the default argument promotions. [...]

`d` and the `x` conversion specifiers expect `int` and `unsigned int` arguments respectively, and according to §7.21.6.1 9,

> If a conversion specification is invalid, the behavior is undefined. **If any argument is not the correct type for the corresponding conversion specification, the behavior is undefined**.

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

> p    The argument shall be a **pointer to void**. [...]

The correct way to write the value of the pointer `y` to the output stream would be to rewrite it like this:

```
int x = 42;
int *y = &x;
fprintf(stdout, "%p", (void *) y);
```

The conversion to pointer to `void` is important, because the `p` conversion specifier expects a pointer to `void`. For any other type of argument (footnote 48 allows pointers to `void` and `char` types to be interchangeable as arguments to functions, return values from functions, and members of union), the behavior would be undefined.

---

### Claim 5

> Dereferencing a null pointer always result in a segmentation fault.

It need not. It may, but it need not.

Footnote 102 presents a list of invalid values for dereferencing a pointer. Those include a null pointer, an address inappropriately aligned for the type of the object pointed to, and the address of an object after the end of its lifetime.

According to §6.5.3.2 4,

> The unary \* operator denotes indirection. If the operand points to a function, the result is a function designator; if it points to an object, the result is an lvalue designating the object. If the  perand has type ‘‘pointer to type’’, the result has type ‘‘type’’. **If an invalid value has been assigned to the pointer, the behavior of the unary \* operator is undefined**.

It is undefined behavior.

So not only is the program allowed to result in a segmentation violation, it can very well be the cause of a zombie outbreak.

---

### Claim 6

> Any object can be reinterpreted by accessing it with an lvalue expression of the desired type.

It's false. For example,

```
int x = 42;
int *y = &x;
float fv = *(float *) y;
printf("%f\n", fv);
```

has undefined behavior because the object pointed to by `y` has an effective type of `int` (§6.5 6:

> The effective type of an object for an access to its stored value is the declared type of the object, if any. [...]

), and what is being done here is the object is being accessed with an illegal lvalue expression, since §6.5 7 mandates,

> An object shall have its stored value accessed only by an lvalue expression that has one of the following types:
> -- a type compatible with the effective of the object,
> -- a qualified version of a type compatible with the effective type of the object,
> -- a type that is the signed or unsigned type corresponding to the effective type of the object,
> -- a type that is the signed or unsigned type corresponding to a qualified version of the effective type of the object,
> -- an aggregate or union type that includes one of the aforementioned types among its members (include, recursively, a member of a subaggregate or contained union), or
> -- a character type

None of the requirements is satisfied, so a "shall" is violated. This means that the program has undefined behavior.

---

### Claim 7

> ```
> fseek(f, 0, SEEK_END);
> size = ftell(f);
> fseek(f, 0, SEEK_SET);
> ```
> is a portable way to check the size of a binary stream.

It is not portable. A binary stream need not meaningfully support an `fseek` call with `SEEK_END`.

§7.21.9.2 3

> For a binary stream, the new position, measured in characters from the beginning of the file, is obtained by adding offset to the position specified by whence. The specified position is the beginning of the file if whence is SEEK_SET, the current value of the file position indicator if SEEK_CUR, or end-of-file if SEEK_END. **A binary stream need not meaningfully support fseek calls with a whence value of SEEK_END**.

---

### Claim 8

> The pointer returned by one of the memory management functions (malloc, aligned alloc, realloc, and calloc) can be converted to pointer to any object type.

§7.22.3 1 states that the pointer returned must be such that it can be assigned to a pointer to any object type with a fundamental alignment requirement.

> The order and contiguity of storage allocated by successive calls to the aligned_alloc, calloc, malloc, and realloc functions is unspecified. **The pointer returned if the allocation succeeds is suitably aligned so that it may be assigned to a pointer to any type of object with a fundamental alignment requirement** and then used to access such an object or an array of such objects in the space allocated (until the space is explicitly deallocated). The lifetime of an allocated object extends from the allocation until the deallocation. Each such allocation shall yield a pointer to an object disjoint from any other object. The pointer returned points to the start (lowest byte address) of the allocated space. If the space cannot be allocated, a null pointer is returned. If the size of the space requested is zero, the behavior is implementation-defined: either a null pointer is returned, or the behavior is as if the size were some nonzero value, except that the returned pointer shall not be used to access an object.

§6.2.8 2 says,

> A fundamental alignment is represented by an alignment less than or equal to the greatest alignment supported by the implementation in all contexts, which is equal to _Alignof (max_align_t).

Not all types are required to have a fundamental alignment, and if the pointer returned by a memory management function is assigned to a pointer to a type with an extended alignment, the behavior will be undefined.

---

### Claim 9

> ```
> unsigned short int x = 0;
> unsigned int y = ~x;
> ```
> is portable.

§6.5.3.3 4 says,

> The result of the ~ operator is the bitwise complement of its (**promoted**) operand (that is, each bit in the result is set if and only if the corresponding bit in the converted operand is not set). The integer promotions are performed on the operand, and the result has the promoted type. If the promoted type is an unsigned type, the expression ~E is equivalent to the maximum value representable in that type minus E.

`x` has a type `unsigned short int`, which has an integer conversion rank less than `unsigned int` and must have values in the subrange of `unsigned int` as mandated by §6.2.5 8, which says,

> For any two integer types with the same signedness and different integer conversion rank, the range of values of the type with smaller integer conversion rank is a subrange of the values of the other type.

Before the `~` operator is applied, `x` must be promoted to either `unsigned int` or `int`, because (§6.3.1.1 2),

> If an int can represent all values of the original type (as restricted by the width, for a bit-field), the value is converted to an int; otherwise, it is converted to an unsigned int. These are called the integer promotions. All other types are unchanged by the integer promotions.

On implementations where `int` can represent all values that `unsigned short int` can, `x` will be promoted to `int` instead of `unsigned int`, and if the implementation is using a ones' complement representation for signed integers, the result of applying the `~` operator on `x` may be a trap representation (with the sign and all value bits set to 1), in which case, the behavior of the program will be undefined. (§6.2.6.2 2:

> [...] Which of these applies is implementation-defined, as is whether the value with sign bit 1 and all value bits zero (for the first two), or with sign bit and all value bits 1 (for ones’ complement), is a trap representation or a normal value. In the case of sign and magnitude and ones’ complement, if this representation is a normal value it is called a negative zero.

)

The correct way to write it is to either cast `x` to `unsigned int` first, or as I like to do it, add the constant `0u` to `x` before applying the `~` operator on it, like so:

```
unsigned short int x = 0;
unsigned int y = ~(x + 0u);
```
