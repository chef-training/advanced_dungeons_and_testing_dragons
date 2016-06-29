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

I would argue that the test suite is one of the most important places for the code to be succint. The tales of the cookbook are written in the tests. Reviewing this test suite it is hard to find an exact answer at a glance what all is taking place.

To get a better understanding of the Ark you are after we are going to start with the test suite. Let's learn a few moves that will rid this test suite of goblins.

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

### Testing it with ChefSpec

step_into
fixture cookbook

### Testing it with Test Kitchen

fixture cookbook

## Testing Helper Methods

### Testing it with refactoring and mocks

Instead of mocking out searching and nodes instead hiding behind helper methods

## Testing Search and Multiple Nodes

### Unit testing it with mocks

Mocking out search and nodes and all calls to the server
