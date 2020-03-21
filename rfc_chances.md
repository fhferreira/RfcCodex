# RFC chances guide 

This guide seeks to help people understand the attitude the PHP internal participants take when evaluating RFCs, and what factors are likely to make RFCs be less or more likely to pass.

It is written as my personal understanding of other people's priorities; which means that it is not guaranteed to be correct, and may become inaccurate over time as people's view change.

## Things that make an RFC less likely to pass

### Adding ini options

There is a general feeling that ini options are a mistake. In general they are harder to manage than not having them.

They are a particular problem where two libraries might want to have different settings (e.g. error handlers, or floating point precision) as there isn't any effective way to do that.

Application level ini settings are significantly less terrible (i.e. where one setting should be used across the entire application e.g. disabled functions) as they don't have the problem of different libraries needing different settings.

### Can be done in userland

PHP is written in C. Maintaining C code is a lot harder than maintaining PHP code.

The release cycle of PHP is fixed, and relatively slow compared to userland projects. This means that BC breaks or adding new features is much harder for core PHP than userland projects.

Anything that is implemented in core PHP adds to the burden of maintaining core PHP. 

Because of those reasons, any RFC that could be implemented in userland (e.g. [Server-Side Request and Response Objects
](https://wiki.php.net/rfc/request_response) ) is going to have a pretty hard time passing without a clear justification of how being in core is better than being a userland library. 


### Bespoke syntax

Some RFCs have suggested introducing a new syntax to solve a particular problem.

* [Compact Object Property Assignment](https://wiki.php.net/rfc/compact-object-property-assignment)

* [Object Initializer](https://wiki.php.net/rfc/object-initializer)

Adding new syntax in this way does not seem a good approach to language design.

Rather than introducing the new syntax to be used for a small RFC, it would be better to introduce the new syntax as it's own RFC, so that all the use cases of that syntax can be though through, and then show how it could be used for the specific RFCs.  

For example, the [named parameters](https://wiki.php.net/rfc/named_params) introduces new syntax but does it in a way that fully thinks through that syntax, and how it would interact with existing PHP code. 

If the Named Parameters RFC was introduced, the syntax would be usable for the 'Object Initializer' and 'Compact Object Property Assignment' RFCs.

### Being obviously incomplete

It's okay for an RFC to deliberately leave stuff for future expansion out of the RFC, but it's much less okay to not address things that are going to be immediately frustrating for PHP users.

For example, for the [Write-Once Properties](https://wiki.php.net/rfc/write_once_properties) RFC, at least some of the no votes were because it did not address object cloning, which is a vital consideration when dealing with immutable objects.


### Not being aware of PHP's idiosyncrasies

There are features in other languages that are possible in those langauges because of the type system used in that language.

Because PHP's type system includes much more type juggling than other languages have, some features would be much more difficult to use in PHP.

For example, [Method Overloading](https://github.com/Danack/RfcCodex/blob/master/method_overloading.md) is something that people have asked for. However this idea is much more difficult to use in PHP where the type juggling makes it harder to reason about what method is going to be called.


### Adding more edge cases

The PHP language is not a perfect design. But internals would prefer to not make the language any worse by adding features that have many edge-cases.

Although the [Allow function calls in constant expressions](https://wiki.php.net/rfc/calls_in_constant_expressions) clearly makes PHP be more powerful, it does so in a way that dramatically increases the number of edge-cases - in this case by allowing some, but not all functions to be used in const initializers.


## Things that make an RFC more likely to pass

### Clear upgrade path

PHP internals values backwards compatibility more than other projects typically do. Where changes that break backwards compatibility need to happen, having a clear upgrade path from the current behaviour to the new behaviour makes it much more likely the RFC will be accepted.

For example, for example for years, the behaviour of [ternary operator associativity](http://phpsadness.com/sad/30) was a source of sadness.

> The ternary operator is left-associative and therefore behaves entirely incorrectly


Rather than trying to fix this in one step, the [Deprecate left-associative ternary operator](https://wiki.php.net/rfc/ternary_associativity) RFC made it so that using nested ternaries without explicit parentheses will throw a deprecation warning.

This means that in a future version of PHP, we could drop the requirement for parentheses and allow for the default behaviour to be right-associative (aka what people expect).


### Being written clearly 

There are couple of things that should be covered in an RFC.

#### Explain why something is a problem

This might seem obvious, but having a clear description of why the current situation is sub-optimal makes it much more likely that the conversation will be conducive to the RFC being passed.

#### Explain why it should be solved in PHP core

Not all problems can or should be solved in PHP core. As well as the preference for 'solving it in userland', there are some problems that just can't be solved adequately in PHP core.

An example of this is the various 'taint' RFCs. Although having these in PHP core would help guard against trusting user input, there would still be cases where it wasn't obvious what was and wasn't user input, which means that people would still need to guard against trusting that data. 


#### Explain why the solution proposed is likely to be the right one

Putting it simply, an RFC needs to explain why an idea is the right one, rather than just something that could be done. 

If this is done well, it will limit how many alternative suggestions people suggest, as well as make people more comfortable adding to core, where it will need to be maintained for many years. 

