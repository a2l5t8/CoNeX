Documentation
#############

The CoNeX framework is built on top of `pymonntorch`, extending its biologically inspired modeling capabilities with a structured and reusable design.

It is organized into three core components:

1. **Behaviors**
2. **Structures / Containers**
3. **Helpers**

Each serves a distinct role in defining, organizing, and operating neural network simulations.

-----------------
1. Behaviors
-----------------

The ``Behaviors`` folder contains all the **modular behavior classes** that define the dynamic operations of:
- ``NeuronGroup`` objects (e.g., LIF dynamics, noise, dendritic computation)
- ``SynapseGroup`` objects (e.g., weight initialization, STDP)
- The ``Network`` object itself (e.g., time resolution, global recording)

These behavior modules are the **core building blocks** of CoNeX and `pymonntorch`. They can be flexibly composed using `prioritize_behaviors()` to define how a network object processes information at each time step.

Example behaviors include:
- ``LIF`` – Leaky Integrate-and-Fire model
- ``InherentNoise`` – Adds stochastic noise to neuron dynamics
- ``SynapseInit`` – Initializes synaptic topology

.. code-block:: python

    ng = NeuronGroup(net, size=100, behavior=prioritize_behaviors([
        LIF(), InherentNoise(), Fire()
    ]))

-------------------------------
2. Structures / Containers
-------------------------------

The ``Structures`` (or ``Containers``) module enables the creation of **hierarchical, reusable components** by grouping lower-level network objects and behaviors into composite structures.

These are ideal for:
- Saving and loading reproducible sub-networks
- Managing **modular components** (e.g., cortical columns, hippocampal circuits)
- Scaling up large networks by reusing smaller functional blocks

This allows you to **abstract complexity** and build neural systems from a library of tested substructures.

Example use cases:
- A visual processing module composed of several LIF layers
- A recurrent memory loop that can be inserted in multiple network contexts

------------------
3. Helpers
------------------

The ``Helpers`` module provides utility tools to streamline common operations, including:
- Data processing and formatting
- Input masking and encoding
- Visualization utilities
- Parameter sampling and initialization logic

These modules are **not part of the core simulation loop**, but they greatly simplify experimentation, analysis, and debugging.

Example helpers:
- ``normalize_tensor()``
- ``MaskBuilder``
- ``PoissonEncoder``

.. code-block:: python

    from conex.helpers import PoissonEncoder

    encoded_input = PoissonEncoder(rate=20).encode(tensor_input)

---------------------
Putting it Together
---------------------

The true power of CoNeX comes from combining all three components:
- Use **Behaviors** to define core neural and synaptic dynamics
- Use **Structures** to organize reusable circuit motifs
- Use **Helpers** to streamline your workflow

Subpackages
-----------

.. toctree::
   :maxdepth: 2

   conex.behaviors
   conex.helpers
   conex.nn

Module contents
---------------

.. automodule:: conex
   :members:
   :show-inheritance:
   :undoc-members:
