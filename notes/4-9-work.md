# Estimate of time to recreate in current Pytorch
After a brief look, there does not seem to be that much within the project which relies on a feature which would be
terribly different in Pytorch. This makes me think the refactoring would be relatively quick. The project is pretty
extensive though, so I think it would take a substantial amount of time for the simple act of rewriting the segments of
code which need to be rewritten. To be honest, I have never done something like this before, I have only ever built
things from scratch, or only worked on select parts of a project this size. This makes it hard for me to give a good
estimate on the amount of time it would take, but I would put it at a couple of weeks at my current availability, with
room for a significant increase depending on how complicated it got.



# What is the format of the skills?
A "skill" is a policy conditioned on a fixed Z. 
The policies are of the type NNPolicy from "nn_policy.py" it is a serializable sadnbox.rocky.tf.policies.base [Policy.](https://github.com/rll/rllab/blob/master/sandbox/rocky/tf/policies/base.py)

Each policy consists of:
> * self._obs_pl
> * self.action
> * self._scope_name

# Explanation of algorithm:

## Critic update:
1. Get the output of the discriminator

Then:
``` python
# Here we are setting the pseudo reward to softmax_cross_entropy...
reward_pl = -1 * tf.nn.softmax_cross_entropy_with_logits(labels=z_one_hot,logits=logits)
# Now we subtract the log of p_z, as in eqn. 3, to create the pseudo-reward
if self._add_p_z:
    reward_pl -= log_p_z
    reward_pl = tf.check_numerics(reward_pl, 'Check numerics: reward_pl')
self._reward_pl = reward_pl
# Here we set the next target using the soft value function approximator
with tf.variable_scope('target'):
    vf_next_target_t = self._vf.get_output_for(self._obs_next_pl)  # N
    self._vf_target_params = self._vf.get_params_internal()

# Below we set the y expectations, calculate loss, and optimize
ys = tf.stop_gradient(
    reward_pl + (1 - self._terminal_pl) * self._discount * vf_next_target_t
)  # N

self._td_loss_t = 0.5 * tf.reduce_mean((ys - self._qf_t)**2)

qf_train_op = tf.train.AdamOptimizer(self._qf_lr).minimize(
    loss=self._td_loss_t,
    var_list=self._qf.get_params_internal()
)

```

## Actor update:
```python
# Set the policy distribution
self._policy_dist = self._policy.get_distribution_for(
            self._obs_pl, reuse=True)
# Calculate log of pi_t, (eqn. 3)
log_pi_t = self._policy_dist.log_p_t  # N

# get parameters and output from soft value function
self._vf_t = self._vf.get_output_for(self._obs_pl, reuse=True)  # N
self._vf_params = self._vf.get_params_internal()

# set the log of target_t to the output of the Q-function when run on the _obs_pl (i think this is the observations)
log_target_t = self._qf.get_output_for(
    self._obs_pl, tf.tanh(self._policy_dist.x_t), reuse=True)  # N
# set the correction
corr = self._squash_correction(self._policy_dist.x_t)
corr = tf.check_numerics(corr, 'Check numerics: corr')

# set the scaled log_pi the entropy scaler value
scaled_log_pi = self._scale_entropy * (log_pi_t - corr)

# calculate the surrogate loss
self._kl_surrogate_loss_t = tf.reduce_mean(log_pi_t * tf.stop_gradient(
    scaled_log_pi - log_target_t + self._vf_t)
)

# calculate the loss
self._vf_loss_t = 0.5 * tf.reduce_mean(
    (self._vf_t - tf.stop_gradient(log_target_t - scaled_log_pi))**2
)

# Set the training operation for the policy
policy_train_op = tf.train.AdamOptimizer(self._policy_lr).minimize(
    loss=self._kl_surrogate_loss_t + self._policy_dist.reg_loss_t,
    var_list=self._policy.get_params_internal()
)

# Set the training operation for the value function
vf_train_op = tf.train.AdamOptimizer(self._vf_lr).minimize(
    loss=self._vf_loss_t,
    var_list=self._vf_params
)

# Append the training operations
self._training_ops.append(policy_train_op)
self._training_ops.append(vf_train_op)
```

## Discriminator Update:
```python
# split the observations into obs, and the one hot values of obs
(obs, z_one_hot) = self._split_obs()

# Depending on _include_actions, get output using the action policy, or using no action policy
if self._include_actions:
    logits = self._discriminator.get_output_for(obs, self._action_pl,
                                                reuse=True)
else:
    logits = self._discriminator.get_output_for(obs, reuse=True)

# Calculate the loss on the discriminator
self._discriminator_loss = tf.reduce_mean(
    tf.nn.softmax_cross_entropy_with_logits(labels=z_one_hot,
                                            logits=logits)
)
# Set the optimizer
optimizer = tf.train.AdamOptimizer(self._discriminator_lr)
# Set the training operation for the discriminator
discriminator_train_op = optimizer.minimize(
    loss=self._discriminator_loss,
    var_list=self._discriminator.get_params_internal()
)
# Append the operation for training the discriminator
self._training_ops.append(discriminator_train_op)
```
### Note to self:
These training operations are added to a list of training operations which will be run when the model is trained,
having to specifically do that logic at the time