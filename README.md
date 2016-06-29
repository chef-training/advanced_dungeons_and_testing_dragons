# Advanced Dungeons and Testing Dragons

Your knowledge of test-driven development has allowed you to defeat the simple cookbooks that live within your organization. Before you now stands a multi-platform cookbook that wields custom resources and helper methods, and that exploits search.

This adventure will test your mettle as we delve through these dark corners of cookbook development. You will strengthen your powers in RSpec, learn the wild secrets of the Ruby language, and begin the treacherous climb to Test Kitchen and ChefSpec mastery. Through carefully crafted exercises you will gain the skills necessary to accomplish the unthinkable and test such a cookbook.

# Outline

## Goblins (Keeping it DRY)

The musty crypt takes on a rancid oder as you descend into lower catacombs. Around each bend and curve of the hand carved caves lie shelves housing the bones of the monks that were once ceremonally buried below. Several of the open graves are overturned and the bones are spilled and scattered to the floor.

The dark passage leads further down into what you hope will be the chamber. The graves along the walls are far less molested here in this area. But you do find smaller bones scattered and spread along the floor. Near the bones the refuse of some creature or creatures. The obvious reason for the foul stench that fills the cavity you are now trespassing through.

Out of the darkness you suddenly hear the screeches and snarls. Your light spell, torch, or low-light vision show that you have stumbled upon a band of goblins standing around the Ark you have been searching for. None of them smart enough to know how to use it and you hope in a few moments far less of them so they aren't able to transport it.

Roll Initiative.

Wouldn't it figure. The first encounter of this epic adventure would have to be Goblins. Goblins were once the mighty opponent when you were a low-level character writing chef cookbooks. Now they are an annoyance. A thing that takes up time. Keeping you from making the changes the make a difference.

These goblins get in your way. These goblins are all throughout the code that we write. And one particular location is in our test suites. Let's take for example the Ark cookbook ChefSpec test suite that currently describes it.

I would argue that the test suite is one of the most important places for the code to be succinct. The tales of the cookbook are written in the tests. Reviewing this test suite it is hard to find an exact answer at a glance what all is taking place.

To get a better understanding of the Ark you are after we are going to start with the test suite. Let's learn a few moves that will rid this test suite of goblins.

[reference: https://learn.chef.io/skills/joy-of-automating-episode-5/]

### Let

First let's start with the keyword `let`. Most of you should be familiar with it as it helps you define the `chef_run` that you can use within your test examples.

[example: chef_run being defined]

When using let what you are doing is providing a name, as a Ruby Symbol, that is mapped to a particular value. The benefit of let is that it only loads the content when it is needed (which yields faster performance) ... essentially whenever it is used. It also calculates that value once within the span of one example. So that means the more times you call `chef_run` it doesn't re-create more ChefRun objects instead uses the same one each time.

The `chef_run` is, however, re-created for each example. This ensures that setup or changes made in one example affect another example. This isolation is important but does come at a performance cost.

It is also an important tool that you can define any helper content that you mean need within a scenario. The power of it is in its clarity that it brings to an example.

[example: let versus no let]

When you define a let it is available within the context you defined it. This includes sub-contexts but excludes essentially sibling contexts. This is useful because you can define something in a parent context and any of the offspring context get access to it.

And if needed a child context can simply redefine the helper if they need to override a default within a single context.

You can even use a value defined in a `let` within another `let`. This is useful as it often times allows you to more clearly express a relationship or important pieces of information to explain what is going on.

### shared_examples

The last move that I want to share with you right now is one called `shared_example`.

When you are working with your test suite you may find yourself writing a similar set of expectations over and over again. You even may be copying that content from one scenario to the next.

Well `shared_example` allows you to define a group of examples separate of a context. You give them an important name to describe the behavior they are attempting to assert and then you include them within the contexts as they are needed.

There are some pitfalls to them. When you use a shared_example within an context if it comes before the shared_example is defined then it will often cause a failure. This can be solved by moving your shared_examples up to the top of the file.

[exercise (30min): refactoring tests with let and shared_example]

[review (15min): refactoring tests with let and shared_example]

So you can see the power of let and shared_examples. Before we finish combatting these Goblins I want to show you a few other important moves that will help increase the clarity within the test suite.

### require

When we define that shared_example we may want to use that within another recipe or simply remove it from the top of the file. Having it there makes it difficult to read the important things within the test suite which is the platforms and the expectations of how those platforms should perform.

At the top of each file there is a require that loads the spec_helper file. That spec_helper file is a perfect place to put shared_examples or other helpers that you may find yourself using across different test files.

### shared_context

A final powerful move is this and I share this with those of you that are finding yourself moving the other concepts rather quickly. This is a fairly advanced move and I encourage you to try it when you finish the other steps first.

The beauty of these shared_examples tied to the different lets within each context allows you the flexibilty to do some magical things.

> Fix that paragraph above. It is talking about something that should be stated before

You may even find yourself repeating the shared_examples over and over again. It may seem that really you are defining new lets and pasting the same code over and over again. You may even be defining the same lets within each context. shared_example only allows you to pass examples and not other things. That is where `shared_context` comes into play.

You can define a `shared_context` with `it_behaves_like` and `let` within a context. Then you can take that shared context and apply it to any of the context with the addition of a flag.

[example: shared_context]

You can even take it one step further with the alias method. Which allows you to do something incredible like make your specification even more descriptive. Albeit magical.

[example: alias with shared_context]

[exercise: same exercise with require and shared_context]

## Custom Resource

Recovering the Ark from the Goblins was a small feat. The larger task lay ahead which is understanding how to use it. By examining the runes along the side of it and with the aid of spiritual tomes granted to you it feels like you could weild the power that it possesses.

> Further setup the idea that before 'using' this powerful magical item (the Ark) that it would be best to practice using it before walking into battle.

The Ark cookbook presents a unique and interesting problem. The cookbook itself provides a resource that can be employed within the cookbook itself but mostly it is used by other cookbooks.

This means that a lot of the functionality is untested until it is used by a downstream cookbook. This is dangerous as this means we have to rely on other cookbooks to test the features that we define within this cookbook.

All the test suite defined and verified was that the correct packages and other node attributes were correctly defined but we have yet to verify that the Ark does what it says it does.

The implementation in front of you comes with a single recipe named 'test'. This recipe was the original way to verify that the cookbook was acting appropriately. One problem with this implementation is that this single recipe does not cover a large set of the cases and conditions; there is a lot of untested functionality.

We could continue to expand that contents of that recipe but eventually we will have a large recipe that tests a lot of content. It will be hard to read but at least it will verify the features of the custom resource.

An alternative to a single large recipe that tests the functionality is multiple smaller recipes. But this brings up the second problem. The recipes are defined alongside the recipes that are used in production. A common tactic that I have seen is to place an underscore in front of the recipe names or prefix them with `test`. This could cause some confusion for those using the cookbook. But one that I would accept for better testing.

There is a third problem is that to use this custom resource we need to add a bunch of files to support the recipes. This means that the cookbook itself is carrying around additional code that is not necessary. So it is not only recipe files but cookbook files that we would have to contend with when worrying about the clarity.

A better solution would be to move these recipes that test Ark to another cookbook. Immediately you might think that you have to define this cookbook outside our existing cookbook but that is not required. Instead we can define this cookbook within our existing cookbook.

### fixture cookbook

First we'll make a fixtures directory.

Then within the fixtures directory we are going to generate a cookbook.

Within that cookbook let's define a single use of the custom resource. Perhaps the most common use with no additional parameters specified.

[example: default recipe that uses custom resource]

Now we need have this cookbook defined we need to add it to the Berksfile. This ensures that when we run ChefSpec it knows where to pull the cookbook from. So within the Berksfile we add the cookbook with the path attribute that points to the path within the fixtures directory.

Now time to create the actual specification file. First we will define a directory to store the specifications.

Then we will define default specification. This specification requires a chef_run. This chef_run is going to converge the recipe within the fixture cookbook. We want to start with a single simple expectation.

[example: default spec with chef_run defined to use ark_spec::default]

When we execute the expectation we see that it passed.

### defining matchers

We cannot actually define tests for the custom resource that we have written. The reason for it is that ChefSpec does not have matchers for that. So if we want to use our custom resource within the the specifications that we have defined we will need to create the matchers.

The matchers are traditionally stored within the libraries directory in a file named `matchers.rb`. We must define a matcher for each action.

[example: matcher defined in the matchers file]

We surround the matcher that we defined with a small amount of logic which states that we only want to load the matcher if the constant `ChefSpec` has been defined. This prevents the matcher from being when we apply this cookbook on a remote system. It likely would not cause an issues but it is important to not load code that could affect a chef-client run.

Now with that matcher defined we can define a new example and expectation that states that our resource is being used and make assertions.

[example: expectation with new matcher used]

### step_into

Making assertions that the resource is being used is useful but we also want to make sure the underlying resources employed by the custom resource are doing what we expect them to do. To do that we need to step into the resource and that requires us to change the `chef_run` that we have setup slightly.

[example: changed chef_run with the `step_into` the resource name]

With that defined we can now setup expectations on the resources within our custom resource. This is useful because we can now work through what it is our resource is doing.

[example: walking through defining expectations for each resource]


As an exercise I want you to define another expectation similar to the one that we defined for a different action or set of parameters. Remember as you define these additional expectations you have the full power of `let`, `shared_example`, and `shared_context`, and `alias`.

[exercise (25m): working through defining more expectations]

## Testing Helper Methods

> We move into the next section and start talk about the daunting task of testing every path through the code. That is when we start talking about writing specs to test some of the helper methods as individual units of code.

> Looking at the Ark helper methods is a bit overwhelming. The solution would require lots of mocks and other stubs to make things work correctly and I think ultimately we may lose some of the focus on the work that is being done.

> Instead this may be good time to move to a different cookbook that employs the Ark cookbook (if we want to continue the thread of the story). If we cannot then we will need to move to another cookbook itself that simply uses helper methods and we can demonstrate how that we can mock/stub them.

### Testing it with refactoring and mocks

Instead of mocking out searching and nodes instead hiding behind helper methods

## Testing Search and Multiple Nodes

### Unit testing it with mocks

Mocking out search and nodes and all calls to the server
