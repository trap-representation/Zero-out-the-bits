Coding assessments mostly just suck. 90% of them are made by people who I would call are as far from being a language lawyer as one can get.

What's the purpose of me writing about this topic, you might ask. Well, I recently had to take this test in my college -- a coding assessment on the C programming language, if you will. I think you know where this is going. The paper was so bad that it actually cured my imposter syndrome.

This article is mostly going to be a rant about that paper.

Without going into too much boring stuff, I'd start off with the thing you are actually here for -- the hilariously bad questions.

Starting with the absolute gem right here.

>What is the result of sizeof(char) in a 32-bit C compiler

If you're a language lawyer, you probably know that this is written by someone who has no idea about how the language they're dealing with works.

I have no idea what the purpose of the "32-bit" here was. `sizeof(char)` is always 1; it's literally in the definition of the `sizeof` operator. It's _not_ implementation-defined.

Quoting §6.5.3.4 4 of the C11 standard:

>When sizeof is applied to an operand that has type char, unsigned char, or signed char, (or a qualified version thereof) the result is 1. [...]

Off to the second one.

>What will the result of this be:
>```
>#include<stdio.h>
>int main(){
>  char s=3;
>  switch(s){
>    case '1':
>      printf("something ");
>    case '2':
>      printf("something else ");
>    case '3':
>      printf("hah ");
>    default:
>      printf("eh");
>  }
>}
>```
>a) something something else hah eh  
>b) something else hah eh  
>c) hah eh  
>d) eh

Yeah, they thought they were pretty clever with this. The answer will be e: none of the given options, because the behavior of this program is implementation-defined. You could tell that whoever was in duty of writing the paper has no idea what they're doing when they literally talk about implementation details when it is not needed, and when it's need the most, they say "Eh, I don't give a heck".

The C11 standard does not mandate any specific value for members of the execution character set.

(§5.2.1 1

>[...] The values of the members of the execution character set are implementation-defined.

)

The only guarantees that are ever provided include: (§5.2.1 2)

>[...] A byte with all bits set to 0, called the null character, shall exist in the basic execution character set; it is used to terminate a character string.

and (§5.2.1 3)

>[...] The representation of each member of the source and execution basic character sets shall fit in a byte. In both the source and execution basic character sets, the value of each character after 0 in the above list of decimal digits shall be one greater than the value of the previous. [...]

Alright. To the third one.

>Choose the option which does not apply when dynamic memory allocation is used  
>a) Things are stored in an unorganized memory called heap  
>b) It is used when the amount of memory a program would required is not known  
>c) It is faster than using static allocation

You might be thinking what's so weird about this question that I decided to include it in this article.

Unfortunately, this is one of those questions whose answer, again, depends on the implementation.

First, C does not define any term called "heap", nor are "dynamic memory allocation" or "static memory allocation" defined, so that's incorrect use of terminology. Some people like to argue that these are well-established terms with specific meaning; I, however, do not like to use this kind of terminology, unless I'm talking about a specific implementation.

Second, since we are not talking about any particular implementation in the question, I'm not sure how someone could ever assert that "dynamic memory allocation" is "faster" than using "static memory allocation" or vice versa.

Well, you might call me nitpicky here, but I don't consider it as a nitpick at all.

Moving on.

>A pointer can be used to store the address of  
>a) A variable  
>b) A structure  
>c) A function  
>d) An instruction

Go on. You have to select a _single_ option as your answer.

Pointers can point to function and object types, don't they? Hmmm, maybe the specification is wrong. Maybe whoever made this paper has some divine knowledge and understanding of the "inner workings" of C's abstract semantics. Yep, the standard is definitely wrong. Definitely.

Jokes aside, pointer types can be derived from function and object types.

From §6.2.5 20 of the C11 specification:

>[...]  
>-- A pointer type may be derived from a function type or an object type, called the referenced type. [...]

So there isn't really a _single_ correct answer.

Also, I would've preferred the use of the term "object" over "variable", but that's just me.

To the next one.

>What is the output of the following:
>```
>#include<stdio.h>
>int main(){
>  int n=41;
>  void *ptr=&n;
>  printf("%d",ptr);
>}
>```
>a) Compilation error  
>b) 41  
>c) Address of ptr  
>d) Address of n

Oh boy, not this again. It literally has undefined behavior. I have no idea what they wanted me to choose for the answer.

If you want to know why exactly this is undefined behavior, check out [Claim 3 of You have been lied to](../you_have_been_lied_to/you_have_been_lied_to.md). Needless to say, there is no "correct" option.

Well, that'll have to do it for now, I'm afraid. I might add in a couple more in the future if I have time.

And if you like language lawyer stuff, be sure to give [You have been lied to](../you_have_been_lied_to/you_have_been_lied_to.md) a read, where I bust such claims as "characters are always represented using ASCII in C" and "`int` always use 4 bytes".
