# Test-Driven Development for Embedded C

* Author: James W. Grenning
* Link: [Test-Driven Development for Embedded C](https://pragprog.com/titles/jgade/test-driven-development-for-embedded-c/)
* Book version: P3.0
* Code repository: [Github](https://github.com/jwgrenning/tddec-code/tree/master)
* My forked repository: [Github](https://github.com/renemoll/tddec-code)

**Why I picked up this book**: I already use test-driven development, with embedded systems, but felt my way of working was not scalable. Therefore, I am hoping to learn alternative ways of working.

## Working with the example code

Clone the repository, then run the following command from within the folder:
```bash
docker run --rm -it -v $PWD:/usr/src -w /usr/src -e CPPUTEST_HOME=/usr/src/cpputest gcc:7 /bin/bash
```

Simply call `make` to build the code base.

# Notes

## Definitions

This is not intended to be a complete list, just the definitions I believe are relevant.

Term            | Definition
|---------------|------------------------------------------|
Test-case		| Code testing a specific behaviour of a module (code under test). It follows the common phases (TODO: ref).
Test-fixture	| A way to re-use code over multiple test-cases to place the code under test in a specific state.

## Way of working

Create an initial list of tests you foresee, it does not need to be complete. It is intended to get a start. Likely, the list will grow during development. Input: behaviour of the module at hand. Pick a test-case and go through the TDD cycle. 

### TDD cycle

1. Add a small test
1. Run all test to see the test fail
1. Add a small implementation to make the test pass
1. Refactor

Alternative: Red-Green-Refactor cycle. Adding a failing test equals a red progress. Making the test pass equals to green progress, after which it is time to refactor.

### Anatomy of a test-case

The phases of a test-case:
1. Prepare: prepare your code under test by getting it in the correct state.
1. Execute: trigger your code to do something (call a function, trigger a state transition, handle incoming trafic).
1. Verify: verify the result against expectations.

Some may add an additional phase: clean-up; with the intent to reset the system in the original state.

### Three laws of TTD

1. Do not write production code unless it is to make a failing unit test pass.
	
	Ensures you specify the desired behaviour.

1. Do not write **more** of a unit test than is sufficient to fail (inc build failures).

	Ensures you can detect a failure.

1. Do not write **more** production code than is sufficient to pass the one failling unit test.

	Ensures you make the minimal change required. 

These rules are intended to create small increments and keep the system as a whole operational. 

### Test attributes

1. Fast
1. Isolated
1. Repeatable
1. Self-verifying

### DTSTTCPW

Stands for: Do The Simplest Thing That Could Possibly Work.

Implies to start with a fake implementation until the effort of maintaining the fake becomes to much work.

### The embedded TDD cycle

The embedded TDD cycle extends the TDD cycle:

1. TDD cycle;

	The first step is to go through the usual TDD cycle to develop your code.

1. Compile for target;

	Once a while, compile your new code using the target compiler to verify it builds. 
	> I tend to do this when most of my tests are working and I ready to start refactoring.

1. Execute unit-tests on target;

	Using either evaluation hardware or final hardware, running unit test on the target hardware to uncover possible runtime library bugs.

	> While possible, I rarely do this step. I have done it in the past and it seemed quite the hassle to get it, and keep it, working. For example, the limited FLASH memory meant I had to split the unit-tests into multiple binaries and each had to be uploaded, executed and verified.

1. Run acceptance tests on target.

	Verify funtionality through automated acceptance tests.

	> Note: you may have to add strategic data injection/recording points into your application for testing. However, I think it is worth the time and costs.

### Common pitfalls

1. Tempted to write more production code then needed to pass a test-case.
   Either you will get to it, or you notice you missed a test-case so add it to the list.
   Also, once more test-case are implemented, the production code will likely be need to be refactored anyway.

## Tips

### DRY: Don't repeat yourself

The idea is to avoid code dubplication by creating a new function when you notice you copy specific logic.

Pros:
1. Maintainability: if code is repeated, you (or the next developer) will forget to update all places where the code is dublicated.
1. Readability: moving a logic into a function with a proper name makes it easier to go through a module. 

> Be careful not to overdo this. Make sure you only do this for actual duplicated code, not what you anticipate or think would be useful.

### Dual targetting

The idea behind dual targetting is that your code runs on at least two targets: the target hardware and your host system. Building your code for both allows the short TDD cycle and ensuring you can easily switch to the target.

#### Risks

* Different compilers may vary in their language support;
* Different compilers likely have different bugs;
* Different compilers come with different runtimes, with different bugs;
* Primitive data types may have different sizes, alignment and endianness.

## Why TDD

1. Focus on the interface first

## Why TDD for embedded

Pros:

1. Verifying production code without hardware, before the (final) hardware is available or scarse;
	
	Of course development kits can be used to a certain extend, until you reach their limitations or costs (time).

1. Avoiding debugging hardware and software at the same time. New hardware likely has bugs, combine that with new software and you have multiple points of failure.

1. Isolate the hardware/software interaction;

	Clearly define the hardware/software interface and decouple software modules from the hardware.

1. Avoiding the flash/trigger/debug cycle.

	And the possible shortcuts one develops during manual testing to save time.
