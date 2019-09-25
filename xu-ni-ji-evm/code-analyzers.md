# Code Analyzers



### Code Analyzers

* [Echidna](https://github.com/trailofbits/echidna)
  * A fuzzer on EVM that also takes Solidity input
  * Able to fuzz a program with sequences of multiple transactions
* [MAIAN](https://arxiv.org/abs/1802.06038)
  * An automatic tool that detects trace vulnerabilities \(Greedy, Prodigal and Suicidal\) with depth-first search of symbolic execution of multiple invocations
* [Mythril](https://github.com/b-mueller/mythril)
  * A blockchain exploration tool that indexes all contracts on the network, containing a disassembler, an ABI function detector and a control flow analyzer
  * Comes with a [--fire-laser option](https://hackernoon.com/crafting-ethereum-exploits-by-laser-fire-1c9acf25af4f)
  * Powered by [laser-ethereum](https://github.com/b-mueller/laser-ethereum)
* [porosity](https://github.com/comaeio/porosity)
  * A reverse enginering tool, a disassembler, an ABI function detector and a decompiler that also highlights vulnerabilities
* [Manticore](https://github.com/trailofbits/manticore)
  * A symbolic execution engine that can generate inputs to cover codepaths \([asciicast](https://asciinema.org/a/154012)\), which also comes with a Python API
* [evmdis](https://github.com/arachnid/evmdis)
  * A disassembler for EVM code
* [ethersplay](https://github.com/trailofbits/ethersplay)
  * An EVM plugin for [Binary Ninja](https://binary.ninja/)
* [Securify](http://securify.ch/)
  * A tool that strives to achieve no false-negatives
  * The implementation seems not public as of now
* [Oyente](https://github.com/melonproject/oyente)
  * An automatic EVM code analyzer based on symbolic execution and [Z3](https://github.com/Z3Prover/z3) SMT solver
* [Dr. Y's Ethereum Contract Analyzer](http://dry.yoichihirai.com/)
  * A symbolic executor for EVM code

