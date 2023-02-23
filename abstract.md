Redesigning the Numba Frontend for Rapidly Changing Python Bytecode
-------------------------------------------------------------------

The advent of the new Python release policy has resulted in a number of
challenges for the Numba project. Numba uses the Python bytecode as an
interface to Just-In-Time (JIT) compile Python functions. Unfortunately the
bytecode was never designed to be stable and was never intended to serve as a
public interface. As such the Bytecode specification is subject to constant
change with every new Python minor version, new bytecodes are regularly
introduced and the behaviour of existing byetcodes themselves may change in
subtle, sometimes non-documented ways. While this is advantageous in terms of
performance for the CPython interpreter it does create additional work for any
project attempting to consume the bytecode.

The challenge that Numba faces is that significant work is needed every year in
order to support the latest Python minor version. While this work is indeed
principled, usually some decisions need to be taken which lead to an increase
in code complexity. For example, sometimes bytecode sequences need to be re
written, such that they match an older minor version of Python where the
bytecode sequence was less complicated. In this talk I will introduce a new
approach, based on formal methods, to regularize (or canonicalize) Python
bytecode and thus cast any bytecode variant into a predictable and stable
format.

The approach is inspired by recent publications in theoretical computer science
related to the construction of so-called Regionalized State Value Dependency
Graphs (RVSDGs). While we don't transfer directly to RVSDGs, we have discovered
an intermediary representation introduced in (ref:: paper) which insufficient
for Numba's needs. The basic idea is to take the bytecode as generated by
CPython, which effectively describes a Control Flow Graph (CFG) of Basic Blocks
(BBs) and transform this into a canonical format. Some features of this format
are the clear identification of branching (if-else) regions and looping (for,
while) regions as well as the insertion of synthetic blocks such that any
region is closed ("closed" in this context means: there is only a single entry
edge/arc and single exit edge/arc from any given region). As a result we
can remove much of Numba's  bytecode resequencing (peephole optimizers) and
this significantly simplifies much of the dataflow analysis.

In this talk I will present the theoretical approach to regularizing bytecode
based on RVSDGs, supplemented by examples and snippets from the Numba code-base.
I will further explain how this new format allows Numba to be specialised for
any given Python minor version and how it relinquishes the need for complex
code. Looking into the future, I will present potential opportunities
for other tools such as JAX, PyTensor and others to consume this format. This
would establish the format as a common intermediary representation of bytecode for any
given minor version.  This means that consumers would receive a computationally
equivalent CFG representation irrespective of the Python minor version used.
Ideally the Python minor bytecode version would thus be abstracted away leading
to an order of magnitude simplification for any tools attempting to consume a
Python program in it's byetcode representation. Furthermore, the implementation
has been designed in a way that the payload itself can be abstracted away,
such that the representation is independent of Python itself. Thus it is
conceivable to transform the bytecode of any other programming language or
perhaps even an alternative program representation such as an Abstract Syntax
Tree (AST). This technique may enable opportunities to open multiple gateways
into the future and will provide us with ample possibilities for sustained
research and innovation.

Furthermore, this approach is part of a larger effort to transform the Numba
codebase into a compiler building toolkit that will become a framework to allow
for the creation of highly specialised compilers for the dawning era of diverse
and heterogeneous silicon that our civilisation is venturing into. I will
conclude the talk with an outlook of potential innovations in compiler
technologies during the next decade and beyond!