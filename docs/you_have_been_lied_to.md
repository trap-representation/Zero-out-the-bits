You have been lied to
---

Often times, I get recommended programming videos on YouTube where the guy teaching does not even have any idea whatsoever about what he's talking about.

And what boils my blood more is that these YouTubers are getting around millions of views, meaning millions of people, who might possibly be newbies too, are actually watching these videos hopefully wanting to learn something, when all they're getting is a heck load of incorrect information.

Unfortunately though, some folks often justify it with a "You're getting this information for free" and stuff like that. In my opinion, if you're putting out information on the internet where thousands of people might see it, that information _should_ be correct, or at least you should put in some effort to make sure it is legitimate. And anyway, I consider this kind of act as a form of fraud. Sure, I'm not paying anything for it, but I'm wasting my time watching these worthless, good-for-nothing tutorials when these YouTubers are profitting off of spreading false information, which is unacceptable to me.

This post is about addressing such false claims that I've heard, not just on YouTube, but also, believe it or not, from college professors of _certain_ institutions that have generated a lot of hype around them, that they of course should not have gotten in the first place, only if people knew better.

Before I start though, I should mention that I will only talk about how such false information has butchered how some people write C code. From summoning nasal demons to literal UFOs -- these people can do all that sorcery you can ever imagine. All they need is a standard-conforming C compiler, and they'll UB the hell out of it.

Each of the following paragraphs starts with a quote specifying the claim, followed by why said claim does not make any sense at all. In some cases, a quote from the standard draft n1570 (yeah, not a huge fan of the direction C2x is headed) will be included for reference.

Claim 1.

>Characters always use the ASCII character set

They don't. The C11 standard does not mandate any specific values for members of the execution character set. §5.2.1 1 of the C11 specification has to say this (emphasis mine):

>Two sets of characters and their associated collating sequences shall be defined: the set in which source files are written (the source character set), and the set interpreted in the execution environment (the execution character set). Each set is further divided into a basic character set, whose contents are given by this subclause, and a set of zero or more locale-specific members (which are not members of the basic character set) called extended characters. The combined set is also called the extended character set. **The values of the members of the execution character set are implementation-defined**.

I think the aforementioned paragraph from the standard is pretty self-explanatory. If only people weren't lazy enough to read the specification.

I would like to add though, the C11 standard does indeed guarantee that UTF-8 string literals are encoded in UTF-8. From §6.4.5 6 of the C11 standard (emphasis mine):

>[...] For UTF-8 string literals, the array elements have type char, and are initialized with the characters of the multibyte character sequence, as **encoded in UTF-8**. [...]"
