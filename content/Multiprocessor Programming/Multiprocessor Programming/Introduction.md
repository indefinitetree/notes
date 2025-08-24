## Shared Objects and Synchronization:
Find all primes between $1$ and $10^{10}$ , using a parallel machine that supports ten concurrent threads. This machine is rented by the minute, so the longer your program takes, the more it costs.

Equal ranges of inputs to each thread does not necessarily produce equal amounts of work. A more promising way to split the work among the threads is to assign each thread one integer at a time. When a thread is finished with testing an integer, it asks for another. We have something called a shared counter, an object that encapsulates an integer value

```
Counter counter = new Counter(1) ; // shared by all threads

void primePrint {
	long long int i = 0 ;
	long long int limit = pow(10 , 10) ;
	while(i < limit){
		i = counter.get() ; // Get counter value
		counter.increment() ; // Increment counter value
		if(isPrime(i)){
			print(i) ;
		}
	}
}
```

Modern multiprocessor hardware provides special read-modify-write instruction that allows threads to read, modify and write a value to memory in one atomic hardware step (indivisible). For the Counter object, we can use such hardware to increment the counter atomically.

We can also provide such atomic behavior by guaranteeing in software that only one thread executes the read-and-write sequence at a time. The problem of making sure that only one thread at a time can execute a particular block of code is called the mutual exclusion problem, one of the classic coordination problems in multiprocessor programming.

## A Fable
Alice and Bob are neighbors, and they share a yard. Alice owns a cat and Bob owns a dog. Both pets like to run around in the yard, but (naturally) they do not get along. After some unfortunate experiences, Alice and Bob agree that they should coordinate to make sure that both pets are never in the yard at the same time.

Each one sets up a flag pole, easily visible to the other. When Alice wants to release her cat, she does the following
1. She raises her flag
2. When Bob's flag is lowered, she unleashes her cat
3. When her cat comes back, she lowers her flag

Bob's behavior is a little more complicated

1. He raises his flag
2. While Alice's flag is raised
	1. Bob lowers his flag
	2. Bob waits until Alice's flag is lowered
	3. Bob raises his flag
3. As soon as his flag his raised and hers is down, he unleashes his dog.
4. When his dog comes back, he lowers his flag.

On an intuitive level, this works because of the following flag principle. If Alice and Bob each
1. raises his or her own flag, and then
2. looks at the other's flag,
then at least one will see the other's flag raised and will not let his or her pet enter the yard.

To prove that the pets will never be in the yard together, assume by way of contradiction that there is a way the pets could end up in the yard together. Consider the last time Alice and Bob each raised their flag and looked at the other’s flag before sending the pet into the yard. When Alice last looked, her flag was already fully raised. She must have not seen Bob’s flag, or she would not have released the cat, so Bob must have not completed raising his flag before Alice started looking. It follows that when Bob looked for the last time, after raising his flag, it must have been after Alice started looking, so he must have seen Alice’s flag raised and would not have released his dog, a contradiction.

This kind of argument by contradiction shows up over and over again, and it is worthwhile spending some time convincing ourselves why this claim is true. It is important to note that we never assumed that “raising my flag” or the “looking at your flag” happens instantaneously, nor did we make any assumptions about how long such activities take. All we care about is when these activities start or end.

### Properties of Mutual Exclusion:
To show that the flag protocol is a correct solution to Alice and Bob's problem, we must understand what properties are required of a solution, and then show that they are met by the protocol.

We proved that the pets are excluded from being in the yard at the same time, a property we call mutual exclusion. This is unlikely to satisfy their pets.

Here is another property of central importance. First, if one pet wants to enter the yard, then it eventually succeeds. Second, if both pets want to enter the yard, then eventually at least one of them succeeds. We consider this deadlock-freedom property to be essential.

Another property of compelling interest is starvation-freedom: if a pet wants to enter the yard, will it eventually succeed? Here, Alice and Bob's protocol performs poorly. It is possible that Alice's cat uses the yard over and over again, while Bob's dog becomes increasingly uncomfortable.

The last property of interest concerns waiting. Imagine that Alice raises her flag and for some reason she falls sick for a week. The problem is that Bob cannot use the yard for the whole week according to the protocol as Alice hasn't lowered here flag. If Alice is delayed, then Bob is also delayed (even if there is no good reason). 

The question of waiting is important as an example of fault-tolerance. The mutual exclusion problem, by its very essence, requires waiting: No mutual exclusion protocol avoids it, no matter how clever. 

### The Moral:
Two kinds of communication occur naturally in concurrent systems:
1. Transient communication requires both parties to participate at the same time.
2. Persistent communication allows the sender and receiver to participate at different times.

Mutual exclusion requires persistent communication. In modern operating systems, one common way for one thread to get the attention of another is to send it an interrupt.


## Harsh Realities of Parallelization:
In an ideal world,  upgrading from a uniprocessor to an n-way multiprocessor should provide about an $n$-fold increase in computational power. In practice, this never happens. The primary reason for this is that most real-world computational problems cannot be effectively parallelized without incurring the costs of inter-processor communication and coordination.

Amdahl's Law: Captures the notion that the extent to which we can speed up any complex job is limited by how much of the job must be executed sequentially.

Define the speedup $S$ of a job to be the ratio between the time it takes one processor to complete the job versus the time it takes $n$ concurrent processors to complete the same job. Amdahl's Law characterizes the maximum speedup $S$ that can be achieved by $n$ processors collaborating on an application, where $p$ is the fraction of the job that can be executed in parallel.

Overall, the parallelized computation takes time: $1 - p + \dfrac{p}{n}$  ($1-p$ for sequential and $\dfrac{p}{n}$ for parallel part)

Amdahl's Law says that speedup $S$ is equal to 
$$
S = \dfrac{1}{1 - p + \dfrac{p}{n}}
$$

