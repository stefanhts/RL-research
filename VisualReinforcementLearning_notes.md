# Visual Reinforcement Learning with Imagined Goals

##Questoins
1. What is a goal-condtioned skill?
2. What is the reward function is 4.2
3. What is the latent space?

## Intro
Standard framework involves learning policies specific to individual tasks, defined by hand-speified reward funcitons. The goal of this paper is to find a way to allow agents to set their own goals and pursue them on their own.

**Proposal:** Incorporate usupervised representation learning into goal-conditioned policies. By learning to reach random goals sampled from the latent varibale model, the goal-conditioned policy learns about the world and can be used to achieve new, user-specified goals at test-time.
The work contributes a framework for learning gerneral-purpose, goal-conditioned policies that can achieve goals specified with target observations. This can be called Reinforcement learning with imagined goals **(RIG)**. 

### Previous 
Prior works can efficiently learn a variety of behavious such as grasphing, pushing, navigation, and other manipulation tasks, but they make assumptions that limit their applicability to training general purpose robots. 
Basically, previous approaches are not very effective when it comes to expanding the skillset and especially goal-conditioned skills. No prior method uses model-free RL to learn policies conditioned on a single goal  image with sufficient efficiency to train directly on real-world robotic systems without acces to ground-truth state or reward information during training.

### The method
USe a goal-conditioned value function in order to solve more general tasks. Samples are retroactively relabeled in the replay buffer with goals sampled from the latent representation. Almost any goal will have no reward signal, so we they take random goals from their learned space to use as goals.
Unsupervised learning has been used to acquire better representations for RL. Other methods use the learned reoresentation as a substitute for the state for the policy,  but need info such as the ground truth reward function based on true state during training time, expert trajectories,... 
This proposed method learns to generate goals and use the learned  representation to create a reward function without any extra information. Method is based on variational autoencoders.

### Background
***Goal-conditioned RL:***
the goal is to learn a policy that maximizes expected return under the current policy and environment  dynamics. Standard model-free RL learns policies that achieve a single task. To obtain a policy that can accomplish a variety of tasks, we use a goal-conditioned policy and reward, optimizing the exprected return with respect to a goal distribution. We minimize the Bellman error (1) to parameterize an aprox Q-function.

***Variational Autoencoders:***
Have been shown to learn latent representations of high dimensional data. It is made of an encoder which maps states to latent distributions, a decoder which maps latents to distributions over states. The coders are trained to optimize eqn (2)

### Sample-efficient RL with Learned representations
The resulting policy need to learn perception and control whihc is a challenge.  We can embed the state and goals into a latent space X using an ecoder e to obtain e(s<sub>t</sub>) and goal z<sub>g</sub>. To learn the state and goal space we train a VAE by executing a random policy and collection state observations. We use the mean of the encoder as the state encoding.
After the VAE is trained, a GCQF (goal-conditioned Q-function) is trained, as well as its corresponding policy. The policy is trained using the reward function above. The policy and q-function operate in the latent space. The goal stated previously is encoded and input to the policy. 
The policy will improve and visit parts of the state space that VAE has not trained on, giving wack encodings, the VAE has to be fine tuned using th erandomly generated state observations and the state aboservations collected during exploration.

***Reward Specs:***
TThe training of the gcvf requires defining  a goal conditioned reward r(s,g). Eqn. 3 is used as apossible metric to  reflect similarity between state and goal. Minimizing the squared distance in the latent space is equiv to rewarding each state that maximizes the probability of the latent goal. Specifically they use A=I, the reasoning for which is unclear to me...

***Improving Sample Efficiency with Latent Goal Relabeling:***
The VAE is used to relabel goals. By artificially generating and recomputing rewards, we can convert a single transition into potentially infinitely many valid training datums. Instead of actually generating goals, we generate latent goals and rewards by sampling from the VAE prio p(z). Eqn. 3 is then used to recompute rewards. By relabeling/recreating data we obtain much more training data. 

***Automated Goal-Generation for Exploration:***
We sample from the distribution to obtain plausible goals from the prior, then give to the policy to collect data.

***Algorithm Summary***
Data collected using exploration policy, train B-VAE, fit prior p(x) to latent encodings
for(...)
> sample latent goal from prior
> sample initial state
> for(...)
> > get action 
> > get next state
> > store into replay buffer
> > sample transition
> > encode
> > replace zg with z'g
> > compute new reward
> > rminimize eqn. q
> for(...)
> > for(...)
> > > sample future state
> > > store
> fine tune B-VAE every K episods

## Visual RL with physical robots
ROG  is a straightforward algorith for real physical systems. Training times are made manageable, image based rewards through learned representation frees from manually designing reward functions. Can be done well with *only* the input from a camera. About an hour of real-world interaction is required to solve visual reaching. 

## Discussion
This paper presents a new algorithm that can efficiently solve goal-conditioned, vision-based tasks without acces to any ground truth state or reward functions. A model is trained that can create it's own data and reward, allowing the model to train itself further.
