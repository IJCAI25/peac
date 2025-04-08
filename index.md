---
layout: default
---

# Perception Emulated Actor Critic in Crowd Scenario Navigation

Our code is available at [here](https://github.com/IJCAI25/peac_code)

# Training Details

# LLM Prompt

Here is an example of the describing prompt:

```
You are a certified professional navigation instructor, and you are currently demonstrating a navigation to students in a residential area in [our country].
In [our country], according to traffic rules, ..., but if you are in a hurry, you can choose to walk along the nearest route.
While walking on the road, you will observe multiple objects ahead, such as pedestrians.
For each object, I am aware of their distance and angle from us. We adopt the safest possible way of walking.
```

An example of the output is as follows:

```html
<b>Semantic Encoder(Observation)</b>:

"<b>State</b>: 

A pedestrian; Angle: -0.65; Distance: 2.23m; 

A pedestrian; Angle: 1.06; Distance: 2.58m; 

... 

A pedestrian; Angle: -31.50; Distance: 10.47m; 

My current speed is 0.21 m/s.

<b>Actions</b>: 

Velocity: [$v_x$: 0.11, $v_y$: 0.18, $v_z$:0.00] 

Angular Velocity: [$w_x$: -1.02, $w_y$: 0.00, $w_z$:0.00] 

Rotation: [$\theta_x$: -0.28, $\theta_y$: -0.56, $\theta_z$:0.78]

<b>Reason</b>:

[Question:What should you do? Answer: I should slow down.]

[Question:Why do you take this action? Answer: Because there are 6 pedestrians in front of me.]

...

[Question: What is your future plan? Answer: I should move as quickly as possible.]"
```

# Transformer Network

<div style="text-align: center;">
  <img src="./figure/sophistry.png" width="80%">
</div>
Our Transformer network, built on components akin to Transformers, maps inputs into a latent space for processing by using the attention mechanism in a non-uniform manner, and then decodes them back to the output space. This approach circumvents the quadratic dependency on the size of the inputs or outputs, enabling more efficient scaling to larger inputs and outputs.

# Background

## Interpretable Navigation

The emergence of LLMs offers a potential direction for interpretability. Some studies started focusing on the interpretability of decision-making in navigation.

**This is mainly for AI safety considerations. For example, in autonomous driving, the model would output both the decision "stop" and the reasoning explanation "I see a red light, so I choose to stop".**

## Sophistry Phenomenon

<div style="text-align: center;">
  <img src="./figure/sophistry.png" width="80%">
</div>

The use of LLMs in navigation can be categorized into two approaches. The first approach treats the LLM as a decision-maker. However, in the field of robotic navigation, agents need to perform real-time motion planning, but the response time of LLMs is insufficient for planning tasks. 

Therefore, the second approach involves using the LLM as an interpreter, providing explanations for the agent's actions. 
However, in this approach, the motion planning unit and the LLM are serial, meaning that regardless of the agent's decision, the LLM must find a justification for it. 

**We refer to a behavior, where the LLM provides explanations for unreasonable decisions, as Sophistry. This phenomenon is concerning because even if the agent encounters a safety incident, the LLM will still describe its actions as rational.**

# PEAC: Perception Emulated Actor Critic

* We propose PEAC: Perception Emulating Actor-Critic, a method that emulates human behavioral perception and effectively addresses the the sophistry regarding unreasonable decisions in crowd scenarios.
* We introduce a three-stage training framework that enables real-time interaction with dynamic environments, achieving superior decision-making performance and facilitating Sim2Real transfer.
* In terms of ensuring AI safety through the interpretation of behaviors, we provide a novel dataset for robot navigation tasks and an embedded LLM network paradigm that accepts vector and prompt features as inputs.

## World Model

<div style="display: flex; justify-content: space-between;">
  <div style="width: 48%; text-align: center;">
    <img src="./figure/world-2.png" width="100%">
    <p>Traditional Actor-Critic Method</p>
  </div>
  <div style="width: 48%; text-align: center;">
    <img src="./figure/world-1.png" width="100%">
    <p>Our PEAC: Perception-Emulating Actor-Critic Method</p>
  </div>
</div>

Recent research on spatial intelligence has proposed embodied agent architectures based on large models, where the ``brain" of the agent is a paradigm containing components such as Actor, Critic, Perception, Memory, and World Model.

## Framework

![Simulation Results](./figure/framework.png)

# Experiments

## Simulation

### Quantitative Results

<table>
  <tr>
    <th rowspan="2">Methods</th>
    <th rowspan="2">Algorithm</th>
    <th rowspan="2">Research Work</th>
    <th colspan="3">Validation Seen</th>
    <th colspan="3">Validation Unseen</th>
    <th colspan="3">Test Unseen</th>
  </tr>
  <tr>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
  </tr>
  <tr>
    <td rowspan="4"><b>IL-Based</b></td>
    <td>IRL</td>
    <td>Pfeiffer et al.</td>
    <td>0.56</td>
    <td>72.76</td>
    <td>70.72</td>
    <td>0.25</td>
    <td>75.14</td>
    <td>107.58</td>
    <td>0.11</td>
    <td>76.85</td>
    <td>174.66</td>
  </tr>
  <tr>
    <td>AIRL</td>
    <td>Okal et al.</td>
    <td>0.43</td>
    <td><b>69.29</b></td>
    <td>121.99</td>
    <td>0.35</td>
    <td>89.14</td>
    <td>101.34</td>
    <td>0.12</td>
    <td>83.78</td>
    <td>192.94</td>
  </tr>
  <tr>
    <td>GAIL</td>
    <td>Tai et al.</td>
    <td>0.56</td>
    <td>74.22</td>
    <td>53.53</td>
    <td>0.52</td>
    <td>83.13</td>
    <td>63.10</td>
    <td>0.30</td>
    <td>90.68</td>
    <td>89.98</td>
  </tr>
  <tr>
    <td>ILPP</td>
    <td>Qin et al.</td>
    <td>0.67</td>
    <td>77.44</td>
    <td>33.32</td>
    <td>0.49</td>
    <td>82.52</td>
    <td>54.57</td>
    <td>0.24</td>
    <td>90.47</td>
    <td>73.14</td>
  </tr>
  <tr>
    <td rowspan="11"><b>RL-Based</b></td>
    <td>MAXQ</td>
    <td>Cai et al.</td>
    <td>0.63</td>
    <td>80.56</td>
    <td>30.60</td>
    <td>0.34</td>
    <td>92.45</td>
    <td>41.96</td>
    <td>0.07</td>
    <td>59.65</td>
    <td>148.67</td>
  </tr>
  <tr>
    <td>MGSMC</td>
    <td>Zhou et al.</td>
    <td>0.49</td>
    <td>81.20</td>
    <td>30.28</td>
    <td>0.37</td>
    <td>90.30</td>
    <td>44.43</td>
    <td>0.08</td>
    <td>60.59</td>
    <td>164.72</td>
  </tr>
  <tr>
    <td>A3C</td>
    <td>Shi et al.</td>
    <td>0.64</td>
    <td>76.98</td>
    <td>28.30</td>
    <td>0.30</td>
    <td>109.81</td>
    <td>71.42</td>
    <td>0.20</td>
    <td>74.28</td>
    <td>69.49</td>
  </tr>
  <tr>
    <td>PG</td>
    <td>Geng et al.</td>
    <td>0.52</td>
    <td>82.73</td>
    <td>31.47</td>
    <td>0.36</td>
    <td>81.03</td>
    <td>68.55</td>
    <td>0.30</td>
    <td>82.20</td>
    <td>53.95</td>
  </tr>
  <tr>
    <td>TRPO</td>
    <td>Walker et al.</td>
    <td>0.60</td>
    <td>79.37</td>
    <td>31.38</td>
    <td>0.42</td>
    <td>89.50</td>
    <td>42.29</td>
    <td>0.08</td>
    <td>60.60</td>
    <td>158.40</td>
  </tr>
  <tr>
    <td>DDPG</td>
    <td>Chai et al.</td>
    <td>0.42</td>
    <td>87.48</td>
    <td>38.19</td>
    <td>0.22</td>
    <td>73.65</td>
    <td>37.04</td>
    <td>0.16</td>
    <td>64.40</td>
    <td>91.35</td>
  </tr>
  <tr>
    <td>DQN</td>
    <td>Escobar-Naranjo et al.</td>
    <td>0.65</td>
    <td>87.40</td>
    <td>36.25</td>
    <td>0.51</td>
    <td>79.70</td>
    <td><b>31.09</b></td>
    <td>0.43</td>
    <td>82.29</td>
    <td>58.96</td>
  </tr>
  <tr>
    <td>D3QN</td>
    <td>Li et al.</td>
    <td>0.45</td>
    <td>83.31</td>
    <td>37.52</td>
    <td>0.40</td>
    <td>88.92</td>
    <td>42.71</td>
    <td>0.25</td>
    <td>74.73</td>
    <td>64.01</td>
  </tr>
  <tr>
    <td>TD3</td>
    <td>Neamah et al.</td>
    <td>0.57</td>
    <td>80.73</td>
    <td>32.26</td>
    <td>0.24</td>
    <td>86.25</td>
    <td>52.64</td>
    <td>0.16</td>
    <td>67.26</td>
    <td>100.75</td>
  </tr>
  <tr>
    <td>PPO</td>
    <td>Taheri et al.</td>
    <td>0.61</td>
    <td>81.29</td>
    <td>30.28</td>
    <td>0.40</td>
    <td>86.23</td>
    <td>39.77</td>
    <td>0.06</td>
    <td>66.15</td>
    <td>159.80</td>
  </tr>
  <tr>
    <td>SAC</td>
    <td>Wen et al.</td>
    <td>0.67</td>
    <td>81.79</td>
    <td>30.81</td>
    <td>0.62</td>
    <td>86.84</td>
    <td>37.22</td>
    <td>0.45</td>
    <td>75.65</td>
    <td>64.59</td>
  </tr>
  <tr>
    <td colspan="2"><b>PEAC</b></td>
    <td>Ours</td>
    <td><b>0.70</b></td>
    <td>72.04</td>
    <td><b>26.17</b></td>
    <td><b>0.66</b></td>
    <td><b>70.35</b></td>
    <td>36.22</td>
    <td><b>0.46</b></td>
    <td><b>53.60</b></td>
    <td><b>43.95</b></td>
  </tr>
</table>

*Table: Experimental results of different decision makers, with an average ρ = 2.*

<table>
  <tr>
    <th rowspan="2">Methods</th>
    <th colspan="3">ρ = 0.5</th>
    <th colspan="3">ρ = 1.0</th>
    <th colspan="3">ρ = 1.5</th>
  </tr>
  <tr>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
  </tr>
  <tr>
    <td>MAXQ</td>
    <td>0.85</td>
    <td>77.21</td>
    <td>35.06</td>
    <td>0.61</td>
    <td>77.85</td>
    <td>47.59</td>
    <td>0.36</td>
    <td>68.51</td>
    <td>75.46</td>
  </tr>
  <tr>
    <td>MGSMC</td>
    <td>0.82</td>
    <td>77.13</td>
    <td>35.16</td>
    <td>0.55</td>
    <td>79.60</td>
    <td>48.65</td>
    <td>0.34</td>
    <td>67.86</td>
    <td>87.00</td>
  </tr>
  <tr>
    <td>A3C</td>
    <td>0.80</td>
    <td>79.57</td>
    <td>36.94</td>
    <td>0.50</td>
    <td>72.056</td>
    <td>70.83</td>
    <td>0.29</td>
    <td>72.04</td>
    <td>69.15</td>
  </tr>
  <tr>
    <td>PG</td>
    <td>0.83</td>
    <td>77.29</td>
    <td>33.53</td>
    <td>0.59</td>
    <td>79.92</td>
    <td>46.44</td>
    <td>0.34</td>
    <td>79.77</td>
    <td>61.07</td>
  </tr>
  <tr>
    <td>TRPO</td>
    <td>0.81</td>
    <td>76.35</td>
    <td>34.43</td>
    <td>0.62</td>
    <td>85.05</td>
    <td>43.11</td>
    <td>0.38</td>
    <td>61.65</td>
    <td>106.26</td>
  </tr>
  <tr>
    <td>DDPG</td>
    <td>0.79</td>
    <td>77.22</td>
    <td>32.36</td>
    <td>0.53</td>
    <td>79.20</td>
    <td>57.42</td>
    <td>0.39</td>
    <td>70.26</td>
    <td>66.34</td>
  </tr>
  <tr>
    <td>DQN</td>
    <td>0.88</td>
    <td>76.07</td>
    <td>38.70</td>
    <td>0.71</td>
    <td>77.62</td>
    <td>44.08</td>
    <td>0.46</td>
    <td>86.06</td>
    <td>48.13</td>
  </tr>
  <tr>
    <td>D3QN</td>
    <td>0.84</td>
    <td>74.62</td>
    <td>32.55</td>
    <td>0.51</td>
    <td>72.06</td>
    <td>66.19</td>
    <td>0.30</td>
    <td>75.47</td>
    <td>73.10</td>
  </tr>
  <tr>
    <td>TD3</td>
    <td>0.75</td>
    <td>76.89</td>
    <td>33.44</td>
    <td>0.52</td>
    <td>77.97</td>
    <td>49.07</td>
    <td>0.31</td>
    <td>79.27</td>
    <td>58.63</td>
  </tr>
  <tr>
    <td>PPO</td>
    <td>0.78</td>
    <td>79.08</td>
    <td>40.35</td>
    <td>0.52</td>
    <td>80.22</td>
    <td>48.34</td>
    <td>0.35</td>
    <td>56.22</td>
    <td>117.46</td>
  </tr>
  <tr>
    <td>SAC</td>
    <td><b>0.90</b></td>
    <td>72.69</td>
    <td>35.89</td>
    <td>0.70</td>
    <td>81.55</td>
    <td>42.10</td>
    <td>0.55</td>
    <td>82.28</td>
    <td>64.53</td>
  </tr>
  <tr>
    <td>PEAC</td>
    <td>0.87</td>
    <td><b>72.01</b></td>
    <td><b>32.48</b></td>
    <td><b>0.78</b></td>
    <td><b>73.49</b></td>
    <td><b>39.35</b></td>
    <td><b>0.56</b></td>
    <td><b>70.96</b></td>
    <td><b>45.39</b></td>
  </tr>
</table>

*Table: Comparison of different RL methods under different ρ.*

<table>
  <tr>
    <th colspan="3">Training Stage</th>
    <th colspan="3">Decision-making</th>
    <th colspan="2">State Observation</th>
    <th colspan="2">Action Output</th>
    <th>Language Ability</th>
  </tr>
  <tr>
    <th>1</th>
    <th>2</th>
    <th>3</th>
    <th>SR↑</th>
    <th>SD↓</th>
    <th>AT↓</th>
    <th>E<sub>ped</sub>↓</th>
    <th>D<sub>obs</sub>↓</th>
    <th>E<sub>x</sub><sup>a</sup>↓</th>
    <th>E<sub>y</sub><sup>a</sup>↓</th>
    <th>L<sub>Token</sub>↓</th>
  </tr>
  <tr>
    <td>✓</td>
    <td>✓</td>
    <td>✗</td>
    <td>0.41</td>
    <td>84.47</td>
    <td>59.77</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
  </tr>
  <tr>
    <td>✓</td>
    <td>✗</td>
    <td>✗</td>
    <td>0.03</td>
    <td>109.54</td>
    <td>68.55</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
  </tr>
  <tr>
    <td>✗</td>
    <td>✓</td>
    <td>✗</td>
    <td>0.43</td>
    <td>82.29</td>
    <td>58.96</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
    <td>/</td>
  </tr>
  <tr>
    <td>✓</td>
    <td>✗</td>
    <td>✓</td>
    <td>0.04</td>
    <td>100.32</td>
    <td>76.95</td>
    <td>0.4612</td>
    <td>0.003</td>
    <td>0.1776</td>
    <td>0.4239</td>
    <td>0.117</td>
  </tr>
  <tr>
    <td>✗</td>
    <td>✓</td>
    <td>✓</td>
    <td>0.44</td>
    <td>79.31</td>
    <td>56.65</td>
    <td>0.5437</td>
    <td>0.095</td>
    <td>0.1072</td>
    <td>0.1811</td>
    <td>0.106</td>
  </tr>
  <tr>
    <td>✓</td>
    <td>✓</td>
    <td>✓</td>
    <td><b>0.46</b></td>
    <td><b>53.60</b></td>
    <td><b>43.95</b></td>
    <td><b>0.46115</b></td>
    <td><b>0.094</b></td>
    <td><b>0.1066</b></td>
    <td><b>0.1799</b></td>
    <td><b>0.099</b></td>
  </tr>
</table>

*Table: Ablation Study of the Three-Stage Training in PEAC.*

| Interpreter | LLM Score (GPT-4) | LLM Score (LLaVA) | LLM Score (Gemini) | Human Score |
|:------------|:------------------|:------------------|:-------------------|:------------|
| ADAPT | 3.95 | 3.5 | 4 | 3.375 |
| PEAC | **4.65** | **4.25** | **4.375** | **4.05** |

*Table: Experimental Results of the Interpreter's Performance.*

### Qualitative Results

![Simulation Results](./figure/simulation.png)

<div style="display: flex; justify-content: space-between;">
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/ADAPT-1.mp4" type="video/mp4">
    </video>
    <p>Scene 1 - ADAPT</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEAC-1.mp4" type="video/mp4">
    </video>
    <p>Scene 1 - PEAC</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/ADAPT-2.mp4" type="video/mp4">
    </video>
    <p>Scene 2 - ADAPT</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEAC-2.mp4" type="video/mp4">
    </video>
    <p>Scene 2 - PEAC</p>
  </div>
</div>

<div style="display: flex; justify-content: space-between; margin-top: 20px;">
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/ADAPT-3.mp4" type="video/mp4">
    </video>
    <p>Scene 3 - ADAPT</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEAC-3.mp4" type="video/mp4">
    </video>
    <p>Scene 3 - PEAC</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/ADAPT-4.mp4" type="video/mp4">
    </video>
    <p>Scene 4 - ADAPT</p>
  </div>
  <div style="width: 24%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEAC-4.mp4" type="video/mp4">
    </video>
    <p>Scene 4 - PEAC</p>
  </div>
</div>

## Real-world

### Quantitative Results

| Methods | SR↑ | NE↓ | SPL↑ | nDTW↑ | CLS↑ |
|:--------|:----|:----|:-----|:------|:------|
| SAC | 0.20 | 2.80 | 45.0 | 55.0 | 50.0 |
| PEAC | **0.40** | **1.50** | **65.0** | **75.0** | **70.0** |

*Table: Quantitative Results of Real-World Experiments.*

### Qualitative Results

<div style="display: flex; justify-content: space-between;">
  <div style="width: 48%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/SAC-1.mp4" type="video/mp4">
    </video>
    <p>SAC</p>
  </div>
  <div style="width: 48%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/SAC-2.mp4" type="video/mp4">
    </video>
    <p>SAC</p>
  </div>
</div>

<div style="display: flex; justify-content: space-between; margin-top: 20px;">
  <div style="width: 48%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEACr-1.mp4" type="video/mp4">
    </video>
    <p>PEAC</p>
  </div>
  <div style="width: 48%; text-align: center;">
    <video width="100%" controls>
      <source src="./video/PEACr-2.mp4" type="video/mp4">
    </video>
    <p>PEAC</p>
  </div>
</div>
