# Eqn 1 and it's code

## The RLAlgorithm File:
This contains the actual training code. This is where the agent is given data and chooses an action, the loss is calculated and the alg learns this all occurs in _train()

## DIAYN._sample_z():
> Samples z from p(z) using the probabilities in self._p_z

## DIAYN._init_critic_update():
> Creates minimization operation for critic Q-function 
> I belive this is where the actual algorithm does it's work.
> * The method creates a tf.optimizer.minimize operation which update the Q-function with gradient descent and appends it to self._training_ops
> > "See Equation (10) in [1] for further information of the Q-function update rules"
![](eqn10.png)
> > Not sure where eqn 10 is, I do not see it in the paper...
 ```python
  self._qf_t = self._qf.get_output_for(self._obs_pl, self._action_pl, reuse=True) 
 ```
> p(z) gets updated on line **184**
> The log(p(z)) is referenced on **186**
> "reward_pl" (reward policy)? gets updated **188**, **189** 

## DIAYN._init_actor_update():
Creates minimization operations for policy and state value functions
The separate function for state value function approximation stailizess training. Equations (8, 13)


> 46         Args:
> 47             base_kwargs (dict): dictionary of base arguments that are directly
> 48                 passed to the base `RLAlgorithm` constructor.
> 49             env (`rllab.Env`): rllab environment object.
> 50             policy: (`rllab.NNPolicy`): A policy function approximator.
> 51             discriminator: (`rllab.NNPolicy`): A discriminator for z.
> 52             qf (`ValueFunction`): Q-function approximator.
> 53             vf (`ValueFunction`): Soft value function approximator.
> 54             pool (`PoolBase`): Replay buffer to add gathered samples to.
> 55             plotter (`QFPolicyPlotter`): Plotter instance to be used for
> 56                 visualizing Q-function during training.
> 57             lr (`float`): Learning rate used for the function approximators.
> 58             scale_entropy (`float`): Scaling factor for entropy.
> 59             discount (`float`): Discount factor for Q-function updates.
> 60             tau (`float`): Soft value function target update weight.
> 61             num_skills (`int`): Number of skills/options to learn.
> 62             save_full_state (`bool`): If True, save the full class in the
> 63                 snapshot. See `self.get_snapshot` for more information.
> 64             find_best_skill_interval (`int`): How often to recompute the best
> 65                 skill.
> 66             best_skill_n_rollouts (`int`): When finding the best skill, how
> 67                 many rollouts to do per skill.
> 68             include_actions (`bool`): Whether to pass actions to the
> 69                 discriminator.
> 70             add_p_z (`bool`): Whether th include log p(z) in the pseudo-reward.
> 71         """
>
1. get policy distribution
2. set log_pi_t to the log_p_t of the _policy_dist
3. Set the v_f
4.  



## _init_discriminator_update
