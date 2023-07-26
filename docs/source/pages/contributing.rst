=============
Contributing
=============

Overview of the contribution process
====================================

The graph below illustrates the general process for contributing code
changes to 2DECOMP&FFT:

.. graphviz::

   digraph G {
       review[shape="diamond", label="Changes approved"]
       commit[label="commit changes locally"]

       PR[label="Open/update Pull Request"];

       Idea -> "Open new issue" -> commit -> PR
       PR -> review
       review -> merge [label="yes"]
       review -> commit [label=" no"]
       "Pick existing issue" -> commit

       {
           rank=sink;
           review; merge
       }
   }

Contributions are accepeted in the form of pull requests targeting the
``main`` branch on the 2DECOMP&FFT GitHub repository.  See `(GitHub docs)
Creating a pull request
<https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request>`_.

By default your GitHub account will not be able to push changes to the
2DECOMP&FFT repo, and you will have to open the pull request from a fork. See
`(GitHub docs) Creating a pull request from a fork
<https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork>`_.

Note that the whole process is **driven by issues**. If you found a
bug not currently referenced by an existing issue, or have an idea on
how to improve a part of 2DECOMP&FFT, please open a new item on the issue
tracker before opening a pull request.

# Contributing

1. You want to contribute but have no idea how ? Please refer to the [Get started](#get-started) section.
2. You have identified a bug ? Please refer to the [Bug](#bug) section.
3. You have improved a part of the code or have developed a new functionality ? Please refer to the [advanced](#advanced-contribution) section.

## Get started

The recommended strategy to contribute is to start with a [discussion](https://github.com/2decomp-fft/2decomp-fft/discussions) or to pick an existing issue.
To modify or experiment with the code, fork the 2decomp github repository and commit changes in a dedicated branch of your fork.
When the modification is ready for review, one can open a pull request as described in the [advanced](#advanced-contribution) section below.

## Bug

It appears that you have identified a bug in the 2decomp library.
If you are not sure this is really a bug in the library, you should go to the [discussions](https://github.com/2decomp-fft/2decomp-fft/discussions) section and open a new discussion.
Otherwise, follow the steps below.

Firstly, try to reproduce the error with a debug build of the library, a small problem size and a small number of MPI ranks.
It makes bug-hunting much easier.
Unfortunately, this is not always possible.
Please note that for a debug build, the log contains all the environment variables.
Use it to hunt the bug but think twice before sharing it as it can expose sensitive and personal information.
At least, please try to reproduce the bug on another machine with another compiler.

Secondly, if you have modified the source code of the 2decomp library, you must reproduce the bug without the modifications in 2decomp.
The development team can only provide support for sections of code available in the present repository.

Thirdly, you must provide a minimal working example.
The program using 2decomp and exposing the bug should be relatively small.
The development team will not provide support if the program exposing the bug is very long.
The programs available in the examples section are a good starting point for a minimal working example, ideally you could contribute the minimal working example to the existing examples.

At this stage, you probably did your best to simplify the problem at hand.
Open an issue and select the bug report template.
Provide a meaningful title, do your best to complete all the sections of the template and provide the version of the compiler, the version of the MPI / FFT library, ...
If you think you have a fix for the bug, please expose it inside the issue.
It is recommended to wait for feedback before opening a pull request.

## Advanced contribution

One should read this section before opening a pull request.
To fix a bug, please open an issue and use the bug report template first.
To improve a part of the code or develop a new functionality, please open an issue and use the feature request template first.
If you are not sure about your contribution, open a [discussion](https://github.com/2decomp-fft/2decomp-fft/discussions) first.
This helps raise awareness of the work and prevents repeated effort from occurring.

The code in a pull-request should be formatted using the `fprettify` program.
See the code sample in the `scripts` folder.

Pull requests must be focused, small, coherent and have a detailed description.
The longer the pull request, the harder the review.
Please empathise with your fellow contributors who are going to spend time reviewing your code.

As long as the pull request is open for discussion and not ready for merging, convert it to draft.
Whenever it is ready for merging, convert it to a regular pull request.
Please note that reviewers might push modifications directly to your branch or request changes.
