---
title: RSA - A full walk thru.
date: "2021-12-06T22:12:03.284Z"
description: "RSA - A full walk thru."
---

RSA (Rivest-Shamir-Adleman) is a public-key cryptosystem that is widely used for secure data transmission. 
A Public key cryptography, also known as asymmetric cryptography, uses two different but mathematically linked keys:
- Public key : can be shared with everyone.
- Private key: must be kept secret.

## What principle is the RSA based on ? 

It is based on the principle that it is easy to multiply large numbers, but factoring large numbers is very difficult.

```
577 * 719 => 414863   [easy to do]
factors of 414863 ?   [a longer process]
```

For all practical purposes, even computers cannot factor large numbers into the product of two primes, in the same way that factoring a number like 414863 by hand is virtually impossible.

## Keys: 

- The public key consists of two numbers where one number is multiplication of two large prime numbers. 

```
n = p * q , where p & q are the two large prime numbers.
```

A user can then distribute his public key `pq`, and anyone seeking to send a message to the user would encrypt their message using the public key.

- The private key is also derived from the same two prime numbers.

So if somebody can factorize the large number, the private key is compromised. 
Therefore encryption strength totally lies on the key size and if we double or triple the key size, the strength of encryption increases exponentially.

## Key Concepts

In order to understand the inner workings of RSA, let's revist some of the number theory concepts we would have encountered in our college/school years: 

First, let's have a quick look at the followings: 

- `Factors`

Multiplying two whole numbers gives a product. The numbers that we multiply are the factors of the product.  

```
 4 * 8      =     32
_______         _______
factors         product
```

In other words: 

is 4 a factor of 32 ? 
4 is the `divisor` and 32 is the `dividend` here.

```
32 / 4  

results: 
8 as quotient. Q
0 as remainder. R
```
A remainder = 0 tells us that the divisor `4` is a factor of the dividend `32`

* Factors are always whole numbers or integers and never decimals or fractions.

- `Prime numbers`

Thou a pretty straightforward concept, but as you learn to play with it you will see that there are actually fairly sophisticated concepts that can 
be built on top of the idea of a prime number. And that includes the idea of cryptography. 

So, what does it mean to be a prime number ?

A number is a prime if it is a natural number which is divisible by exactly two natural numbers: 
1. First, the number itself.
2. one

Eg. 
1 is not a prime: read more about it [here](https://blogs.scientificamerican.com/roots-of-unity/why-isnt-1-a-prime-number/)
2 is divisible by 1 and by 2 itself and not another natural number. The only even number that is prime.
So on ..

```
The first 25 prime numbers (all the prime numbers less than 100)

2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97
```

- `Relatively Prime`

Two integers are relatively prime when there are no common factors other than 1. This means that no other integer could divide both numbers evenly. Two integers  𝑎,𝑏  are called relatively prime to each other if  gcd(𝑎,𝑏)=1 .

Note: that neither integers need to be prime in order for them to be relatively prime 8 and 15 are both not prime, yet they are relatively prime.

- `GCD`

....

Apart from public key cryptography a few other areas where prime numbers are also used in computing are: 
* [checksums]()
* [hash tables]()
* [pseudorandom number generators]()

### How does the public key cryptography works: 

Rolling pre-credit scene-

l'acteur:  Bob
l'actrice: Alice


<insert> the whole walk thru


Overview of the algorithm, in the enxt section we will break it up into parts and analyze each step

1. To begin, Alice selects two large prime numbers, p and q. Their product will be half of the public key, n=pq.

2. Alice then calculates `ϕ(pq)=(p-1)(q-1)` and chooses a number e relatively prime to `ϕ(pq)`. In practice, e is often chosen to be `2^{16}+1=655372` though it can be as small as 3 in some cases. e will be the other half of the public key.

3. Next, she calculates the modular inverse `d of e modulo ϕ(n).` 
The resulting d is the private key of Alice.

4. Alice distributes both portions of the public key: n and e, but d she keeps it to herself.

## The Algorithm Part 1: Generating the public key

Alice wants to send Bob an encrypted message: 
For the sake of simplicity let `p = 7` & `q = 11`

The implementation of RSA makes heavy use of modular arithmetic, Euler's theorem, and Euler's totient function. 
Let's explore these concepts and some examples to understand as to what is happening under the hood.

### Euler's totient function

Written using the Greek letter phi as `ϕ(n)` and may also be called Euler's phi function.

In number theory, Euler's totient function counts `the positive integers up to a given integer n that are relatively prime to n`.
It is the number of integers `k in the range 1 ≤ k ≤ n` for which the greatest common divisor `gcd(n, k) is equal to 1.`

let n = 9

```
for k in the range 1 ≤ k ≤ 9: positive integers up to 9

k = 1,2,3,4,5,6,7,8,9
```

```
Integers that are relatively prime to 9

gcd(1, 9) = 1 ✅ 
gcd(2, 9) = 1 ✅ 
gcd(3, 9) = 3 ❌
gcd(4, 9) = 1 ✅ 
gcd(5, 9) = 1 ✅ 
gcd(6, 9) = 3 ❌
gcd(7, 9) = 1 ✅ 
gcd(8, 9) = 1 ✅ 
gcd(9, 9) = 3 ❌

Thus, 1,2,4,5,7,8 are relatively prime to 9

ϕ(9) = 6
```

For any prime number x $$ϕ(x) = x - 1$$

```
For eg. ϕ(7) = 6

gcd(1, 7) = 1 
gcd(2, 7) = 1 
gcd(3, 7) = 1 
gcd(4, 7) = 1 
gcd(5, 7) = 1 
gcd(6, 7) = 1
```

Have a look [here](https://www.khanacademy.org/computing/computer-science/cryptography/modern-crypt/pi/euler-totient-exploration)

You can also try out this snippet to confirm the same, insert any valid prime number n

```js
function euclideanGcd(a, b) {
  if (a < b) return euclideanGcd(b, a)

  if (b === 0) return a

  return euclideanGcd(b, a % b)
}

/**
 * @param {number} n 
 */
function eulerPhiFunction(n){
  let counter = 0

  for (let k = 1; k <= n; k++) {
    const isNandKRelativelyPrime = euclideanGcd(n, k) === 1
    isNandKRelativelyPrime && counter++
  }

  return counter
}

const n = 7
const result = eulerPhiFunction(n)

console.log(result) // 6
```

So for a large prime number `n` $$ϕ(n) = n - 1$$

Euler's totient function is a multiplicative function, meaning that if two numbers p and q are relatively prime, then $$ϕ(pq) = ϕ(p)ϕ(q)$$

```
p = 7 & q = 11

ϕ(p) = p - 1 => ϕ(7) = 7 - 1 => 6
ϕ(q) = q - 1 => ϕ(11) = 11 - 1 => 10

ϕ(pq) = ϕ(p)ϕ(q) 
ϕ(7 * 11) = ϕ(7)ϕ(11)
ϕ(77) = 6 * 10
ϕ(77) = 60
```

Wait, why do we need this ? 

In order to choose a number `e` relatively prime to `ϕ(pq)`

```
gcd(e, ϕ(pq)) === 1
gcd(e, 60) === 1

eg. 

gcd(7, 60) = 1
gcd(13, 60) = 1 
.
.
e = 7,13 ... for ϕ(pq) = 60
```

### Summing up part 1

So far we have two parts to the algorithms which make up for the public key i.e 

- `n` -> `n = pq`
- `e` -> `ϕ(pq)` which helps choose a number `e` such that `gcd(e, ϕ(pq)) === 1`

## The Algorithm Part 2: Generating the private key

1. Modulo Inverse
2. Bezout's identity
3. Euler's extended algo to calc modulo inverse and gcd (Diophantine equations)

modular inverse `d of e modulo ϕ(n).` 

<!-- Now that the public and private keys have been generated, they can be reused as often as wanted. To transmit a message, follow these steps: -->

Since the implementation of RSA makes heavy use of modular arithmetic, Euler's theorem, and Euler's totient function. 

The simplest linear Diophantine equation takes the form ax + by = c, where a, b and c are given integers.





References: 

1. https://www.geeksforgeeks.org/rsa-algorithm-cryptography/
2. https://brilliant.org/wiki/rsa-encryption/#applications-and-vulnerabilities
3. https://www.khanacademy.org/math/cc-fourth-grade-math/imp-factors-multiples-and-patterns/imp-prime-and-composite-numbers/v/prime-numbers
4. Image of numbers from: https://www.national5.com/integers/
5. https://crypto.stackexchange.com/questions/5715/phipq-p-1-q-1