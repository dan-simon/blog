Topics: parsing, python, haskell, programming

I created a parser in Python yesterday night. It's vaguely based on Parsec, despite the fact that I don't think I fully understand Parsec yet. (In general, it seems that to understand a Haskell monad, it is best to understand fmap, return, >>=, >>, and the general equivalent of concat. (I did a quick search and I haven't found what Haskell programmers call that function in general.) In a MonadPlus, there are of course other things that also need to be understood. (I haven't seen enough of them to say what, but I would suspect mzero and mplus get you a long way.))

But this post is not about Parsec. It is about the internals of the parser I made, as opposed to those parsers which are actually made by Haskell programmers. So, what are the basic notions involved?

(By the way, before we get any further, I need to say that my parser is probably really slow and certainly is terrible at catching errors. At least it's made of composible pieces and so not entirely impossible to debug.)

The most basic notion is a thing being matched against (typically a string). This is just what you would expect it to be.

The next most basic notion is a state, which in the case of string matching is generally a location in the string.

The third notion, after which the others start to fit together, is a rule. A rule is simply a function from a thing being matched against and a state to a generator yielding tuples, each tuple containing a match (which could be anything) and a new state. In the case of a string, a rule is like a regex: it will generally start at the location indicated by the state and try to match it, and if one type of match fails it will try another.

Note that there are very general ways to combine rules. They can be chained. They can both be options. You can try to get as many matches for one as possible (the star in regex). Note that based on our definition of a rule, this can often be done in pure python fairly easily. (Repetition is hard, but it can be done in reasonable generality and then never really has to be done again.)

The next notion is a grammar, which is simply a set of rules referring to each other. If you use some `__getattr__` magic (which I did), you can make Python seem lazy and allow even self-referential rules. Also, I made the main rule autmatically try to match the whole string when a grammar is run, which is convenient.

The final, and most general, idea involved is a grammar generator. This simply has two features: given something to be matched, what is the initial state, and given something to be matched, is a state the final state? Clearly, to match something, a grammar needs to know the answer to these two questions. For strings, the answers are 0, and "is the state the length of the string?" So we have a string grammar generator. It's clear that most of the time this is the one you'd want to use, but one can imagine other cases.

So, that's how my parsing system works. I'm just glad I got away without using the term "monad". There's a believed-to-be-complete example of a JSON parser there (less than 100 lines long): check it out and tell me what you think.
