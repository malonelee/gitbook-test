---
title: Klaytn Virtual Machine
sidebar: mydoc_sidebar_docs
permalink: smart_contract/klaytn_virtual_machine.html
folder: mydoc
---

## Overview

The current version of the Klaytn Virtual Machine (KLVM) is derived from the
Ethereum Virtual Machine (EVM).  The content of this chapter is based primarily
on the [Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper).  KLVM
is continuously being improved by the Klaytn team, thus this document could be
updated frequently.  Please do not regard this document as the final version of
the KLVM specification.  As described in the Klaytn position paper, the Klaytn
team also plans to adopt other virtual machines or execution environments in
order to strengthen the capability and performance of the Klaytn platform.
This chapter presents a specification of KLVM and the differences between KLVM
and EVM.

KLVM is a virtual state machine that formally specifies Klaytn's execution
model.  The execution model specifies how the system state is altered given a
series of bytecode instructions and a small tuple of environmental data.  KLVM
is a quasi-Turing-complete machine; the *quasi* qualification stems from the
fact that the computation is intrinsically bounded through a parameter, *gas*,
which limits the total amount of computation performed.

KLVM executes Klaytn virtual machine code (or Klaytn bytecode) which consits of
a sequence of KLVM instructions.  The KLVM code is the programming language
used for accounts on the Klaytn blockchain that contain code.  The KLVM code
associated with an account is executed every time a message is sent to that
account; this code has the ability to read/write from/to storage and send
messages.


## KLVM Specification

### Symbols

The following tables summarize the symbols used in the KLVM specification.

#### Blockchain-Related Symbols

| Symbol | Description |
| --- | --- |
| <code>BC</code> | Blockchain |
| <code>B</code>  | Block |
| <code>B<sub>header</sub></code> | The block header of the present block |


#### State-Related Symbols

| Symbol | Description |
| --- | --- |
| <code>S</code> | State |
| <code>S<sub>system</sub></code> | System state |
| <code>S<sub>machine</sub></code> | Machine state |
| <code>P<sub>modify_state</sub></code> | The permission to make state modifications |


#### Transaction-Related Symbols

| Symbol | Description |
| --- | --- |
| <code>T</code> | Transaction |
| <code>T<sub>code</sub></code> | A byte array containing machine code to be executed |
| <code>T<sub>data</sub></code> | A byte array containing the input data to the execution; if the execution agent is a transaction, this would be the transaction data. |
| <code>T<sub>value</sub></code> | A value, in peb, passed to the account as part of the execution procedure; if the execution agent is a transaction, this would be the transaction value. |
| <code>T<sub>depth</sub></code> | The depth of the present message-call or contract-creation stack (*i.e.*, the number of `CALL`s or `CREATE`s being executed at present) |


#### Gas-Related Symbols

| Symbol | Description |
| --- | --- |
| <code>G</code> | Gas |
| <code>G<sub>rem</sub></code> | Remaining gas for computation |
| <code>G<sub>price</sub></code> | The price of gas in the transaction that originated the execution |


#### Address-Related Symbols

| Symbol | Description |
| --- | --- |
| <code>A</code> | Address |
| <code>A<sub>code_owner</sub></code> | The address of the account that owns the executing code |
| <code>A<sub>tx_sender</sub></code> | The sender address of the transaction that originated the current execution |
| <code>A<sub>code_executor</sub></code> | the address of the account that initiated code execution; if the execution agent is a transaction, this would be the transaction sender. |


#### Functions

| Symbol | Description |
| :---: | --- |
| <code>F<sub>apply</sub></code> | A function that applies a transaction with input to a given state and returns the resultant state and outputs |


### Basics

KLVM is a simple stack-based architecture.  The word size of the machine (and
thus the size of stack items) is 256-bit.  This was chosen to facilitate the
Keccak-256 hash scheme and the elliptic-curve computations.  The memory model
is a simple word-addressed byte array.  The stack has a maximum size of 1024.
The machine also has an independent storage model; this is similar in concept
to the memory but rather than a byte array, it is a word-addressable word
array.  Unlike memory, which is volatile, storage is nonvolatile and is
maintained as part of the system state.  All locations in both storage and
memory are initially well-defined as zero.

The machine does not follow the standard von Neumann architecture.  Rather than
storing program code in generally accessible memory or storage, code is stored
separately in virtual read-only memory and can be interacted with only through
specialized instructions.

The machine can execute exception code for several reasons, including stack
underflows and invalid instructions.  Similar to an out-of-gas exception, these
exceptions do not leave state changes intact.  Rather, the virtual machine
halts immediately and reports the issue to the execution agent (either the
transaction processor or, recursively, the spawning execution environment),
which will be addressed separately.


### Fees Overview

Fees (denominated in gas) are charged under three distinct circumstances, all
three are prerequisite to operation execution.  The first and most common is
the fee intrinsic to the computation of the operation.  Second, gas may be
deducted to form the payment for a subordinate message call or contract
creation; this forms part of the payment for `CREATE`, `CALL` and `CALLCODE`.
Finally, gas may be charged due to an increase in memory usage.

Over an account's execution, the total fee payable for memory-usage payable is
proportional to the smallest multiple of 32 bytes that are required to include
all memory indices (whether for read or write) in the range.  This fee is paid
on a just-in-time basis; consequently, referencing an area of memory at least
32 bytes greater than any previously indexed memory will result in an
additional memory usage fee.  Due to this fee, it is highly unlikely that
addresses will ever exceed the 32-bit bounds.  That said, implementations must
be able to manage this eventuality.

Storage fees have a slightly nuanced behavior.  To incentivize minimization of
the use of storage (which corresponds directly to a larger state database on
all nodes), the execution fee for an operation that clears an entry from
storage is not only waived but also elicits a qualified refund; in fact, this
refund is effectively paid in advance because the initial usage of a storage
location costs substantially more than normal usage.


#### Fee Schedule

[//]: # (Modified from Appendix G. Fee Schedule of Ethereum Yellow Paper and modified for Klaytn's implementation)

The fee schedule <code>G</code> is a tuple of 35 scalar values corresponding to
the relative costs, in gas, of a number of abstract operations that a
transaction may incur.

[//]: # (Below table is confirmed with Klaytn impelementation. Commit ID: 0432f029f7894210320868accca97d14093fcb37 )

Name | Value | Description
---- | -----:|:-----------
<code>G<sub>zero</sub></code> | 0 | Nothing paid for operations of the set <code>W<sub>zero</sub></code>
<code>G<sub>base</sub></code> | 2 | Amount of gas paid for operations of the set <code>W<sub>base</sub></code>
<code>G<sub>verylow</sub></code> | 3 | Amount of gas paid for operations of the set <code>W<sub>verylow</sub></code>
<code>G<sub>low</sub></code> | 5 | Amount of gas paid for operations of the set <code>W<sub>low</sub></code>
<code>G<sub>mid</sub></code> | 8 | Amount of gas paid for operations of the set <code>W<sub>mid</sub></code>
<code>G<sub>high</sub></code> | 10 | Amount of gas paid for operations of the set <code>W<sub>high</sub></code>
<code>G<sub>extcode</sub></code> | 700 | Amount of gas paid for operations of the set <code>W<sub>extcode</sub></code>
<code>G<sub>balance</sub></code> | 400 | Amount of gas paid for a `BALANCE` operation
<code>G<sub>sload</sub></code> | 200 | Amount of gas paid for an `SLOAD` operation
<code>G<sub>jumpdest</sub></code> | 1 | Amount of gas paid for a `JUMPDEST` operation
<code>G<sub>sset</sub></code> | 20000 | Amount of gas paid for an `SSTORE` operation when the storage value is set to nonzero from zero
<code>G<sub>sreset</sub></code> | 5000 | Amount of gas paid for an `SSTORE` operation when the storage value remains unchanged at zero or is set to zero
<code>R<sub>sclear</sub></code> | 15000 | Refund given (added to the refund counter) when the storage value is set to zero from nonzero
<code>R<sub>selfdestruct</sub></code> | 24000 | Refund given (added to the refund counter) for self-destructing an account
<code>G<sub>selfdestruct</sub></code> |  5000 | Amount of gas paid for a `SELFDESTRUCT` operation
<code>G<sub>create</sub></code> | 32000 | Amount of gas paid for a `CREATE` operation
<code>G<sub>codedeposit</sub></code> | 200 | Amount of gas paid per byte for a `CREATE` operation that succeeds in placing code into state
<code>G<sub>call</sub></code> | 700 | Amount of gas paid for a `CALL` operation
<code>G<sub>callvalue</sub></code> | 9000 | Amount of gas paid for a nonzero value transfer as part of a `CALL` operation
<code>G<sub>callstipend</sub></code> | 2300 | A stipend for the called contract subtracted from <code>G<sub>callvalue</sub></code> for a nonzero value transfer
<code>G<sub>newaccount</sub></code> | 25000 | Amount of gas paid for a `CALL` or `SELFDESTRUCT` operation that creates an account
<code>G<sub>exp</sub></code> | 10 | Partial payment for an `EXP` operation
<code>G<sub>expbyte</sub></code> | 50 | Partial payment when multiplied by <code>ceil(log<sub>256</sub>(exponent))</code> for an `EXP` operation
<code>G<sub>memory</sub></code> | 3 | Amount of gas paid for every additional word when expanding memory
<code>G<sub>txcreate</sub></code> | 32000 | Amount of gas paid by all contract-creating transactions
<code>G<sub>txdatazero</sub></code> | 4 | Amount of gas paid for every zero byte of data or code for a transaction
<code>G<sub>txdatanonzero</sub></code> | 68 | Amount of gas paid for every nonzero byte of data or code for a transaction
<code>G<sub>transaction</sub></code> | 21000 | Amount of gas paid for every transaction
<code>G<sub>log</sub></code> | 375 | Partial payment for a `LOG` operation
<code>G<sub>logdata</sub></code> | 8 | Amount of gas paid for each byte in a `LOG` operation's data
<code>G<sub>logtopic</sub></code> | 375 | Amount of gas paid for each topic of a `LOG` operation
<code>G<sub>sha3</sub></code> | 30 | Amount of gas paid for each `SHA3` operation
<code>G<sub>sha3word</sub></code> | 6 | Amount of gas paid for each word (rounded up) for input data to a `SHA3` operation
<code>G<sub>copy</sub></code> | 3 | Partial payment for `COPY` operations, multiplied by words copied, rounded up
<code>G<sub>blockhash</sub></code> | 20 | Payment for a `BLOCKHASH` operation

[//]: # (TODO-GX Couldn't find in Klaytn. Pleaes help me out ! )
[//]: # (<code>G<sub>quaddivisor</sub></code> | 100 | The quadratic coefficient of the input sizes of the exponentiation-over-modulo precompiled)

We define the following subsets of instructions:

[//]: # (W_zero confirmed with Klaytn)
- <code>W<sub>zero</sub></code> = {`STOP`, `RETURN`, `REVERT`}

[//]: # (W_base confirmed with Klaytn)
- <code>W<sub>base</sub></code> = {`ADDRESS`, `ORIGIN`, `CALLER`, `CALLVALUE`, `CALLDATASIZE`, `CODESIZE`, `GASPRICE`, `COINBASE`, `TIMESTAMP`, `NUMBER`, `DIFFICULTY`, `GASLIMIT`, `RETURNDATASIZE`, `POP`, `PC`, `MSIZE`, `GAS`}

[//]: # (W_verylow confirmed with Klaytn)
- <code>W<sub>verylow</sub></code> = {`ADD`, `SUB`, `LT`, `GT`, `SLT`, `SGT`, `EQ`, `ISZERO`, `AND`, `OR`, `XOR`, `NOT`, `BYTE`, `CALLDATALOAD`, `MLOAD`, `MSTORE`, `MSTORE8`, `PUSH`, `DUP`, `SWAP`}

[//]: # (W_low confirmed with Klaytn)
- <code>W<sub>low</sub></code> = {`MUL`, `DIV`, `SDIV`, `MOD`, `SMOD`, `SIGNEXTEND`}

[//]: # (W_mid confirmed with Klaytn)
- <code>W<sub>mid</sub></code> = {`ADDMOD`, `MULMOD`, `JUMP`}

[//]: # (W_high confirmed with Klaytn)
- <code>W<sub>high</sub></code> = {`JUMPI`}

[//]: # (W_extcode confirmed with Klaytn)
- <code>W<sub>extcode</sub></code> = {`EXTCODESIZE`}


#### Gas Cost

[//]: # (Modified from Appendix H.1. Gas Cost of Ethereum Yellow Paper for Klaytn)

The general gas cost function, <code>C</code>, is defined as follows:

[//]: # (sigma -> S_system  mu -> S_machine mu_pc -> S_machine,pc mu_s -> S_machine,sp I_b -> T_code)

<code>C(S<sub>system</sub>, S<sub>machine</sub>, I) := C<sub>mem</sub>(S<sub>machine,i</sub>') - C<sub>mem</sub>(S<sub>machine, i</sub>) +</code>
- <code>C<sub>SSTORE</sub>(S<sub>system</sub>, S<sub>machine</sub>)</code>, if <code>w == SSTORE</code>
- <code>G<sub>exp</sub></code>, if <code>(w == EXP) && (S<sub>machine</sub>[1] == 0)</code>
- <code>G<sub>exp</sub> + G<sub>expbyte</sub> x (1 + floor(log<sub>256</sub>(S<sub>machine,sp</sub>[1])))</code>,
  if <code>(w == EXP) && (S<sub>machine,sp</sub>[1] > 0)</code>
- <code>G<sub>verylow</sub> + G<sub>copy</sub> x ceil(S<sub>machine,sp</sub>[2] / 32)</code>,
  if <code>w == CALLDATACOPY || CODECOPY || RETURNDATACOPY</code>
- <code>G<sub>extcode</sub> + G<sub>copy</sub> x ceil(S<sub>machine,sp</sub>[3] / 32)</code>,
  if <code>w == EXTCODECOPY</code>
- <code>G<sub>log</sub> + G<sub>logdata</sub> x S<sub>machine,sp</sub>[1]</code>,
  if <code>w == LOG0</code>
- <code>G<sub>log</sub> + G<sub>logdata</sub> x S<sub>machine,sp</sub>[1] + G<sub>logtopic</sub></code>,
  if <code>w == LOG1</code>
- <code>G<sub>log</sub> + G<sub>logdata</sub> x S<sub>machine,sp</sub>[1] + 2 x G<sub>logtopic</sub></code>,
  if <code>w == LOG2</code>
- <code>G<sub>log</sub> + G<sub>logdata</sub> x S<sub>machine,sp</sub>[1] + 3 x G<sub>logtopic</sub></code>,
  if <code>w == LOG3</code>
- <code>G<sub>log</sub> + G<sub>logdata</sub> x S<sub>machine,sp</sub>[1] + 4 x G<sub>logtopic</sub></code>,
  if <code>w == LOG4</code>
- <code>C<sub>CALL</sub>(S<sub>system</sub>, S<sub>machine</sub>)</code>,
  if <code>w == CALL || CALLCODE || DELEGATECALL</code>
- <code>C<sub>SELFDESTRUCT</sub>(S<sub>system</sub>, S<sub>machine</sub>)</code>,
  if <code>w == SELFDESTRUCT</code>
- <code>G<sub>create</sub></code>, if <code>w == CREATE</code>
- <code>G<sub>sha3</sub> + G<sub>sha3word</sub> x ceil(s[1] / 32)</code>,
  if <code>w == SHA3</code>
- <code>G<sub>jumpdest</sub></code>, if <code>w == JUMPDEST</code>
- <code>G<sub>sload</sub></code>, if <code>w == SLOAD</code>
- <code>G<sub>zero</sub></code>, if `w` in <code>W<sub>zero</sub></code>
- <code>G<sub>base</sub></code>, if `w` in <code>W<sub>base</sub></code>
- <code>G<sub>verylow</sub></code>, if `w` in <code>W<sub>verylow</sub></code>
- <code>G<sub>low</sub></code>, if `w` in <code>W<sub>low</sub></code>
- <code>G<sub>mid</sub></code>, if `w` in <code>W<sub>mid</sub></code>
- <code>G<sub><sub>high</sub></sub></code>, if `w` in <code>W<sub>high</sub></code>
- <code>G<sub>extcode</sub></code>, if `w` in <code>W<sub>extcode</sub></code>
- <code>G<sub>balance</sub></code>, if <code>w == BALANCE</code>
- <code>G<sub>blockhash</sub></code>, if <code>w == BLOCKHASH</code>

- where `w` is
  - <code>T<sub>code</sub>[S<sub>machine,pc</sub>]</code>,
    if <code>S<sub>machine,pc</sub> < length(T<sub>code</sub>)</code>
  - `STOP`, otherwise

- where <code>C<sub>mem</sub>(a) := G<sub>memory</sub> x  a + floor(a<sup>2</sup> / 512)</code>

[//]: # (TODO-GX Describe details of C_call, C_selfdestruct, C_sstore.)
with <code>C<sub>CALL</sub></code>, <code>C<sub>SELFDESTRUCT</sub></code> and
<code>C<sub>SSTORE</sub></code> which will be described in the future.


### Execution Environment

The execution environment consists of the system state
<code>S<sub>system</sub></code>, the remaining gas for computation
<code>G<sub>rem</sub></code>, and the information <code>I</code> that the
execution agent provides.  <code>I</code> is a tuple defined as shown below:

<code>I := (B<sub>header</sub>, T<sub>code</sub>, T<sub>depth</sub>, T<sub>value</sub>, T<sub>data</sub>, A<sub>tx_sender</sub>, A<sub>code_executor</sub>, A<sub>code_owner</sub>, G<sub>price</sub>, P<sub>modify_state</sub>)</code>

The execution model defines the function <code>F<sub>apply</sub></code>, which
can compute the resultant state <code>S<sub>system</sub>'</code>,
the remaining gas <code>G<sub>rem</sub>'</code>,
the accrued substate <code>A</code> and
the resultant output <code>O<sub>result</sub></code> when given these definitions.
For the present context, we will define it as follows:

<code>(S<sub>system</sub>', G<sub>rem</sub>', A, O<sub>result</sub>) = F<sub>apply</sub>(S<sub>system</sub>, G<sub>rem</sub>, I)</code>

where we must remember that <code>A</code>, the accrued substate, is defined as
the tuple of the suicides set <code>Set<sub>suicide</sub></code>, the log
series <code>L</code>, the touched accounts
<code>Set<sub>touched_accounts</sub></code> and the refunds
<code>G<sub>refund</sub></code>:

<code>A := (Set<sub>suicide</sub>, L, Set<sub>touched_accounts</sub>, G<sub>refund</sub>)</code>


### Execution Overview

In most practical implementations, <code>F<sub>apply</sub></code> will be modeled
as an iterative progression of the pair comprising the full system state
<code>S<sub>system</sub></code> and the machine state <code>S<sub>machine</sub></code>.
Formally, we define it recursively with a function <code>X</code> that uses an
iterator function <code>O</code> (which defines the result of a single cycle of
the state machine) together with functions <code>Z</code>, which determines
if the present state is an exceptional halted machine state, and
<code>H</code>, which specifies the output data of an instruction if and
only if the present state is a normal halted machine state.

The empty sequence, denoted as <code>()</code>, is not equal to the empty set,
denoted as <code>Set<sub>empty</sub></code>; this is important when interpreting
the output of <code>H</code>, which evaluates to <code>Set<sub>empty</sub></code>
when execution is to continue but to a series (potentially empty) when execution
should halt.

<code>F<sub>apply</sub>(S<sub>machine</sub>, G<sub>rem</sub>, I, T) :=
  (S<sub>system</sub>', S<sub>machine,g</sub>', A, o)</code>
- <code>(S<sub>system</sub>', S<sub>machine,g</sub>', A, ..., o) := X((S<sub>system</sub>, S<sub>machine</sub>, A<sup>0</sup>, I))</code>
- <code>S<sub>machine,g</sub> := G<sub>rem</sub></code>
- <code>S<sub>machine,pc</sub> := 0</code>
- <code>S<sub>machine,memory</sub> := (0, 0, ...)</code>
- <code>S<sub>machine,i</sub> := 0</code>
- <code>S<sub>machine,stack</sub> := ()</code>
- <code>S<sub>machine,o</sub> := ()</code>
- <code>X((S<sub>system</sub>, S<sub>machine</sub>, A, I)) :=</code>
   - <code>(Set<sub>empty</sub>, S<sub>machine</sub>, A<sup>0</sup>, I, Set<sub>empty</sub>)</code> if <code>Z(S<sub>system</sub>, S<sub>machine</sub>, I)</code>
   - <code>(Set<sub>empty</sub>, S<sub>machine</sub>', A<sup>0</sup>, I, o)</code> if <code>w = REVERT</code>
   - <code>O(S<sub>system</sub>, S<sub>machine</sub>, A, I) · o</code> if <code>o != Set<sub>empty</sub></code>
   - <code>X(O(S<sub>system</sub>, S<sub>machine</sub>, A, I))</code> otherwise

where
- <code>o := H(S<sub>machine</sub>, I)</code>
- <code>(a, b, c, d) · e := (a, b, c, d, e)</code>
- <code>S<sub>machine</sub>' := S<sub>machine</sub></code> except
  <code>S<sub>machine,g</sub>' := S<sub>machine,g</sub> - C(S<sub>system</sub>, S<sub>machine</sub>, I)</code>
   - This means that when we evaluate <code>F<sub>apply</sub></code>, we
     extract the remaining gas <code>S<sub>machine,g</sub>'</code> from the
     resultant machine state <code>S<sub>machine</sub>'</code>.

<code>X</code> is thus cycled (recursively here, but implementations are
generally expected to use a simple iterative loop) until either <code>Z</code>
becomes true, indicating that the present state is exceptional and that the
machine must be halted and any changes are discarded, or until <code>H</code>
becomes a series (rather than the empty set), indicating that the machine has
reached a controlled halt.


#### Machine State

The machine state <code>S<sub>machine</sub></code> is defined as a tuple
<code>(g, pc, memory, i, stack)</code>, which represent the available gas, the
program counter <code>pc</code> (64-bit unsigned integer), the memory contents,
the active number of words in memory (counting continuously from position 0),
and the stack contents. The memory contents <code>S<sub>machine,memory</sub></code>
are a series of zeroes of size 2<sup>256</sup>.

For ease of reading, the instruction mnemonics written in small-caps (*e.g.*,
`ADD`) should be interpreted as their numeric equivalents; the full table of
instructions and their specifics is given in the
[Instruction Set](#instruction-set) section.

To define <code>Z</code>, <code>H</code> and <code>O</code>, we define
<code>w</code> as the current operation to be executed:

- <code>w := T<sub>code</sub>[S<sub>machine,pc</sub>]</code> if <code>S<sub>machine,pc</sub> < len(T<sub>code</sub>)</code>
- <code>w := </code>`STOP` otherwise


[//]: # (TODO: #### Exceptional Halting)

[//]: # (TODO: #### Jump Destination Validity)

[//]: # (TODO: #### Normal Halting)

[//]: # (TODO: ### Execution Cycle)



### Instruction Set

[//]: # (TODO: List up the instruction set)

NOTE: This section will be filled in the future.


## Differences from EVM

[//]: # (TODO: Describe more differences between KLVM and EVM)

As mentioned earlier, the current KLVM is based on EVM; thus, its specification
currently is very similar to that of EVM.  Some differences between KLVM
and EVM are listed below.
- KLVM uses Klaytn's gas uints, such as peb, ston, or KLAY.
- KLVM does not accept a gas price from the user; instead, it uses a
  platform-defined value as the gas price.

The Klaytn team will try to maintain compatibility between KLVM and EVM, but as
Klaytn becomes increasingly implemented and evolves, the KLVM specification
will be updated, and there will probably be more differences compared to EVM.

NOTE: This section will be updated in the future.
