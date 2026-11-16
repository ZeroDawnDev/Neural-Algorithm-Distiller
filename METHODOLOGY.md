
# Methodology

## 1. Research Overview

Neural Algorithm Distiller (NAD) investigates an adaptive approach to deep neural network compression by combining reinforcement learning, structured channel pruning, and knowledge distillation.

The methodology follows a teacher–student framework in which a trained teacher network guides the development of a smaller student network. A Deep Deterministic Policy Gradient (DDPG) controller interacts with a compression environment to determine channel-pruning actions.

The objective is to reduce model complexity while retaining as much predictive performance as possible.

## 2. Overall Methodology

The proposed workflow consists of the following stages:

1. Train or load a teacher network.
2. Initialize a student network.
3. Observe the student network's compression state.
4. Use a DDPG controller to select compression actions.
5. Apply structured channel pruning.
6. Train the student using knowledge distillation.
7. Evaluate accuracy and computational complexity.
8. Repeat the compression process according to the experimental configuration.

## 3. Teacher Network

The teacher network acts as the reference model throughout the compression process.

A ResNet-based architecture is used as the teacher model in the experimental setup.

Its responsibilities include:

- Learning the original classification task.
- Producing reference predictions.
- Providing knowledge to the student network.
- Serving as a baseline for evaluating compression.

The teacher network is treated as the reference model, rather than the network being compressed during student optimization.

## 4. Student Network

The student network is the target of the compression process.

It begins with a network architecture that can be progressively reduced through structured channel pruning.

The student is optimized to achieve a balance between:

- Predictive accuracy.
- Parameter count.
- Computational cost.
- Model compactness.

After pruning, the student is trained to recover performance through knowledge distillation.

## 5. Reinforcement Learning Controller

NAD uses Deep Deterministic Policy Gradient (DDPG) as its reinforcement-learning controller.

DDPG is an actor–critic algorithm designed for continuous action spaces.

### 5.1 State Representation

The compression environment provides a state representation to the controller.

The state describes information about the current compression process. Depending on the implementation, this can include:

- Current layer information.
- Channel or pruning information.
- Model complexity.
- Compression progress.
- Performance-related measurements.

The state representation is used by the controller to make compression decisions.

### 5.2 Actor Network

The actor network maps the observed state to a continuous action.

The action represents a proposed compression decision, such as a channel-pruning ratio.

### 5.3 Critic Network

The critic network evaluates the quality of the actor's proposed action by estimating its expected return.

### 5.4 Experience Replay

Transitions collected from the compression environment are stored in a replay buffer.

The controller samples previous experiences to update its actor and critic networks.

### 5.5 Exploration

Exploration allows the controller to investigate different compression actions instead of repeatedly choosing the same decisions.

The exploration strategy is defined by the implementation and experimental configuration.

## 6. Compression Environment

The compression environment connects the reinforcement-learning controller to the model-compression process.

It performs the following operations:

1. Receives the current compression state.
2. Passes the state to the DDPG controller.
3. Receives the selected action.
4. Applies the corresponding compression operation.
5. Evaluates the resulting model state.
6. Returns the next state and reward.

The environment provides the feedback required for reinforcement learning.

## 7. Structured Channel Pruning

NAD uses structured channel pruning to reduce the size and computational requirements of the student network.

Instead of removing individual weights, structured pruning removes complete channels or feature-map dimensions.

This approach aims to produce smaller network structures that can benefit from conventional deep-learning hardware and inference frameworks.

The pruning process involves:

1. Identifying the target layer.
2. Determining the channel-pruning action.
3. Applying the pruning operation.
4. Maintaining compatible network dimensions.
5. Measuring the resulting model complexity.

The resulting student network is evaluated after compression.

## 8. Knowledge Distillation

Knowledge distillation transfers information from the teacher network to the student network.

The teacher generates reference predictions, while the student learns to approximate the teacher's output distribution alongside the original task objective.

A typical distillation objective combines hard-label classification loss and soft-target distillation loss.

The formulation is:

\[
\mathcal{L}_{KD}
=
\alpha \mathcal{L}_{CE}
+
(1-\alpha)T^2\mathcal{L}_{KL}
\]

Where:

- \(\mathcal{L}_{CE}\) is the cross-entropy loss.
- \(\mathcal{L}_{KL}\) is the Kullback–Leibler divergence between teacher and student predictions.
- \(T\) is the distillation temperature.
- \(\alpha\) controls the contribution of the classification objective.

The temperature softens the output probability distributions, allowing the student to learn information beyond the teacher's highest-probability class.

## 9. Reward Design

The reinforcement-learning controller requires a reward signal to evaluate compression decisions.

The reward is produced by the compression environment and should reflect the trade-off between model efficiency and predictive performance.

Relevant evaluation signals include:

- Student accuracy.
- Parameter reduction.
- FLOPs or MACs reduction.
- Compression constraints.
- Accuracy degradation.

The exact reward equation and coefficients must match the implementation used in the experiments.

No specific reward weights are assumed in this documentation.

## 10. Experimental Evaluation

The compressed student network should be evaluated against the original teacher network and an appropriate uncompressed baseline.

The evaluation considers:

| Metric | Purpose |
|---|---|
| Classification accuracy | Measures predictive performance |
| Parameter count | Measures model size |
| FLOPs / MACs | Estimates computational complexity |
| Compression ratio | Measures reduction in model size |
| Inference latency | Measures practical execution speed |
| Accuracy change | Measures performance degradation |

### 10.1 Compression Ratio

A parameter-based compression ratio can be calculated as:

\[
CR =
\frac{P_{\text{original}}}
{P_{\text{compressed}}}
\]

Where:

- \(P_{\text{original}}\) is the original parameter count.
- \(P_{\text{compressed}}\) is the compressed parameter count.

### 10.2 Parameter Reduction

\[
R_P =
\left(
1 -
\frac{P_{\text{compressed}}}
{P_{\text{original}}}
\right)
\times 100
\]

### 10.3 Accuracy Change

\[
\Delta A =
A_{\text{compressed}}
-
A_{\text{baseline}}
\]

These metrics help determine whether the compression process produces a useful reduction in model complexity without unacceptable accuracy loss.

## 11. Experimental Reproducibility

For meaningful comparisons, experiments should document:

- Dataset and preprocessing configuration.
- Teacher and student architectures.
- Training and validation settings.
- DDPG hyperparameters.
- Exploration strategy.
- Compression actions.
- Knowledge-distillation parameters.
- Random seeds.
- Hardware and software environment.

All reported results should be obtained from recorded experiments.

## 12. Limitations

The methodology has several practical considerations:

- Aggressive pruning can reduce predictive accuracy.
- Reinforcement-learning training can require substantial computation.
- Compression decisions may depend on the network architecture.
- Structured pruning requires compatible dimensions across connected layers.
- Knowledge distillation does not guarantee complete recovery of lost accuracy.
- Inference improvements depend on the hardware and execution framework.

## 13. Research Objective

The central research objective of NAD is to investigate whether reinforcement-learning-guided structured pruning, combined with knowledge distillation, can produce compact neural networks while maintaining useful predictive performance.

The project focuses on adaptive compression decisions rather than relying exclusively on fixed pruning schedules.
