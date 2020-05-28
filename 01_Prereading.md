## 1.1 A bit of context

Okay, so there are a lot of places we could start from, the beginning of the universe being one of them. But let's save ourselves a good deal of time by picking a starting point.

- We're at the start of the [NISQ](https://arxiv.org/pdf/1801.00862.pdf) era. That stands for Noisy Intermediate-Scale Quantum, which is... pretty much what it is. We're at a place where:

  - we can build quantum devices with 50- several 100 qubits.
  - the qubits are still in quite noisy environments, meaning we can't run very long computations without the state of our system being spoiled.

- Currently there are a lot of reasons we'd want to know the ground state energy of a quantum system. Knowledge of the ground state helps us calculate/approximate a lot of other interesting properties. For instance, in quantum molecular chemistry we can use the ground state to calculate reaction rates, binding strengths or molecular pathways ([source](https://www.mustythoughts.com/variational-quantum-eigensolver-explained)).

- Classical computers have a very hard time at coming up with exact solutions for the aforementioned problem, the main reason being that the size of the quantum system's Hamiltonian scales exponentially with the number of qubits.

- There are quantum computing algorithms designed to solve related problems, but they still aren't feasible on today's NISQ devices.

## 1.2 So how does VQE fit in?

Right, so we're in a situation where we want to find the ground state energy of a quantum system, but for practical applications we can't do it with a classical computer, and a quantum computer which could solve the problem doesn't exist yet.

But what if we try combining classical computing algorithms with short quantum computations (remember that noise prevents us from running long quantum computations)? Well, that's where VQE fits in.

## 1.3 A quick summary of what VQE does

To sum it up, VQE starts from the premise that we have a quantum system with a known Hamiltonian, and gets an upper bound approximation for the ground state energy via an optimisation algorithm. The optimisation steps are calculated by the classical computer, whereas the evaluation of the objective function (ie. the energy) is done by the quantum computer.

Let me just go through that again in a little more detail...

#### We have a quantum system with a known Hamiltonian

Remember that the Hamiltonian is an operator which, among other properties, has a matrix representation with eigenvalues equivalent to the various energies of the quantum system (_if that caught you off guard, I'd recommend you brush up on the Hamiltonian and postulate 2 of quantum mechanics_).

Keep in mind that the size of a Hamiltonian's matrix representation scales with an $n$ qubit system as $2^n$. It's pretty safe to say then that 50-100 qubit systems (like NISQ devices) are pretty much impossible to represent exactly on a classical computer.

_Pause... Do the back-of-the envelope calculation. A 50 qubit system has a Hamiltonian with $2^100$ entries. If we were to matrix-multiply with the corresponding $2^50$ long state vector that's $2^100$ multiplication operations (plus sums). An NVIDIA Tesla V100 GPU can get to around 10^14 floating point operations (FLOPS) per second. Plug that all into your favorite calculator and you get that it would take several hundreds of thousands of years. You can do a similar exercise to figure out how many of your hardrive it would take to actually represent the Hamiltonian. Unpause..._

#### We want to get the ground state energy

The ground state is the lowest energy state of the quantum system and is the same as the lowest eigenvalue of Hamiltonian. Analytically, you would just eigendecompose the Hamiltonian matrix to get all the eigenvalues and associated eigenvectors. But as mentioned, this becomes impossible with 50~100+ qubit systems.

#### A quantum computer evaluates the objective function in our search space

Our "search space" is the space of all possible states of our quantum system, and the "objective function" is the energy associated with a given state. VQE makes use of a quantum system to actually measure the energy of a prepared state. Cool! So now we have a feasible way of actually computing an eigenvalue (**this is meant to be an Aha! moment by the way**).

#### We're actually getting an upper bound for the ground state energy

Because we're actually doing quantum measurements to get the ground state energy, we can only expect to get measurement outcomes at least as high as the ground state energy because... well that's what "ground state" is by defintion. And actually, if we're working with a practical problem which is analytically intractable, we'll never truly know it even if we do get to the exact ground state. So that's why it's called an "upper bound".

#### We use a classical computer for computing the optimisation steps

Every time we prepare a state and make a measurement, we get a datapoint: state $|\psi⟩$ has energy $E$. We can use classical optimisation algorithms to propose a next state to try based on the data we already have. Furthermore, that algorithm may be able to calculate some degree of certainty that we are near the real ground state.

You might find this diagram useful in summing up the process

![](./figures/vqe-simple-schematic.png)

Now head on to the tutorial notebook to dive into a VQE example!