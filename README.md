Traffic Signal and Route Optimization Using Quantum Computing
==============
This repository contains an implementation of Traffic Signal and Route Optimization using **quantum computing** principles. The goal is to minimize congestion and optimize traffic light signals at intersections using a QUBO (Quadratic Unconstrained Binary Optimization) approach, solved with D-Wave's quantum annealer.

Table of Contents
=================


* [Overview](#Overview)
* [Assumptions](#Assumptionst)
* [Quantum Computing Approach](#Quantum-Computing-Approach)
* [Pipeline](#Pipeline)
* [Implementation](#Implementation)

## Overview

Traffic congestion in urban areas is a persistent issue. Traffic lights at intersections and inefficient routing of vehicles contribute to delays and increased travel times. The goal of this project is to optimize the signal timings of traffic lights at intersections and assign efficient routes for vehicles using quantum computing techniques. This optimization is achieved by formulating the problem as a QUBO and solving it with the D-Wave quantum annealer.

## Assumptions

- The Number of vehicles affected by each signal mode is known.
- Real-time vehicle positions and map data (road length, speed limit, average speed) are available.
- Only six signal modes are considered at each intersection.
- Pedestrian crossings are not considered.

# Quantum Computing Approach

**D-Wave** uses quantum annealing to solve optimization problems by exploring many possible solutions in parallel with qubits, which can represent both 0 and 1 simultaneously. **QUBO (Quadratic Unconstrained Binary Optimization)** is the mathematical framework used to minimize a **quadratic objective function** with **binary variables**, making it ideal for traffic optimization.

# Pipeline

The Traffic Optimization process involves the following steps: 

***Data Collection :-***
Gather real-time data on vehicle positions, map information (road lengths, speed limits), and the number of vehicles at each intersection.

***QUBO Formulation :-***
Formulate the traffic signal optimization and route selection as a QUBO problem, incorporating vehicle data, congestion levels, and signal timing.

***D-Wave Quantum Solver :-***
Solve the QUBO problem using the D-Wave quantum annealer to propose optimized signal timings and vehicle routes.

***Route Optimization :-***
Assign efficient routes to vehicles based on road conditions and minimize the overlap in congested road segments, dynamically adjusting based on real-time data.

### Below steps are not Implemented in the above code 

***Signal Application :-***
Implement the traffic signal changes at intersections based on the QUBO output for 20 seconds.

***Recalculation :-***
After 20 seconds, recalculate the QUBO, and if the signal remains unchanged, extend the green light. Continue this process for multiple iterations.

*The entire process runs in cycles, allowing for continuous optimization of traffic flows and vehicle routing.*

# Implementation
## *Traffic Signal Optimization*

- ***Objective:-*** Maximize the number of vehicles passing through each intersection while ensuring only one signal mode is active at any given time.
- ***Approach:-***
        
        1. Define the cost function based on the number of vehicles affected by each signal mode.
        2. Use adjacent intersections to adjust the signal timing dynamically.
        3. The optimization is recalculated every 10 seconds.
- ***Explaination:-***

    Binary Variable  **x_i_j** represents **ith intersection** in **mode j**

    <img width="760" alt="image1" src="https://github.com/naitik-2006/traffic_optimization/blob/main/img.png">

    C_i_j represent the number of vehicles that will exit the intersection if jth mode is activated at intesection i.

    $$
    \text{Obj} = -\sum_{i=1}^{n} \sum_{j=1}^{6} C_{ij} x_{ij}^2
    $$

    o establish a relationship between neighboring traffic signals, we consider two intersections as being related if they support each other and the time elapsed since the last activation of the 𝑗-th mode at one intersection is greater than or approximately equal to the time required to reach the neighboring intersection.

    This ensures that the signals can effectively coordinate their timings based on the following criteria:

    Supportive Relationship: The two intersections must influence each other's traffic flow positively.
    Timing Condition: The elapsed time since the last activation of the 𝑗-th mode must be sufficiently long, ensuring that vehicles have enough time to reach the neighboring intersection without immediate signal changes.

## *Route Optimization*
- ***Objective:-*** Minimize road congestion while allowing vehicles to take overlapping routes if the road segment can handle additional load.
- ***Approach:-***
        
        1. For each car, map the source and destination to their nearest nodes in the road graph.
        2. Assign three different routes per vehicle based on weight given to road segments.
        3. Define the QUBO with binary variable q_i_j
        4. Solve the QUBO problem
- ***Explaination***

    For selecting three different routes for a vehicle, we apply **Dijkstra's Algorithm** iteratively. In each iteration, we increase the weight of the road segments that were previously selected to encourage the algorithm to find alternative paths.

    Binary variable **q_i_j** represents **car i** taking **route j**. 
    
    Since each vehicle can only take one route at a time, exactly one route variable per vehicle must be selected in the minimum of the QUBO solution.

    Thus, we introduce the following **constraint term** to enforce this condition:

    $$ 
    0 = \left( \sum_{j \in \{1, 2, 3\}} q_{ij} - 1 \right)^2
    $$

    This ensures that for each vehicle 𝑖, only one route 𝑗 is selected among the three possible routes.

    The cost associated with each road segment can be calculated based on the number of vehicles using that segment. For a road segment 𝑠, the cost is given by:

    $$
    \text{cost}(s) = \left( \sum_{q_{ij} \in B_s} q_{ij} \right)^2.
    $$

    Here, 𝐵_𝑠 represents the set of all vehicle-route pairs 𝑞_𝑖_𝑗 that include road segment 𝑠.
