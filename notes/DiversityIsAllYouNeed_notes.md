# [DIAYN - "Diversity is all you need"] (https://arxiv.org/pdf/1802.06070.pdf)
> A method for learning skills without a reward function by maximizing an information theoretic objective
> using a maximum entropy policy.

# *Questions*
* What is a discriminator?

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

3 distinctions allow the methods in the paper to tasks more complex than gridworlds. MAximum entropy policies force the sklls to be diverse. Including entropy results in the RL objecive results in the skills being maximum entropy in aggregate. 2nd the prior distribution is fixed over skills rather than learning them. 3rd discriminator looks at every state, which provides additional reward signal.

The goal is minimal supervision to improve efficiency and allow the technique to be applied to any problemset.

Previous approach has been to use intrinsic motivation objective to learn a *single* policy, while the idea here is an objective for learning *many*, diverse policies.

## How it works
### 3 main ideas
1. skills should be useful and should visit different state, hence being distinguishable.
2. USe states , not actionis, to distinguish skills, because we don't care what actions take place.
3. Exploration should be encouraged, to allow the learning of the most diverse skills as possible

### Information theory 
*S*, *A* represent states and variables (random). Z ∼ p(z) is a latent variable which the policy is conditioned on. teh  policy on Z is a "skill", I, H refer to mutual information and Shannon entropy.**(The eqns are on pg 3+ starting with eqn. (1) refer back if necessary)** 
Basically, the equations are maximizing the entropy and then the skill, z, is infered from the current state.  Each skill should act randomly. Giving objective *F*.

### Implementation
DIAYN is implemented using a soft actor critic, learning a policy π<sub>θ</sub>(a | s, z) which is conditioned on the variable z. The SAC maximized the entropy over action. During unsupervised learning, a skill is sampled at the start of the episode and act according to the skill throughout the pisode. The agent is rewarded for visiting states that are easy to discriminate, while the discriminator is updated to bettr infer the skill z from the states.

### Stability
Unlike previous methods, DIAYN forms a coop game which avoids instabilities of saddle-point formulations. It is empirically found to be robust to random seed, ie. it is a reliable method that does not depend on starting conditions.

## Experiments
### Questions
1. What skills does DIAYN learn?
> Skills such as navigating 2D space, mountain car, etc. The agent learned skills to solve the task without rewards, and also learned multiple distinct skills for solving the task.
2. How does distribution of skills change during training?
> Skills become increasingly diverse diverse as  the agent trains, some skills corespond to small reward, while others to large.
3. Does discriminating on single states restrict learning to visting disjoint sets of states?
> The alg favors skills that don't overlap, but skills are allowed to overlap. 
4. How does it differ from (VIC)?
> The key difference is not learning the prior p(z). DIAYN does not learn as quickly, but has a much wider set of skills which allows it to be more applicable to diff things.

### Harnessing skills
This allows us to pivot to things such as imitation learning and easily select the correct skill

### Accelerating learning
Skills can easily be adapted to solve desired tasks, after the initial tasks have been learned.

5. Can learned skill  sbe used to maximize task reward
> Individual skills can be altered and fine tuned, which allows each skill to become very efficient.   
### Heirarchical RL
DIAYN discovers diverse and discrete skills which form the building blocks of HRL.

6. Are skills discovered useful for HRL?
> A higher level agent learns to choose skills.

7. Leveraging prior knowledge
> The number of possible skills grows with task complexity. Incorporating prior knowledge can aid in  discovering useful skills and boost performance on the task.It is easy to add in supervision if wanted, which causes a better performance.

8. Can learned skills be used to imitate an expert?
> Learned skills can be used to follow expert demonstrations. Whether hard coded, or human controlled. The learned discriminator is used to estimate which skill  was likely to do the desired outcome. This can be optimized as shown.

## Conclusion
DIAYN is a method for learning skills without a reward function. It learns diverse skills for complext tasks and often solves with a learned skill without receiving any task reward. It can make learning a task easier by replacing the task's complex action space with a useful set of skills. It can be combined with other methods for augmentingthe observations space and reward funciton. IT can also possibly learn more efficiently from human preferences by having humans select among learned skills. 


# Things to look into
1. code for eqn 1., take notes on eqn. to code overlap
2. find what the learned descriminator uses 3.1
3. 
