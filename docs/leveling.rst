Leveling
========

This section will cover all the leveling system related functionality. In this case i will explain you how to create your own leveling profiles
and your own experience formulas if you have a formula based experience assignation to each of your levels. For example you can have one leveling
profile depending on your character type or the difficulty mode of your game. As all the leveling functionality is implemented in ``BPC_Leveling``,
you have a detailed explanation of its variables, functions and dispatchers at the end of this section.

.. _creating_leveling_profiles:

Creating new leveling profiles
------------------------------

This process is also datatable oriented, which makes the process very easy to manage:

1. Go to :ref:`dt_leveling` and create a new row name (lets call it *Test* for example).
2. Fill the leveling settings you want to have for that profile.
3. Go to your class that has the leveling component you want to load that data.
4. Click on the leveling component and in the *Details* panel, you will see the ``LevelingTableRow`` variable under the *Settings* category,
   just put the row name you created before (in this case it would be *Test*).

.. Note:: If you dont use a formula, make sure you fill the ``NeededExperiencePerLevel`` variable
   manually from ``StartingLevel`` to ``MaxLevel`` in the datatable.

.. _adding_exp_formulas:

Adding new experience formulas
------------------------------

This process has 2 steps: creating the formula itself and its calculus implementation.

1. Go to ``e_ExperienceFormulaType`` under the :ref:`enums` folder and add your
   new formula name.
2. In ``BPC_Leveling``, you will see there is a function called **ApplyExperienceFormula**, just open it
   and now you will have a new pin available in the *Switch* node. You just have to implement your calculations there
   as you see in the examples i have provided. The function will give you the level that the experience is calculated for.

.. Tip:: Unreal Engine has a dedicated node for math expressions as you have seen in the examples, if you want to get more
   information about it `here <https://docs.unrealengine.com/en-US/Engine/Blueprints/UserGuide/MathNode/index.html>`__ you
   have a link to the official documentation.

.. _bpc_leveling:

BPC_Leveling
------------

.. _leveling_variables:

Variables
^^^^^^^^^

* ``StartingLevel``: The actor starting level.
* ``MaxLevel``: Maximum level the actor can reach. When reached, it wont accept any experience points.
* ``CurrentLevel``: The current actor level.
* ``CurrentExperience``: The current actor experience.
* ``NeededExperience``: The experience that the actor needs to reach next level.
* ``TotalExperience``: The total experience that the actor has received (from **StartingLevel** to **CurrentLevel** + **CurrentExperience**)
* ``NeededExperiencePerLevel``: The levels that the actor can reach and the needed experience for each one. It will be constructed by formula or
  by datatable as said in the :ref:`dt_leveling` section.

.. _leveling_functions:

Functions
^^^^^^^^^

* ``ReceiveExperience``: Takes some experience and sums it to the **TotalExperience** and **CurrentExperience** variables.
  If this one is greater than the **NeededExperience** it will loop leveling up and updating them until **CurrentExperience** is less
  than the **NeededExperience**. This is because the actor can get a huge amount of experience which can cause leveling a few levels at once with just 1 call
  to the function.
* ``UpdateNeededExperience``: Just updates **NeededExperience** with the value in **NeededExperiencePerLevel** for the new reached level.
* ``LevelUp``: Increases **CurrentLevel** by 1, then it calls **AddStatsPerLevel** and **AddAttributesPerLevel** if any **BPC_Stats** or
  **BPC_Attributes** was found on initialization, then it gets and send the level reward to be handled by the owning actor through the
  **OnReceiveLevelUpReward** dispatcher.
* ``ApplyExperienceFormula``: Here you implement you own formulas calculations as explained in :ref:`adding_exp_formulas`.
* ``CreateNeededExperienceByFormula``: Called in the initialization when using a formula to set the experience per level. It will loop from **StartingLevel** to
  **MaxLevel** applying the formula for each level and setting the values in the **NeededExperiencePerLevel** variable.
* ``GetLevelingData``: Used to get the data in **LevelingTable** with the row you have set up in the variable **LevelingDataRow**.

.. _leveling_dispatchers:

Dispatchers
^^^^^^^^^^^

* ``OnExperienceReceived``
* ``OnLevelUp``
* ``OnReceiveLevelUpReward``
