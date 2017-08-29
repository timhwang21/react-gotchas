# Abstraction (WIP})

## Why do people think abstraction is good?

* No abstraction: first, second, third.... implementations are equally hard; maintenance is O(n)
* Good abstraction: first time is extra hard, second onwards is very easy; maintenance is O(1)
* Bad abstraction: first time is easy, second onwards is harder than writing from scratch

## Benefits of abstraction

* Abstraction is a tradeoff between FLEXIBILITY and USABILITY. No abstraction grants the ultimate flexibility, the same way that assembly is more flexible or POWERFUL than Javascript. But when it comes to usefulness, raw potential is often the incorrect metric. Use assembler vs. higher level language example here.

## Costs of abstraction

* Oftentimes we fall into the trap of assuming that generalizing is ALWAYS good, that duplication of logic is ALWAYS bad.
* The hidden cost of abstraction, though, is that with every layer of abstraction, we mask the implementation detial.
* Abstraction is HARD: abstract too little, and the user still has to do a lot of work in each case, and the abstraction isn't that useful. Abstract too much, and the user has no idea what's going on, and is constrained to the use cases accounted for at time of implementation.
* When faced with two instances of duplicated logic: is the benefit of making this case easier worth the cost of hiding implementation details?

## Effective abstraction

* The most powerful abstractions are usually the ones where the level of abstraction matches the problem space well, e.g. Rails. Rails works, because its problem space is very well defined (framework for quick iteration in web development). However, sometimes the target shifts, e.g. when a company grows (e.g. Twitter moving from Rails to Java and Scala, for performance reasons)
* You want to write it right the first time. Thus, the best time to abstract is probably not the instance you see a repeated use case.
* Coordinated abstraction is effective abstraction. Ad hoc abstraction is INEFFECTIVE abstraction. Ad hoc abstraction, with little communication, leads to constant reinvention of the wheel.
* Hidden benefit to not abstracting (yet): waiting to see if the problem space becomes better defined
	* Maybe you see three bits of duplicated code you want to abstract away. But next sprint, case 4 appears that's different enough than the previous 3 cases, such that had you written your abstraction last sprint, you wouldn't have accounted for the 4th case.


## Case studies

* DMP, the misalignment of abstraction hinges on incorrect or incomplete assumptions.
	* What: the `GridContainer` component was built under the assumption that the every page would need to display at most one type of form. Form type was interlinked with grid type. This gave the benefit that when implementing new pages, we didn't have to worry about conditional rendering logic in the grid component AT ALL. We just specified a configuration object, slapped our form into there, and it loads exactly how you would expect, doing the fetches you'd expect, storing the data in the store where you'd expect. One place to pre-process API data, one place to post-process form data, one place to define async validations. This is what let the DMP team churn out forms so quickly.
	* However, two missing pieces in the assumption: 1) changes to Navajo functionality, and 2) not accounting for routing.

## Conclusion

* My intent is not to say that abstraction is BAD. I DEFINITELY am not trying to say that. PLEASE do not make that your takeaway.
* My intent is to highlight the point that ABSTRACTION IS NOT UNILATERALLY GOOD. Abstraction has costs, the main ones being:
	* Overspecificity (lack of flexibility for unforeseen use cases)
	* Obscuring implementation detail (especially makes it harder for newcomers)
	* Duplication of abstraction implementation (when coordination is lacking)
* How to address these issues:
	* Brainstorming? (But also important not to account for too many use cases -- this hurts both runtime performance and engineer performance)
	* Consistent documentation (we had Mike R's redux-form talk, but an hour long video isn't the best thing to throw at a new hire)
	* How to solve? Big gripe currently for me, Steven F.
* To complete the picture, here are the problems for lack of abstraction:
	* Maintenance nightmare -- have to change many files whenever a functionality change is requested
	* Extremely manual -- if you have to implement 10 very similar functionalities, you don't want to do it from zero each time
	* Buggy code -- doing everything from scratch can lead to deviations in implementation and behavior
* The worst is a mix of overspecificity and underspecificity
	* I'm a huge coffee nerd, if anyone else here is into coffee the analogy is like brewing a pourover with a bad grinder. With inconsistent particle size you get both over- and under-extraction -- coffee that is both too sour and too bitter. The worst.
	* Example of overabstraction:
		* DMP Grid system is somewhat overabstracted. Remember several months ago we were very proud of making 15 grids in a week, or something. And we were also capable of churning out forms extremely quickly. This was because our use case was, to us, very clear. We were porting Navajo, we knew EXACTLY what the implementation details were, and George designed a system that accounted for the necessities. The problem came when we were faced with places where Navajo was deemed insufficient, and new behavior was requested that we DIDN'T account for. We basically had to bend over backwards to implement these in extremely inelegant ways, an example being -- opening a from from Grid B from within Grid A. We have all the data we need to make the requests, but because we added an extra abstraction layer between parent -> props -> child, this became WAY harder than it needed to be.
	* Example of underabstraction: in my opinion, rich numbers, especially money. One sign of underabstraction is that ad-hoc abstractions spring up. We have so many files and functions and selectors named /utils.js or /formatters.js. We constantly have to worry about dollars or impressions, dollars or cents, currency. What if we made an Orion Currency library, sort of like how moment JS abstracted away dates?
	* Example of a good level of abstraction:
		* Form control decorator. Imagine if every time we defined a new component, we had to specify how its label and tooltip were positioned relative to the control. Imagine if we had to specify how error messages were displayed.
		* Form validation. Not to toot my own horn, but I consider this one of the best things I've written at RF. To me, it's extremely flexible because all the rules are just functions, and it's extremely expressive because rules can be arbitrarily composed.