---
layout: post
title:  "The case against randomness in tests"
date:   2018-12-11
description: Random tests are great while they pass, but they cannot totally ensure your testing suite being run certainly with the same result every time because of their nature. Solving it may be a great deal.
---

![Accuracy and repeatibility](/assets/images/predictability.jpg)

_Good tests kill flawed theories; we remain alive to guess again._
\- Karl Popper

## Preambule

Automated testing is an integral part of modern software engineer
workflow. Tests serve to the purpose of veryfing that specifications
and requirements set for the elements of the system and the system as a
whole are still valid. Automated tests extend on that basis and allow the
program to be tested in an autonomous manner, both outside and inside,
covering not only the limits of a tested system, but also its internals.
Another perk of automated test suites is the fact that they can be run
multiple times at the same time period on the developers machine, build
server or any infrastructure without a need to include a real person in
the pipeline. It drastically cuts the quality assurance costs with less
people involved as the system grows and also lets quality engineers to
work on the more intelligent and business-involved tasks leaving mundane
and repetitive work behind.

However, as any technique, automated testing needs to be performed
correctly to be useful. There must me some criterias that define the good
tests and distinct them from the bad ones. How to test if the test is
valid? Actually, it depends on the program that is being tested, its
desired outputs, predicted change frequency for the code, usage frequency,
position in the system and complexity and functionality of the code.
In the common case the most likely code to be tested is the unit of
some larger system, interconnected with other units, used and changed
with medium frequency with more or less established specifications. Such tests
are written in quite the large numbers to cover most of the units and subsystems
to make sure every part works as required.

## Accuracy and repeatability

In order to be good tests must possess two features, apart from the others. These
features are **accuracy** and **repeatability**. Accuracy means that tests should have
the input with values that are proper and indistinguishable from the ones that will
be used in the real system. In may not be exactly the same data, but its significant
structure and features must be the identical with the real samples in order to
really test the code. Repeatability means idempotence and determinism. Tests
should always pass if the specifications are met and fail if they are not. No
matter how many times they are run, if neither the code, nor tests, nor infrastructure
are changed noticeably the result of the suite must stay the same.

These features are crucial because if they are not present the tests are not only
useless, but also dangerous. In case if test repeatability is compromised, tests
become a treacherous placebo, that looks like an insurance from failure on the
production stage, but in reality they may pass even if the code is not working
properly thus leaking malfunctioning units into the real world. On the other hand,
when an engineer notices that tests are brittle and has no time to recover them,
he starts to distrust the test suite and run tests repeatedly to ensure that everything
is OK or simply stops taking their results into an account which may cause numerous
troubles. If accuracy requirement is not met, tests just don't verify the real system
status, both giving false confidence and improper information on the data passed to
the unit of the system being tested to the developers that dig into the test code.

Accuracy and repeatability both work with the same concept - data. There may be some cases
when they are compromised by wrong test code, but most likely source of trouble with any
of these features is the data provided in the test setup.

## Real world dangers

Software engineers tend to use a multitude of faking tools to provide data fixtures
for the input of their tests. Mostly, these tools put a random value from the array
of data samples that belong to some category and initialize input data with such values.
Such tools are not commonly crafted well enough to provide the extensive
interface for contstraining generated data and may sometimes break the test cases
with some edge cases that can not be repeated because they are pulled from array that
also includes valid samples randomly thus making tests undeterministic. The other big
problem with such way of data generation is that category of generated inputs is not
concrete enough and may include completly inappropriate cases that won't exist in real life.
The only way to understand the parameters of generated data but the API naming of the library
is to fire it and get the sample by yourself also fails because the samples may vary pretty
much.

Random data generation for tests is a tricky concept and it needs to be used with its
defects and quirks in mind. In order to sustain accuracy and repeatability data
has to be generated under control and in an explicit manner. By explicit manner
I mean that separate setup must be provided for each category of data(correct and incorrect
email address, names with and without apostrophes and hyphens) and these types of inputs
should be stated as valid or invalid explicitly in the test.

The generation process with such requirements may be either simple hardcoding of the value in
some kind of constant in the test body that will be used both in setup and assert steps of the test execution,
or generation of strictly constrained datasets with desired characteristics which are clear
and sound inside the test itself. These methods are valuable in order to provide the immediate context
and concreteness to the data that is used in the test. If the input of the method is any integer number -
you ought to show it in test, if it is a valid email that belongs to some domain, that also has to be
the part of the test setup both with its invalid counterpart to test how some unit will handle both happy
and unhappy paths of execution. In case if some parts of the system require the input to be unique,
an engineer may use a sequence generator that will change the input in a constrained fashion to fit into
the requirement but not more(like changing a number at the end of the email address local part).

## Conclusion

It is better to avoid uncontrolled randomness in tests due to the accuracy
and repeatability problems that may arise if the data for the setup is not picked properly
 and consistently. The solution to it may be a usage of explicit literal values both in setup
and assert stages or more advanced data generation tools that allow to set constraints and build
data using some predefined strict scheme. In cases when randomness is vitally needed it is required
to catch the structure of the data needed in order to test the code and form a test data generation
engine that will form the input that will be as close to the real world as possible.

