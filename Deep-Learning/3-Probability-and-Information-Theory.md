# 2. Probability and Information Theory
1. Probability theory is a mathematical framework for representing uncertain statements. In Artificial intelligence applications, we use probability theory in two major ways:
    1. tell us how AI systems should reason.
    2. theoretically analyze the behavior of proposed AI system.
2. Information theory enables us to quantify the amount of uncertainty in a probability distribution.
## 3.1 Why Probability
1. Three possible sources of uncertainty
    1. Inherent stochasticity in the system being modeled.
    2. Incomplete observability.
    3. Incomplete modeling.
2. Two kind of probability theory
    1. Frequentist probability: the frequencies of events
    2. Bayesian probability: The degree of belief
3. Probability can be seen as the extension of logic to deal with uncertainty.
    1. Logic provides a set of formal rules for determining what propositions are implied to be true or false given the assumption that some other set of propositions is true or false.
    2. Probability theory provides a set of formal rules for determining the likelihood of a proposition being true given the likelihood of other propositions.
## 3.2 Random Variables
1. A random variable 
    1. is a variable that can take on different values randomly.
    2. is just a description of the states that are possible.
    3. is coupled with a probability distribution that specifies how likely each of these states are.
## 3.3 Probability Distributions
1. A probability distribution is a description of how likely a random variable or set of random variables is to take on each of its possible states.
### 3.3.1 Discrete Variables and Probability Mass Functions
### 3.3.2 Continuous Variables and Probability Density Functions
## 3.4 Marginal Probability
## 3.5 Conditional Probability
1. We cannot compute the conditional probability conditioned on an event that never happens.
2. It is important not to confuse conditional probability with computing what would happen if some action were undertaken (intervention query).
## 3.6 The Chain Rule of Conditional Probabilities
1. Chain rule: any joint probability distribution over many random variables may be decomposed into conditional distributions over only one variable.
## 3.7 Independence and Conditional INdependence
1. Independence: $x \bot y$
2. Conditionally: $x \bot y | z$
## 3.8 Expectation, Variance and Covariance
1. Expectation
2. Variance
3. Covariance: how much two values are linearly related to each other.
4. Covariance matrix
## 3.9 Common Probability Distributions
1. Bernulli Distribution
    1. $P(x=1)=\phi$
    2. $P(x=0)=1-\phi$
    3. $P(x=x)=\phi^x(1-\phi)^{(1-x)}$
2. Multinoulli Distribution
    1. Do not usually assume that state 1 has numerical value 1, 
    2. Do not usually need to compute the expectation or variance of multinoulli-distributed random variables
    3. Bernoulli and multinoulli distributions are sufficient to describe any distribution over their domain.
        1. They are particularly powerful
        2. Their domain is simple, they model discrete variables for which it is feasible to enumerate all the states.
3. Gaussian Distribution
    1. $N(x:\mu,\sigma^2)=\sqrt{\frac{1}{2\pi\sigma^2}}\exp\left(-\frac{1}{2\sigma^2}(x-\mu)^2\right)$
    2. $\beta=\sigma^2\to N(x:\mu,\beta^{-1})=\sqrt{\frac{\beta}{2\pi}}\exp\left(-\frac{\beta}{2}(x-\mu)^2\right)$
    3. $N(x;\mu,\Sigma)=\sqrt{\frac{1}{(2\pi)^ndet(\Sigma)}}\exp\left(-\frac{1}{2}(x-\mu)^T\Sigma^{-1}(x-\mu)\right)$
    4. $N(x;\mu,\beta^{-1})=\sqrt{\frac{det(\beta)}{(2\pi)^n}}\exp\left(-\frac{1}{2}(x-\mu)^T\beta(x-\mu)\right)$
    5. In the absence of prior knowledge about what form a distribution over the real numbers should take, the normal distribution is a good default choice for two major reasons.
        1. The central limit theorem shows that the sum of many independent random variables is approximately normally distributed.
        2. Out of all possible probability distributions with the same variance, the normal distribution encodes the maximum amount of uncertainty over the real numbers. We can thus think of the normal distribution as being the one that inserts the least amount of prior knowledge into a model. 
4. Exponential and Laplace Distributions
    1. Exponential: A probability distribution with a sharp point at x=0. 
        1. $p(x;\lambda)=\lambda \textbf{1}_{x\ge0}exp(-\lambda x) $
    3. Laplace: A probability distribution with a sharp point at any arbitrary point $\mu$.
        1. $p(x;\mu,\gamma)=\frac{1}{2\gamma}exp\left(-\frac{|x-\mu|}{\gamma}\right)$ 
5. The Dirac Distribution and Empirical Distribution
    1. Dirac: all the mass in a probability distribution clusters around a single point.
        1. $p(x)=\delta(x-\mu)$
        2. Generalized function is defined in terms of its properties when integrated.
    2. Empirical frequency: 
        1. $p(x)=\frac{1}{m}\sum_{i=1}^{m}\delta(x-x^{i})$
    3. Continuous variables: dirac delta distribution.
    4. Discrete variables: multinoulli distribution, with a probability associated with each possible input value that is equal to the empirical frequency of that value in the training set. 
6. Mixtures of Distributions
    1. On each trial, the choice of which component distribution should generate the sample is determined by sampling a component identity from a multinoulli distirbution.
## 3.10 Useful Properties of Common Functions
1. Logistic sigmoid: $\sigma(x)=\frac{1}{1+\exp(-x)}$
    1. Commonly used to produce the $\phi$ parameter of a Bernoulli distribution because its range is (0,1)
    2. Saturates: when its argument is very positive or negative. meaning that the function becomes very flat and insensitive to small changes in its input.
2. Softplus function: $\zeta(x)=log(1+\exp(x))$
    1. Useful for producing the $\beta$ or $\sigma$ parameter of a normal distribution.
    2. smoothed version of $x^+=\max(0,x)$
## 3.11 Bayes' Rule
1. $P(x|y)=\frac{P(x)P(y|x)}{\sum_{x}P(y|x)P(x)}$
## 3.12 Technical Details of Continuous Variables
1. Measure zero: a set of measure zero occupies no volume in the space we are measuring. 
    1. Any union of countably many sets that each have measure zero also has measure zero. So the set of all the rational numbers has measure zero, for instance.
2. Almost everywhere: a property that holds almost everywhere holds throughout all space except for on a set of measure zero.
    1. Some important results in probability theory hold for all discrete values but hold allmost everywhere only for continuous values.
3. $p_x(x)=p_y(g(x))\left|det\left(\frac{\delta g(x)}{\delta x}\right)\right|$
## 3.13 Information Theory
1. In this book, the key ideas from information theory characterize probability distributions or quantify similarity between probability distributions.
2. The basic intuition behind information theory is that learning that an unlikely event has occurred is more informative than learning that a likely event has occurred.
3. Independent events should have additive information.
4. Self-information: $I(x)=-\log P(x)$
    1. In this book log is used to mean the natural logarithm,
    2. An event with unit density still has zero information, despite not being an event that is guaranteed to occur.
5. Shannon entropy (differential entropy): $H(x)=E_{x\sim P}[I(x)]=-E_{x\sim P}[\log P(x)]$
    1. The Shannon entropy of a distribution is the expected amount of information in an event drawn from that distribution.
    2. It gives a lower bound on the number of bits needed on average to encode symbols drawn from a distribution P.
6. KL divergence: $D_{KL}(p||Q)=E_{x\sim P}\left[\log \frac{P(x)}{Q(x)}\right]=E_{x\sim P}\left[\log P(x)-\log Q(x)\right]$
    1. In the case of discrete variables, it is the extra amount of information needed to send a message containing symbols drawn from probability distribution P, when we use a code that was designed to minimize the length of messages drwan from probability distribution Q.
    2. set P is groundtrue
        1. $D_{KL}(P||Q)\propto P$, Q has high probability where P has high probability.
        2. $D_{KL}(Q||P)\propto 1/P$, Q has low probability where P has low probability.
    3. Minimizing the cross-entropy with respect to Q is equivalent to minimizing the KL divergence. $H(P,Q)=-E_{x\sim P}\log Q(x)=H(P) + D_{KL}(P||Q)$
## 3.14 Structured Probabilistic Models (Graphical Model)
1. Instead of using a single function to represent a probability distribution, we can split a probability distribution into many factors that we multiply together.
2. These factorizations can greatly reduce the number of parameters needed to describe the distribution.
3. Each factor uses a number of parameters that is exponential in the number of variables in the factor.
4. Directed
5. Undirected
    1. Any set of fnodes that are all connected to each other in G is called a clique.
    2. The probability of a configuration of random variables is proportional to the product of all these factors.
6. Graphical representations of factorizations are a language for describing probability distributions.
7. Directed or undirected is not a property of a probability distribution, it is a property of a particular description of a probability distribution, but any probability distribution may be described in both ways.
## New word 
1. fidelity: the quality of being accurate
2. common sense: the ability to think about things in a practical way and make sensible decisions.
3. proposition: an idea or a plan of action that is suggested especially in business.
4. glimpse: start to understand sth