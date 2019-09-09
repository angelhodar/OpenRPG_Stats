================
Folder Structure
================

This section will cover the project folder structure and a brief description of the main files on it.
When you download the project from `GitHub <https://github.com/angelhodar/OpenRPG_Stats>`__ and you open it with Unreal Engine,
the first thing you will see at the root of the project folder are the ``Demo`` and ``OpenRPG_Stats`` folders. Lets quickly see
what each one contains:

Demo
----
This folder contains blueprints, icons, widgets and stuff from the *Third Person Template* and other ones created just
to test and explore the system. Lets focus on the blueprints and widgets folders:

Blueprints
^^^^^^^^^^
  * ``BP_DemoCharacter``: Used to test the system when you run the project. It has all the system related functionality in
    a separated ``StatsSystem`` graph, which contains components initialization, keyboard controls, enemy selection
    with the mouse and interaction with the saving / loading functionality.
  * ``BP_DemoEnemy``: Just to see what the system is capable of, i have included an enemy that can be selected with the mouse and
    implementats stats functionality, as i think its something very common in RPG games and helps you see the flexibility of the
    system components, which arent limited to be implemented only in the character for example.
  * ``BP_DemoEnemyStats``: The implementation of the stats functionality for the enemies. The only thing you need to know by now is that
    it has all the system stats functionality but with a very little modification to handle the widget that displays the health of the selected enemies.
  * ``GI_DemoStats``: This is a ``GameInstance`` object. As you will see later, the system can save and load the data using a file in the hard drive with
    the ``.sav`` extension, or by using the ``GameInstance``, which is commonly used to keep data between game levels without needing to use the hard disk
    ``.sav`` file, so its much faster and easy to manage. Its functionality is very simple and you can get it implemented in your own game instance in just
    a few minutes. When the game starts, this class will dump all the saved data in the file disk into itself.

Widgets
^^^^^^^^^^
  * ``WB_DemoStatsWindow``: Simple widget to watch the system values updated when you use keyboard controls, you can also check
    my weak artist concepts :(
  * ``WB_DemoMain``: Its the widget added to the viewport and displayed in the game, which contains all the other widgets.
  * ``WB_DemoEnemy``: The widget that displays the health of the selected enemy.

.. Note:: Its a good practice to create a main HUD widget as the WB_DemoMain, that contains all your widgets, and then add it to the viewport
   instead of adding all of them individually.

OpenRPG_Stats
-------------
It contains all the system functionality. Lets see exactly what each folder contains:

Blueprints
^^^^^^^^^^
This is by far **the most important folder, because it contains the system core functionality**. This folder has the blueprint
function library ``BPL_Stats``, which includes utility functions that we can call anywhere, and the ``BP_StatsSaveObject``, which
is the container for the saved data in your hard disk, so it will keep the saved data even if the player leaves the game. This folder also
contains another 2 subfolders:

.. _components:

Components
**********

These components will provide any actor they are added to with stats functionality with buffs and effects
(``BPC_Stats``), attributes (``BPC_Attributes``) and customizable leveling system (``BPC_Leveling``). These components are
independent from each other, so you can just implement the ones you want on your actors, but they can work together perfectly!
Each one will have its own section in the documentation as they implement most of the overall system functionality.

.. _interfaces:

Interfaces
**********

They are used mainly to implement the interaction between the components and the widgets. They allow the components
to be independent of the widgets, so you can use your own widgets and just implement the interface on them to be able
to receive components data! There are 5 interfaces with this purpose:

* ``BPI_Stats``: Used by ``BPC_Stats`` to notify the widgets when a stat value is modified.
* ``BPI_Attributes``: Used by ``BPC_Attributes`` to notify the widgets when an attribute value or the attribute points are modified.
* ``BPI_Leveling``: Used by ``BPC_Leveling`` to notify the widgets when some experience is received or the actor levels up.
* ``BPI_Buffs``: Used by ``BPC_Stats`` to notify the widgets when a buff is applied or removed.
* ``BPI_Effects``: Used by ``BPC_Stats`` to notify the widgets when an effect is applied, removed or ticks.

There is also an extra interface used by the built-in save / load system:

* ``BPI_SaveLoad``: It is responsible of the communication between the components and the saving containers
  (``BP_StatsSaveObject`` and ``GameInstance``). Its useful to make the components independent from the saving containers,
  they will just send and receive data using the interface, but they dont care about the type of container the data is going or
  coming from.

.. Note:: If you dont know how interfaces work `here <https://docs.unrealengine.com/en-US/Engine/Blueprints/UserGuide/Types/Interface/UsingInterfaces/index.html>`__
   you have the link of the official documentacion.

.. _data tables:

DataTables
^^^^^^^^^^

This folder contains all the tables where you configure your own default system data. When you create a new row and fill its data,
you can just use that row name putting it in the corresponding component, so it will load the data you filled in that row, which allows you to create
your own sets of attributes and stats.

.. Note:: This tables will be accesed by their respective components **only** when there is no saved data to load, so
   they take the tables data as **default**.

.. _dt_stats:

DT_Stats
************

* ``Stats``: Here you configure stats and the default values they are going to have when ``BPC_Stats`` loads them.
* ``CurrentStats``: The default values of the stats that have current value. For example health, mana, hunger,
  stamina and so on are stats that have a value between 0 and the maximum value they can get, that value is called current stat.
  So all the stats you add in this variable will have a current stat value.
* ``StatsPerLevel``: If an actor implementes leveling system and stats system, when it levels up, the component
  ``BPC_Leveling`` will tell ``BPC_Stats`` to add the stats you configure here.

.. _dt_attributes:

DT_Attributes
*****************

* ``Attributes``: Here you configure attributes and the default values they are going to have when ``BPC_Attributes`` loads them.
* ``AttributesBonusesToStats``: When an attribute is modified, here you can configure what bonuses are going to be applied to a set
  of stats. The best way to see this is with an example. Lets say you add the *Strenght* attribute, and then you say that when it is
  increased, the stat *AttackValue* is going to be increased by 1%. You can do it with as many stats as you want, either
  flat or percentage bonuses as seen in the example.
* ``AttributesPerLevel``: If an actor implements leveling system and attributes system, when it levels up, the component
  ``BPC_Leveling`` will tell ``BPC_Attributes`` to add the attribute points you configure here.

.. _dt_leveling:

DT_Leveling
***********

* ``StartingLevel``: The starting level the actor is going to have.
* ``MaxLevel``: The maximum level the actor can reach. When this level is reached and actor receives more experience points,
  they will be ignored.
* ``UseCustomExperienceFormula?``: There are some RPG games where the experience per level is defined by a formula. So if you
  set this to true, system will use the formula specified by ``ExperienceFormulaType``. If you leave it to false, you need to set the
  ``NeededExperiencePerLevel`` map variable manually.
* ``ExperienceFormulaType``: The formula you want to apply. I have included 2 examples, which are implemented in ``BPC_Leveling``.
  If you add new formulas make sure you implement them there!
* ``NeededExperiencePerLevel``: As said before, if you dont use a custom formula, you need to manually set it up. The key is a
  level, and the value is the needed experience to reach next level. If custom formula is used, it will be constructed from ``StartingLevel``
  to ``MaxLevel`` using the formula. Even if you use a formula, you can put default values that can be useful for some types of formulas. For
  example, in the *Type2* formula that i have implemented, the needed experience for each level is the needed experience in the previous level
  + 10% of that experience, but what do we do for level 1? I have simply added a level 0 entry, so when the data is constructed, for level 1 it will
  see that entry :)
* ``LevelUpRewards``: The reward given to the player when it levels up, you can customize it as you want. Each key represents the reached level.

.. _dt_buffs:

DT_Buffs
********

* ``DisplayedName``: The name you want to assing to that buff when displayed (for example if you have a tooltip or something like that).
* ``Description``: The buff description (can be used in tooltips as mentioned before).
* ``Icon``: The icon to display when buff is applied.
* ``Displayable``: Controls if the buff should be displayed or not when applied.

.. _dt_effects:

DT_Effects
**********

* ``DisplayedName``: The name you want to assing to that effect when displayed (for example if you have a tooltip or something like that).
* ``Description``: The effect description (can be used in tooltips as mentioned before). In this case, if you write *{value}* in it,
  it will be parsed with the value the effect has.
* ``Icon``: The icon to display when effect is applied.
* ``Displayable``: Controls if the effect should be displayed or not when applied.

As you can see ``DT_Buffs`` and ``DT_Effects`` are almost identical, but i prefered to have them in separate data tables so if you want to
add new data for buffs or i decide to include new features on them, it can be done without interfere in the effects and viceversa. Moreover,
these tables are not used by components as ``DT_Stats``, ``DT_Attributes`` or ``DT_Leveling``, in this case we use them to refer to a buff
or effect, very useful to retrieve neccessary data on demand (in widgets for example).

.. Tip:: You can create your own data tables if you want to organize them differently. For example, you can have one datatable for the sets of stats
   of your enemies, and other one for your each of your character types of your game. `Here <https://www.youtube.com/watch?v=nt1hlJO-DPo>`__ you have
   a video explaining data tables.

.. _enums:

Enums
^^^^^
All the enums used in the system:

  * ``e_Stat``: Contains all the stats that you have in your game.
  * ``e_Attribute`` : Contains all the attributes that you have in your game.
  * ``e_ExperienceFormulaType``: The leveling experience formulas that you use in your game.

.. _structs:

Structs
^^^^^^^
All the structs used in the system by the components, data tables, save / load system, etc.

  * ``s_Attribute``: It is used by ``BPC_Attributes`` to keep your attributes bonuses and final values correctly calculated.
  * ``s_AttributeBonus``: Used to store the flat and percentage bonuses that can be applied to attributes.
  * ``s_AttributesData``: Specifies the data each row has on :ref:`dt_attributes`.
  * ``s_AttributesSaveData``: You can see it as a data packet that contains all the attributes saved data for a particular actor.
  * ``s_Buff``: Contains the stats and attributes bonuses, duration and so on that you configure when applying a buff.
  * ``s_BuffStatic``: Buff data which is always the same for that buff (used in :ref:`dt_buffs`).
  * ``s_DynamicBuffData``: Used internally by ``BPC_Stats`` to keep track of the buffs timing.
  * ``s_Effect``: Contains the effect value, duration and so on that you configure when applying a effect.
  * ``s_EffectStatic``: Effect data which is always the same for that effect (used in :ref:`dt_effects`).
  * ``s_DynamicEffectData``: Used internally by ``BPC_Stats`` to keep track of the effects ticks and timing.
  * ``s_LevelingData``: Specifies the data each row has on :ref:`dt_leveling`.
  * ``s_LevelUpReward``: Here you can configure the reward you want to give to an actor when it levels up (applied also by yourself).
  * ``s_LevelingSaveData``: Contains all the leveling data to be saved, like level, experience, etc.
  * ``s_Stat``: It is used by ``BPC_Stats`` to keep your stats bonues and final values correctly calculated.
  * ``s_StatBonus``: Used to store the flat and percentage bonuses that can be applied to stats.
  * ``s_StatsData``: Specifies the data each row has on :ref:`dt_stats`.
  * ``s_StatsSaveData``: All the stats data used to save and load from it.

.. _ui:

UI
^^
This folder contain a set of widgets to show how your own widget could be implemented, if you dont have you own widgets design yet, you can use this ones!
The only thing you need to do for your custom widgets is adding the :ref:`interfaces` you want depending on what data is going to be displayed in that widget.
For example you can see that the ``WB_Stat`` implements the ``BPI_Stats`` interface, and the ``WB_Bar`` implements both ``BPI_Buffs`` and
``BPI_Effects`` interfaces to display buffs and effects on it.

As they are only 4 widgets with very little code, i suggest you to give a look at them and the variables they have. It wont take you much time and it will help you
later when adding new stats and attributes ;)
