.. highlight:: python
    
Tutorial
########


This tutorial will guide you through building and running a spiking neural network simulation using **CoNeX** and its integration with `pymonntorch`.

We’ll:
- Define a neuron group with multiple biological behaviors
- Set up a recurrent synapse group
- Record and visualize membrane potentials and spikes

Prerequisites
=============

Before starting, make sure you've installed the required packages:

.. code-block:: console

    $ pip install CoNeX, pymonntorch

If not, refer to the :doc:`Installation <installation>` page.

Step 1: Imports
==============

Start by importing the necessary modules:

.. code-block:: python

    from pymonntorch import *
    from conex import *
    import torch
    from matplotlib import pyplot as plt

`pymonntorch` is the simulation backend. `conex` provides biologically inspired behaviors. PyTorch is used for tensor computations, and `matplotlib` is used for visualization.

Step 2: Set Up the Network
==========================

We create a network and define the simulation time step.

.. code-block:: python

    net = Network(behavior = prioritize_behaviors([
        TimeResolution(dt = 1)
    ]))

- ``TimeResolution``: Sets the temporal granularity of the simulation to 1 ms per iteration.
- ``prioritize_behaviors``: Ensures behaviors are executed in a defined order.

Step 3: Define the Neuron Group
===============================

We define a group of 10 neurons with various biologically inspired behaviors:

.. code-block:: python

    ng = NeuronGroup(net = net, size = 10, behavior = prioritize_behaviors([
        SimpleDendriteStructure(),
        SimpleDendriteComputation(),
        LIF(
            init_v = -65,
            R = 2,
            tau = 5,
            v_rest = -65,
            v_reset = -70,
            threshold = -15,
        ),
        InherentNoise(scale = 18),
        Fire(),
        NeuronAxon(),
    ]) | {
        600 : Recorder(["v"]),
        601 : EventRecorder(["spikes"])
    }, tag = "exi")

Explanation of behaviors:

- ``SimpleDendriteStructure``: Defines a simplified dendritic tree architecture.

- ``SimpleDendriteComputation``: Handles computation of input integration through dendrites.

- ``LIF``: Implements a leaky integrate-and-fire neuron model with specific parameters.

- ``InherentNoise``: Adds intrinsic noise to the membrane potential (here, with `scale=18`).

- ``Fire``: Emits spikes once the threshold is reached.

- ``NeuronAxon``: Manages spike transmission through the neuron's axon.

- ``Recorder / EventRecorder``: Used for recording continuous variables (like voltage `v`) and events (like `spikes`), respectively.

The `| { 600 : ..., 601 : ... }` syntax adds recorders at specific execution priorities.

Step 4: Add Synaptic Connections
================================

We connect the neuron group to itself using a synapse group:

.. code-block:: python

    sg = SynapseGroup(net = net, src = ng, dst = ng, behavior = prioritize_behaviors([
        SynapseInit(),
        WeightInitializer(mode = "normal(3, 8)"),
        SimpleDendriticInput(),
    ]))

Explanation:
- ``SynapseInit``: Initializes synapse parameters and connections.

- ``WeightInitializer``: Initializes synaptic weights with a normal distribution (mean=3, std=8).

- ``SimpleDendriticInput``: Routes synaptic input to the dendritic compartments of the post-synaptic neuron.

Step 5: Run the Simulation
==========================

Now, initialize the network and simulate 100 iterations:

.. code-block:: python

    net.initialize()
    net.simulate_iterations(100)

Step 6: Visualize the Results
=============================

We plot the membrane potential (`v`) and the spike events:

.. code-block:: python

    plt.plot(net["v", 0])
    plt.show()

    plt.plot(net["spikes.t", 0], net["spikes.i", 0], 'o')
    plt.show()

The first plot shows how the voltage of neuron 0 changes over time.  
The second plot is a **spike raster** showing spike events over time across neurons.

Next Steps
==========

You’ve just:
- Built a biologically inspired spiking network
- Configured neuron and synapse behaviors
- Recorded and visualized neural activity

Continue exploring CoNeX by checking out:
- :doc:`Plasticity <plasticity>`
- :doc:`Visualization <visualization>`
- :doc:`Model Configuration Files <configuration>`

For a full list of behaviors and tools, visit the :doc:`API Reference <api>`.
