# Welcome to Lab 2

## Orientation

In this lab, we will focus on preparing you for your journey into containerized C++ development using cmake and other build tools. In the first part of this exercise, you will create a Dockerfile allows you to build an image that encapsulates your C++ development environment. Though it's not the case that every development project can be achieved using the same image (projects may require different sets of depencies, for example, or versions of dependencies), it's the case that many projects may require a common set of software to develop (e.g. a compiler, or build tools like cmake). In this project we will build a base docker image which you can use for development during later exercises in this course. We will also illustrate how you can _extend_ that base development image to create new images that contain project specific dependencies like BLAS or MPI.

### Part 1: Building your base development image

#### Background

A "development image" is simply an image that contains the dependencies required to develop your software. This could entail any or all of the following:

- A compiler
- Build tools like cmake
- A testing harness
- Code coverage tools
- A debugger
- Version control (if you are going to development inside your container)

Feel free to include anything you think will be useful in your development container, remember: you can use and develop this image throughout the semester!

At a minimum, include a compiler (g++), make, and cmake. Check out the [build-essentials](https://packages.debian.org/sid/build-essential) meta-package as well. This is a bundle of useful tools for C++ development. Though it also includes some extras, which are related to generating Debian packages,(which goes against our small-image ethos), this image is meant for your personal use, so you don't need to be super strict about size.

To summarize:
1. Fill out DevelopmentBase.Dockerfile with the definition for your development image
2. Build and specify a tag for your development image
3. Add a line to develop_container.sh that runs an interactive container based on your image and mounts the contents of example_library to a directory called in your container (I recommend something like /app or /workspace in the container - avoid using the filesystem root).
4. Using the interactive container, navigate to the `src` directory (inside example_library on the host, but inside /workspace in the container) and create a new file called README.md with contents "Hello World". Confirm that this file appears on the host filesystem.

### Part 2: Creating a specific development image that includes BLAS

In some cases (and in this example), you will be developing a package that has requirements beyond the development tools in your base development image. For example, in this lab, we will be writing a small C++ program that utilizes functionality from BLAS. Not every program you write may require BLAS, but you will need it for this project, so it needs to be inside your development container. 

Create a new image definition in DevelopmentBLAS.Dockerfile that extends your general base image from Part 1 with an installation of the BLAS development libraries.

### Part 3: Writing a small program in your development image that uses BLAS

In the `example_library/src` directory in this repository, implement two functions in the library which,

1. Calculates the projection of one vector onto another. Use the BLAS dot product implementation for this.
2. Calculates the determinant of a 2x2 matrix.

Fill out the `example_library/CMakeLists.txt` file such that the BLAS dependency is included in the compiled output.

### Part 4: Writing unit tests using the Google Testing framework

Depending on your personality, writing tests can feel boring or comforting or anywhere in between. Regardless, it is a foundational aspect of software development. If you are interviewed for a job by a company that does no automated testing, I recommend vigorously searching for a different position. Without automated tests it is fantastically difficult to be aware of regressions that occur with each change to large codebase. As a result, even scientists and engineers who think of test writing as boring or secondary will come to enjoy the security guaranteed by a thorough test suite.

To ensure that your implementations are correct, use the Google Test Framework to write test cases for your projection function. Make sure (using tests) your implementation works for the following cases:

1. Two parallel vectors of equal length
2. Two orthogonal vectors
3. Vectors of equal length at a 45 degree angle to eachother

Invoke your tests using the XXX file, and fill out the `example_library/run_tests.sh` file with a line that invokes your test runner.

### Part 5: Assessing test coverage using gcov

Test coverage metrics are an important tool for inspecting the completeness of the test suite that you've written. These tools tell you the fraction of your source code that is executed when your test suite runs. If a line of code is not run during the test suite, it follows that there are no assertions in the test suite that depend on its behavior - in other words, it is untested, and if a commit inadvertently changes or breaks its behavior, no one will be the wiser until a bug report from a disgruntled user comes floating in.

Different organizations will adopt differing testing philosophies, that is, what to test, how to test it, when the tests should be run, and when the tests should be written.

The most optimistic of engineers sometimes strive for 100% test coverage. In this case, every line of source code is invoked by the test runner. This doesn't guarantee that the test suite thoroughly the confirms behavior of every corner case in your development project, but it at least means that there are no completely blind spots in your project.

We have only tested the projection function in our library. The determinant function remains untested. Let's see how that appears in the code coverage report.



### Part 6: Using github actions to run your tests