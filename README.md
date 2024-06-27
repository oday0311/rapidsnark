# rapidsnark

rapid snark is a zkSnark proof generation written in C++ and intel assembly. That generates proofs created in [circom](https://github.com/iden3/circom) and [snarkjs](https://github.com/iden3/snarkjs) very fast.

## Dependencies

You should have installed gcc, cmake, libsodium, and gmp (development)

In ubuntu:

````
sudo apt-get install build-essential cmake libgmp-dev libsodium-dev nasm
````

## Compile prover in standalone mode

````sh
npm install
git submodule init
git submodule update
npx task createFieldSources
npx task buildProver
````

## Compile prover in server mode

````sh
npm install
git submodule init
git submodule update
npx task createFieldSources
npx task buildPistache
npx task buildProverServer
````

## Building proof

You have a full prover compiled in the build directory.

So you can replace snarkjs command:

````sh
snarkjs groth16 prove <circuit.zkey> <witness.wtns> <proof.json> <public.json>
````

by this one
````sh
./build/prover <circuit.zkey> <witness.wtns> <proof.json> <public.json>
````

Note that the first two arguments are the input files whereas the last two correspond to the output files. It takes the zkey (`circuit.zkey`) and a witness (`witness.wtns`) as inputs, and outputs a proof (`proof.json`) and the public inputs (`public.json`).

## Launch prover in server mode

In server mode, the prover also compiles the inputs to generate a witness.

If your circuit's name is `circuit.circom`, then you have to generate the C++ binaries using circom ([link from circom docs](https://docs.circom.io/getting-started/computing-the-witness/#computing-the-witness-with-c)). In the end, you should have two files `circuit` and `circuit.dat`.   【what is circuit.dat come from, can anyone give a detail?】

To launch the server, set two environment variables:
1. `ZKEY`: Pointing to the zkey file
2.  `WITNESS_BINARIES`: Pointing to the folder in which `circuit` and `circuit.dat` are present

and run
````sh
./build/proverServer
````

Note 1: Compared to [iden3's rapidsnark server](https://github.com/iden3/rapidsnark), ours is simpler. Their server was designed to work with multiple zkeys, but ours only handles one. However, we found their code to be buggy in how it handles multiple simultaneous requests.

Note 2: Be careful when setting the log level, e.g., DEBUG logs could contain PII. The default is set to INFO.

## Benchmark

This prover uses intel assembly with ADX extensions and parallelizes as much as it can the proof generation. 

The prover is much faster that snarkjs and faster than bellman.

[TODO] Some comparation tests should be done.


## License

rapidsnark is part of the iden3 project copyright 2021 0KIMS association and published with GPL-3 license. Please check the COPYING file for more details.
