---

layout: post
title:  Deep learning and Cyber-Security : Part 1/5
date:   2020-02-10 16:05:55 +0300
image:  wp2406516.jpg
tags:   [Deep-Learning,Computer-Science, Cryptography, Code Breaking]

---

This is the first article of our serie : Deep learning and Cyber-Security. We will try and explain how Machine learning, Deep learning, and Artificial intelligence algorithms can intersect with cyber-security. Our question today : Can Deep learning be a good alternative for protrecting communication ?

Neural networks have reached state-of-the-art results in a great diversity of tasks that were impossible to treat computationally just a few years ago. Attention models have drastically improved NLP. Deep LSTM networks changed time-series forecasting performance, and Graph Convolutional networks became a new standard in a wide range of tasks related to interconnected data (Link prediction, Node classification, Representation learning). 

Nevertheless, Deep learning applications in Cryptology -Cryptography, and Cryptanalysis- remain marginal and the classical neural network learning framework does not seem to be a great candidate for solving Cryptology problems. Recently, Generative adversarial Neural Networks (GANs) have attracted wide attention for their ability to generate efficiently unstructured data such as texts and artworks. More than toys algorithms, GANs are a beautiful intersection between Game Theory and Deep Learning. In this article, we will introduce Adversarial Neural Cryptography.

 
## Mathematical foundations of modern cryptography

Any type of data that can be read and understood without any special treatment is called *plaintext*. The methods we use to convert it into *cyphertext* is called encryption, and the set of rules or the equations we use to go convert *cyphertext* to *plaintext* are called decryption. While cryptography is the science of securing data, cryptanalysis is the science of analyzing and breaking secure communication. What are the building blocks of modern cryptography and cryptanalysis? There are three major differences between *Classic Cryptography* and *Modern Cryptography* :

* Classic cryptography manipulates gross data. Modern cryptography only uses bit sequences and perform computational tasks on these sequences.

* *Security through obscurity* **VS** *Open-Source mathematical algorithms*: While classical cryptography requires to protect the whole crypto-system, modern cryptography leaves public the algorithm used for ciphering and only keep private a reduced number of sensitive information (typically private-keys).

* Consequently, any agent using classical cryptosystem need to know the whole cryptosystem while modern cryptography requires you to possess the secret key only.

In a word, modern encryption systems are designed around cryptographic random number generators, and their output is designed to be statistically indistinguishable from true randomness. The building block of these modern encryption system are **prime numbers**. Indeed, most modern computer cryptography works by using the prime factors of large numbers. Finding those factors is technically only a matter of time, but it’s a matter of so much time that we generally describe the *inverse problem* as computationally infeasible. The best super-computer could work on a 256-bit decryption problem for longer than the current age of the sun.


## GANs : Adversarial framework

Before going through the implementation and the full description of our learning framework, let's refresh our minds on GANs :
We train them using a Zero-Sum game set-up between a generator and a discriminator. The generator tries to generate data that fits the distribution of the training set in order to trick the discriminator. The discriminator distinguishes between real and synthetic data. That way we can build an accurate generative model as well as deep-learned features for classification tasks. 

![](image_gan.png)

The discriminator looks at real samples and generated ones separately. It distinguishes whether the input comes from the generator or the actual training sample. The output $D(X)$ is the probability that the input $x$ is not synthetic,  i.e., $P$(class of input = real sample).

The discriminator is trained just like a deep network classifier. If the input is real, the actual output should be $D(x)=1$, and $0$ if the sample is synthetic. Through this process, the discriminator identifies features that contribute to the real sample, and then possibly learn the more in-depth features useful for future classification using the same type of data.
On the other hand, we want the generator to trick the discriminator by matching exactly the training sample probabilistic distribution. 

Both networks are trained in alternating steps and constrain them into a zero-sum game competition to reach the optimal result. Ideally, the discriminator identifies the details that make the actual sample authentic, and the generator creates data points that the discriminator can't distinguish. The GAN model eventually converges and can produce natural-looking images or intelligible texts. 

## Basic Principles of Adversarial neural cryptography

Adversarial neural cryptography is built upon the same framework as classical cryptography, with two agents -here they are actually neural networks- trying to communicate securely over a public channel, and one attacker eavesdropping the communication. Note that the full algorithm uses for encryption is public and that the only secret information is the private key $K$ used for computation.

Thus, suppose Alice and Bob want to communicate securely over a public channel, and Eve is an attacker eavesdropping on the communication. For symmetric encryption, we may assume Alice and Bob both have a common secret key. Eve can capture all the encrypted messages Alice sends to Bob but does not have access to the secret key. 

The objective of Alice and Bob is not only to both secrecy and integrity. They have to transmit to each other a fully-encrypted message that will be intelligible after decryption.

![](gan_f.png)

GAN framework can be written as a Zero-Sum Game. In this case, it means that Alice and Bob not only have to defeat a vanilla adversarial neural network but the best version of Eve after training. Alice and Bob **have to maximize the reconstruction error of Eve** and at the exact same time  **minimize the reconstruction error of Bob**. The zero-sum game optimization problem can be written as :

$L_{AB}(θ_A,θ_B) = L_B(θ_A, θ_B) − L_E(θ_A, O_E(θ_A))$ 

* $L_E(θ_A, θ_E)$ is Eve's loss in reconstruction , $L_E(θ_A, O_E(θ_E))$ is the optimal reconstruction value.

* $L_{AB}(θ_A,θ_B)$ is the loss function for Alice and Bob.

This combination reflects that Alice and Bob want to minimize Bob’s reconstruction error and to maximize the reconstruction error of the “optimal Eve,” the objective of our optimization problem  is  to find :

$(O_A, O_B) = argmin_{(θ_A,θ_B)}(L_{AB}(θ_A, θ_B))$

Note that we generally do not find a global minimum solution and that there may be several equi-optimal solutions. As for classical GAN, the training starts with random initialization. At this stage, one can argue that the learning framework and optimization problems are extremely simple. The main goals of this article and this implementation are more to give insights about possible communications between the neural network. Indeed, it seems necessary to tweak loss function to integrate a *pure-luck* scenario. Here is an example of a learning curve using Convolutional Neural Networks. The adversarial learning framework is written as Zero-Sum Game, thus it is not interesting at all to train models such as attackers are always fooled at it would be sufficient to inverse all the results to obtain an unbeatable attacker. 

## Note on implementation and results 

For their implementation, Google researchers used Tensorflow-based neural network and ADA optimizier with a learning rate of 0.0008 and actually manage to protect the communication between the two agents. Their algorithm run on GPU using mini-batch size ranging from 256 to 4096 entries. Most classification problems use adaptive learning rate, making it smaller once the performance of the model reaches a plateau. Here, the goal is to secure a message of a certain length and to **learn VS an attacker**. Loss functions are designed so the intetegrity of the message is preserved and attacker does not reach better performance than a random guessing. Here are the learning curves for 20 epochs, a batch size of 512, and RMSProp(0.01) optimizer.

![](loss.png)


Loss functions of attacker decreases over the time and the communication is not protected. Changing Batch Size and optimizer and made the whole process insecure and the attacker able read our 16-bits message. 

## Conclusion and Opening

No one said Adversarial cryptography would never be secure, as we poorly trained, optimized, and questioned our neural networks. However, it seems adversarial neural cryptography would need more research and test before being implemented on a large scale and be a credible alternative to AES and RSA algorithms. As pros, deep learning algorithms are, by nature, tough to interpret and explain, and this characteristic could be a helpful asset for protecting communication using neural networks.
In our next article, we will describe the most common forms of attacks in 2019 and 2020 and see how modern deep learning algorithms -especially natural language processing tools- can be used by black-hat hackers.

[Code available in this GitHub repository.](https://github.com/quantrack/qt_blog-neural_cryptography)
