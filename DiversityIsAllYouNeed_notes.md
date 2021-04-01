# DIAYN - "Diversity is all you need"
> A method for learning skills without a reward function by maximizing an information theoretic objective
> using a maximum entropy policy.

## Deep Reinforcement Learning (RL)
> shown to be effective for a wide range of reward driven skills, including pla-
> ying games, controlling robots, and navigating complex environments

## Applications of learning skills without rewards
 * If the rewards are sparse there is effectively no reward until the agent randomly reaches a goal. Skills learned without reward can serve as primitives for hierarchical RL, thus making it develop faster.
 * It is free to interact with the surroundings, but it rewards require human interaction. 
 * It is difficult to determine what tasks an agent should learn, unsupervised can provide answers to that.
 * Very difficult problem. A skill is a latent-conditioned policy that alters the state of the environment in a consisten way.
 * The goal is unknown, so in order to learn the most effective skills we want to learn as many skills as we can. There needs to be a learning objective. 
 * A simple objective based on mutula information can allow agents to discover skills, useful for hierarchical reinforcement learning and imitation learning amoung others.

## Hypothesis
In order to acquire skills that are useful, skills must be trained to **maximize coverage over the set of possible behaviors** Some skills could be useless, and some vital. We also want to learn skills that are as **diverse** as possible. Distinguishable states can still be essentially identical.

## 5 Parts:
1) Method for learning useful skills without rewards
2) Simple exploration objective results in the emergence of diverse skills
3) Simple method for using learned skills for hierarchical RL 
4) How discovered skills can be adapted to solve new tasks
5) How skills can be used for imitaion learning

## Previous Work
Learned skills have maximized a single, known, reward function by jointly learning a set of skills and a meta-controller. "Bad" options are never chosen and thus never improved upon, potentially preventing substantial improvements. By not training with rewards, the agent can learn task-agnostic and avoid the issue of reward design. 

Recent work applied tools from info theory to skill discovery. The mutual information between states and actions are used as a  notion of empowerment for an intrinsically motivated agent. This paper focuses on maximizing the mutual information between states and *skills* which can be interpreted as maximizing the empowerment of a hierarchical  agent whose actions pace is the set of skills. (Creates an agent which can choose between skills).

3 distinctions allow the methods in the paper to tasks more complex than gridworlds. MAximum entropy policies force the sklls to be diverse. Including entropy results in the RL objecive results in the skills being maximum entropy in aggregate. 2nd the prior distribution is fixed over skills rather than learning them.r
