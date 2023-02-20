Redesigning the Numba Frontend for Rapidly Changing Python Bytecode
-------------------------------------------------------------------

The advent of the new Python release policy has created a number of challenges
for the Numba project. Numba uses the Python bytecode as an interface to
Just-In-Time compile Python functions. Unfortunately the bytecode was never
designed to be stable and was never intended to serve as a public interface. As
such the Bytecode specification is subject to constant change with every new
Python minor version, new bytecodes are regularly introduced and the behaviour
of existing byetcodes themselves may change in subtle, sometimes
non-documented ways (examples?). Effectively the Python bytecode is largely not
designed for humans or any systems other than the CPython interpreter to
consume and as such can only be described as "vomit on his sweater already,
mom's spaghetti.".

The challenge that Numba faces is that significant work is needed every year in
order to support the latest Python minor version. Until recently this has been
a cacophony of largely unprincipled hacks, workarounds and heuristics in order
to support new Python versions. In this talk I will introduce a new,
principled approach to regularize (or sanitize) Python bytecode. The ideas used
here are inspired by recent publications in theoretical computer science on the
topics of Regionalized State Value Dependency Graphs or RVSDGs as they are
commonly known. While we don't transfer directly to RVSDGs, we have found that
an intermediary representation introduced in (ref:: paper) may be sufficient for
Numba's needs. The basic idea is to take the bytecode spaghetti, which
effectively describes a Control Flow Graph (CFG) of Basic Blocks (BBs) and
transform this into a sane format. Some features of this format are the clear
identification of branching (if-else) regions and looping (for, while) regions
as well as the insertion of synthetic blocks such that any region is closed
("closed" in this context means: there is only a single entry edge/arc and
single exit edge/arc from any given region).

In this talk I will present the theoretical approach to regularizing bytecode,
supplemented by examples and snippets from the Numba code-base. I will further
explain how this new format allows Numba to be specialised for any given Python
minor version and how it relinquishes the need for hacks and heuristics.
Looking into the future, I will present potential opportunities for other tools
such as JAX, PyTensor and others to consume this format, which would make the
format a common intermediary representation of bytecode for any given minor
version.  This means that consumers would receive a computationally equivalent
CFG representation irrespective of the Python minor version used. Ideally the
Python minor bytecode version would thus be abstracted away leading to an order
of magnitude simplification for any tools attempting to consume a Python
program in it's byetcode representation. Furthermore, the implementation has
been designed in a way that the payload itself can be abstracted away, meaning
that the representation is independent of Python itself and one could
conceivably also transform the bytecode of some other programming language or
perhaps even an alternative program representation such as an Abstract Syntax
Tree (AST). The important take home message here is that the presented
technique is likely to open multiple gateways into the future and will provide us
with ample opportunities for sustained research and innovation. Furthermore,
this approach is part of a larger effort to transform the Numba codebase into a
compiler building toolkit that will become a framework to allow for the
creation of highly specialised compilers for the dawning era of diverse and
heterogeneous silicon that our civilisation is venturing into. I will conclude
the talk with an outlook of potential innovations in compiler technologies
during the next decade and beyond!
