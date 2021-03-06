# News
01/01/2015 - Began transitioning HaskHOL's project page to GitHub to prepare for my departure/graduation from KU.

03/01/2015 - Made a number of HaskHOL packages public (haskhol-deductive, haskhol-math, haskhol-haskell) in support of the verificatin plugin described in our ICFP'15 submission.

# Description

HaskHOL is a Haskell hosted domain specific language for Higher-Order Logic (HOL) theorem proving.
The goal of HaskHOL is to provide a lightweight, general purpose theorem proving library for direct use by other Haskell programs and libraries.

HaskHOL, like every other member of the HOL proof system family, follows the LCF implementation style -- it starts with a sound and simple logical kernel from which more advanced features are bootstrapped.
The primitive logic of HaskHOL is inspired heavily by Freek Wiedijk's [Stateless HOL](http://www.cs.ru.nl/~freek/notes/hol_light-stateless.tar.gz) and Norbert Voelker's [HOL2P](http://dces.essex.ac.uk/staff/norbert/hol2p/); both of which are extensions of John Harrison's [HOL Light](http://www.cl.cam.ac.uk/~jrh13/hol-light/).

At the lowest level, HaskHOL's logical kernel is a "psuedo-stateless" implementation of a HOL logic that supports limited second-order polymorphism.  This logic is essentially a confluence of Wiedijk and Voelker's logics, with a subtle modification to the stateless approach to embed hashes for comparison, rather than definitional values.  This allows HaskHOL to efficiently represent and operate over its primitive data types, even in the presence of non-lazy traversals.  As a brief aside, the current reliance on hashes is potentially unsound, however, that should be fixed by moving to static keys as provided by the static pointers implementation coming soon to GHC.

The stateful features of the HaskHOL system are built on top of this kernel by simulating the desired side effects via a computational monad.  We find this segregation to be beneficial, as the implementation of the side effects does not need to worry about maintaining the soundness of the system, provided that the interface exposed by the kernel is respected.
The [haskhol-core](https://github.com/ecaustin/haskhol-core) package provides the kernel, stateful layer, and other basic functionality (parser, pretty-printer, etc.) of HaskHOL.
Additional packages providing more advanced libraries are in an experimental, semi-stable state, but should be made publicly available soon.

The current focus of the HaskHOL project is developing a linkage with GHC at the compiler plugin level for the purpose of verifying Haskell code natively at compile time.


# Status

The Core HaskHOL system is now publicly available on Hackage.
Provided you have an up to date Haskell and Cabal installation, installing HaskHOL-Core should be as easy as running the command: `cabal install haskhol-core`

Additional libraries are available by request.
They will be released publicly, pending a code review and documentation.

A basic tutorial is in the works, but its availability is currently unknown.
In the mean time, we encourage you to consult the documentation of the previously mentioned systems, and Gordon and Melham's *Introduction to HOL*, to answer any HOL questions you may have.


# Papers

* Austin, E., and P. Alexander, [The Proof is in the Plugin](/haskhol/papers/proof_plugin.pdf), Submitted for Consideration to ICFP'15.

* Austin, E., and P. Alexander, [HaskHOL: A Haskell Hosted Domain Specific Language Representation of HOL Light](/haskhol/papers/haskhol-tfp10.pdf), Trends in Functional Programming (TFP’10), Norman, OK, May 17-19, 2010.
    
    This draft publication details the very earliest implementation of HaskHOL -- a naive translation of HOL Light's logical kernel into a functionally-equivalent Haskell version.  The crux of the work was identifying both the requisite OCaml side-effects and a monad transformer stack that could simulate them.  This version failed miserably for a number of reasons, but it framed the basic design of HaskHOL that's still used today.  There's also a brief mention of using QuickCheck to verify the prover kernel by testing with generators for well-formed proof terms, but that work has sadly gone neglected in the last few years.


* Austin, E., [HaskHOL: A Haskell Hosted Domain Specific Language for Higher-Order Logic Theorem Proving](/haskhol/papers/austin-thesis.pdf), Masters Thesis, University of Kansas, 2011.

    My Masters Thesis detailed the first major revision of the implementation of HaskHOL.  The major changes included:
    * A mechanism for extensible state to support libraries introducing new fields to the working theory.
    * A move to a monad stack based on `IO`.
    * A foundation for implementing HaskHOL libraries that modified the current working theory.

    It also notably included the first performance metrics for HaskHOL.  Summarized:  HaskHOL worked, but it was incredibly slow.  There were known space leak issues and most non-trivial proofs expanded out to computations where sub-proofs were used, and thus recomputed, repeatedly.  Still.  It worked, which was pretty exciting at the time.


* Austin, E., and P. Alexander, [Haskell + HOL = HaskHOL](/haskhol/papers/haskhol-ifl11.pdf), 2011 Symposium on Implementation and Application of Functional Langauges (IFL'11), Lawrence, KS, 2011.
    
    This is essentially a boiled down summary of my Masters Thesis, focusing on the implementation aspects of HaskHOL and its run-time performance numbers.


* Austin, E., and P. Alexander, [Chasing Sound, Efficient Proof with a Monadic, HOL System](/haskhol/papers/chasing_efficient_proof.pdf), Unpublished, 2012.

    This unpublished paper extended the IFL2011 publication to include the first attempt to optimize HaskHOL.  Compile-time meta-programming was used to move as much proof effort from run-time to compile-time as possible.  Following from the same basic idea as quasi-quoting proof terms, we wanted to "quasi-quote" proofs themselves -- lifting constant theorems into the source code as values at compile-time.  In order to guarantee that these theorems were only reused in sound contexts, Haskell's advanced type features to record what theory checkpoints a computation or operation depended on.

    The performance evaluation was repeated, this time with an apples-to-oranges comparison with HOL Light.  As was to be expected, run-time was greatly improved and was now comparable to HOL Light, at least for the selected test cases.


* Austin, E., and P. Alexander, [Protect and Serve:  Policing Your Monadic Computations](/haskhol/papers/protect_and_serve.pdf), Unpublished, 2013.

    This unpublished pearl attempts to boil down HaskHOL's compile-time proof mechanism to a more general technique.  The `Protected` type class presented in this paper is the basis for protecting the soundness of HaskHOL values prepared via `runQ` at compile-time or `unsafePerformIO` at run-time.


* Austin, E. and P. Alexander, [Stateless Higher-Order Logic with Quantified Types](/papers/haskhol/haskhol_itp2013.pdf), International Conference on Interactive Theorem Proving (ITP’13), LNCS 7998, Rennes, France, July 2013.

    This pearl describes the foundational logic of HaskHOL, focusing on the issues behind unifying stateless and polymorphic HOL logics.  Though slightly out of date at this point in time, it is still currently the best reference for understanding HaskHOL's primitive data types.


* Austin, E. and P. Alexander, [A Monadic Approach to the LCF-Style](/papers/haskhol/monadic_lcf.pdf), Unpublished, 2014.

    This unpublished paper was an attempt to document the current iteration of HaskHOL's implementation.  Again, though slightly out of date, it represents a decent reference for understanding HaskHOL.  The plan is to update it, yet again, to reflect the state of HaskHOL after my dissertation is defended.


* Austin, E. and P. Alexander, [Compiler Directed Verification of Haskell Programs](/papers/haskhol/comp_directed_verif.pdf), Unpublished, 2014.

    This unpublished pearl documents the high-level design behind the HaskHOL and Haskell linkage.  It is in the process of being expanded to a full length paper for submission after my dissertation is defended. 