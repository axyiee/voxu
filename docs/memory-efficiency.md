## Memory efficiency

Voxu is designed to be as memory efficient as possible, in other words, it must be able to
allocate as little memory as possible — just the amount of memory needed to store data —
and free it as soon it isn't necessary anymore.

This of course includes a memory model, which is a set of rules that define how memory is
allocated, freed, and accessed.

First of all, it is important to understand how memory management works in software, and how
it instructs the computer to allocate and free memory.

It is interesting to understand the difference between physical and virtual memory first.
Physical memory is the actual memory chips that are installed in the computer, and it is what
the computer uses to store data.

Each operating system kernel has its implementation of a **virtual address space (VAS)** per
process, which allows the process to access virtual _or logic_ memory.

The operating system is also responsible for _address binding_ (also called address translation),
which is the process of mapping virtual addresses to physical addresses. To first understand how
address binding works, we need to look for another concept called **page table**.

Each VAS has a page table. Each entry in the page table is called a **page table entry (PTE)**, and
it contains information indicating where a memory page is located in physical memory. Address binding
works at the hardware level by communicating with a component called Memory Management Unit (MMU),
which is located in the CPU.

When we need to access or write to a memory location, the CPU sends an instruction to query the MMU
for the physical address of that memory location. The MMU then looks for the page table entry in the
page table and returns a section containing the physical page number and the page offset.

To finally access the complete physical address, the physical page number needs to be combined with
the page offset. The CPU also prefers to query the Translation Lookaside Buffer (TLB) after the MMU
has returned the physical address if applicable.

The TLB consists of a portion of the page table cached inside the MMU chip, and it is used to speed up
address binding. The original page table is stored in the main memory, and it is consulted by MMU if
the page is not found in the TLB. The point of TLB is to avoid consulting the main memory when possible
every time a new virtual address is mapped.

The purpose of Voxu is to allow the programmer to write safe code that is as efficient as possible while
allowing the user to do unsafe things if they want to.

Voxu is designed to be a low-level language, and it is not intended to be used as a high-level language.
It is intended to follow a memory ownership model similar to [Rust's].

It is worth noting that Voxu is not a garbage collected language, and it is not intended to be one. It is
cool to note as well that as seen in other languages, abstractions of stack and heap memory are implemented
in Voxu.

All variables, functions, modules, types, and traits have their value calculated at compile-time. This means
that code is meant to be safe and efficient, while avoiding using the _heap_ stack as much as possible, also
meaning that most of time time, items aren't going to be dynamically allocated, unless there are explicit
instructions to do so.

The stack is a memory region that is used to store static data — data which their length is known at
compile-time.

The heap is a memory region that is used to store dynamic data — data which their length is not known at
compile-time — all values in Voxu are allocated to the stack by default, the only exceptions being that
one explained. An example for dynamic data are mutable lists (lists that can be modified at runtime) and
mutable strings.

The concept of ownership is pretty simple and straightforward. It is a concept that is used to define
the scope of a variable, and it is used to determine when a variable is no longer needed and can be
freed, and are based in a few simple rules:

- Every value in Voxu has a variable as its owner.
- There can only be one owner at a time.
- When the owner goes out of scope and there's no borrows alive, the value will be dropped.
- The value can be moved from one owner to another.
- The value can be borrowed from one owner to another without transferring ownership.
- The value can be borrowed mutably (while allowing mutable access) from one owner to another without
  transferring ownership.
- The previous owner cannot be used after the value has been moved.
- Another mutable borrow cannot be taken while there's a mutable borrow of it alive.

[rust's]: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html

**Sources**:

- [Memory Management in Computer System, Mohammad M Rahman (2022)](https://www.researchgate.net/publication/361691619_Memory_Management_in_Computer_System)
