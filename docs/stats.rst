Stats
=====

In this section firstly im going to show you how easy is to add new stats and sets of stats to your actors like enemies or characters,
as well as how to add new buffs and effects to your game. After that, as all functionality related to stats is implemented in ``BPC_Stats``,
i will explain its variables, functions and dispatchers so you can understand how it works and what can you do with it.

.. _adding_new_stats:

Adding new stats
----------------

Adding new stats is a very straightforward process:

1. Go to ``e_Stat`` under the :ref:`enums` folder and add your new stat.
2. Then in your widget that displays all the stats, add a new ``WB_Stat``, select it and you will see in the *Details*
   panel that it has some exposed variables. Fill the enum variable with your new stat and configure the rest of variables as you want.
3. Make sure you add that new widget to the widgets that the component has to refresh with the **AddCustomWidgets** function.

.. Tip:: To remove a stat the process is exactly the inverse, just remove the widget and the entry in the enum.

.. _creating_stats_sets:

Creating sets of stats
----------------------

Once you have created your desired stats, then im sure you want to create a set of stats to, for example, assign a different one
per character race in your game. This is also extremely easy:

1. Go to :ref:`dt_stats` and create a new row, you can call it *Ninja* for example.
2. Then just fill the stats you want to have for that particular set.
3. Go to your class that has the stats component you want to load that data (lets suppose you have a *BP_Ninja* class).
4. Click on the stats component and in the *Details* panel you will see the ``StatsTableRow`` variable under the *Settings* category,
   just put the row name you created before (in this case it would be *Ninja*).

.. Note:: I have used ``DT_Stats`` as the datatable for the example, if you have created your own one then its perfect, just make sure you put that
   table in the ``StatsTable`` of the component.

**Done!** Now when your *BP_Ninja* class is initialized, it will load the set of stats you have configured in the datatable (remember
it will only happen if there is no previous saved data).

.. _adding_new_buffs:

Adding new buffs
----------------

Buffs have two parts of data. One of them is the static data, which means it will never change (like the icon, the description, etc). The other one
is the dynamic data, which can be the duration, the bonuses to stats and attributes, if it is going to be permanent, etc. Lets create the static
data first:

1. Go to :ref:`dt_buffs` and create a new row (lets call it *FireBall* for example).
2. Fill the required data for that particular buff.

With that now you are able to apply a buff. When you apply a buff using the *ApplyBuff* function in ``BPC_Stats``, it will ask you for the row name
you have created (the static data, *FireBall* in the example) and the dynamic data of the buff in that moment. This last one is completely up to you
as it will may change depending  on the situation and your own game requirements.

.. _adding_new_effects:

Adding new effects
------------------

The process is almost the same as buffs. The difference is that the implementation of each effect is completely up to you.

1. Go to :ref:`dt_effects` and create a new row (lets call it *Poison* for example).
2. Fill the required data for that particular effect.

Now, as mentioned, the way each effect is implemented is completely up to you using the dispatcher **OnEffectTick** in the actors that implement the stats component.
For example the *Poison* effect of the example can remove 3% of the actor's *Health* each time it ticks. You can see the ``BP_DemoCharacter`` to
check some examples.

.. Note:: Buffs and effects have the same static data, but they have been kept separated to make it more flexible, so if you expand
   the buffs static data, it doesnt interfer with the effects data.

.. _bpc_stats:

BPC_Stats
---------

.. _stats_variables:

Variables
^^^^^^^^^

* ``UseAttributeBonusForPercentageCalculations?``: If true, the system will treat the stat base value **and** the bonuses
  from attributes for the percentage bonus calculations. If false, it will only consider the stat base value.

* ``Stats``: The updated values of the stats set. It also keeps the bonuses.

* ``CurrentStats``: The value of the stats that have current value, for example health, mana, hunger, etc.

* ``Buffs``: The currently applied buffs and its dynamic data.

* ``BuffsTimer``: This timer will keep track of the buffs remaining time and will fire an event to remove
  the one whose duration has expired.
* ``NextShortestBuff``: The buff with the shortest remaining time, it will be
  the next one to be removed when **BuffsTimer** expires.
* ``Effects``: The currently applied effects and its dynamic data.
* ``EffectsTimer``: This timer will keep track of the effects remaining tick time and will fire an event to remove
  and tick them when needed.
* ``NextEffectToTick``: The effect with the shortest remaining tick time, which will
  get its ticks count increased by 1 when **EffectsTimer** expires.

.. _stats_functions:

Functions
^^^^^^^^^

Stats
"""""

* ``GetStatValue``: Returns the final value of the stat passed as input, with all the bonuses applied and calculated.
  If that stat is also a current stat, you will get the proper value in the *Current Stat* output pin.
* ``AddStatBonuses``: It receives flat and percentage bonuses and sums them to the **Stats** component variable, then it calls
  **CalculateStatsFinalValues**.
* ``RemoveStatBonuses``: Same as the previous one, but it will substract the bonuses instead of summing them.
* ``CalculateStatsFinalValues``: Recalculates the values of the stats it receives as input. It will also take care of the
  current stat values to clamp them properly.
* ``ModifyCurrentStatValue``: Modifies the stat passed as input with a value. It will make sure to clamp it between 0 and the final
  value that the stat has at the moment (it would be a problem if you can have more health than the maximum health).
* ``AddStatsPerLevel``: It wil be called by **BPC_Leveling** if the owning actor implements leveling system and it levels up, adding
  the values you have set up in the **StatsPerLevel** on **StatsTable**.
* ``IsCurrentStat``: It will return true if the stat passed as input is a current stat.
* ``GetStatsData``: Used to get the data in **StatsTable** with the row you have set up in the variable **TableDataRow**.

Buffs
"""""

* ``ApplyBuff``: Applies the buff passed as input. If the buff is already applied, it will just remove the current one to apply the new one.
* ``RemoveBuff``: Removes the buff passed as input if it is already applied.
* ``IsBuffActive?``: Returns true whether a buff is applied.
* ``UpdateBuffsRemainingTime``: It will update the remaining time of each applied buff.
* ``GetShortestBuffTime``: When the buffs remaining time is updated, this function will return the buff with the shortest remaining time, that is,
  the next buff to be removed when the **BuffsTimer** expires.
* ``IsBuffPermanent?``: Returns true if buff is applied and permanent.

Effects
"""""""

* ``ApplyEffect``: Applies the buff passed as input. If the effect is already applied, it will just remove the current one to apply the new one.
* ``RemoveEffect``: Removes the effect passed as input.
* ``IsEffectActive?``: Returns true whether an effect is applied.
* ``UpdateEffectRemainingIntervalTime``: It will update the remaining tick time of each applied effect.
* ``IncrementEffectTicks``: When an effect tick is executed, this function will increment its ticks count and if that updated count is equal
  to the ticks it was going to do, it will call **RemoveEffect** (except when its permanent).
* ``GetShortestEffectTime``: When the effects remaining tick time is updated, this function will return the effect with the shortest remaining tick time, that is,
  the next effect to tick when the **EffectsTimer** expires.
* ``IsEffectPermanent?``: Returns true if effect is applied and permanent.

.. _stats_dispatchers:

Dispatchers
^^^^^^^^^^^

The component has a set of dispatchers that will be called when certain events happen, so you can override them in the actor that owns the component.
The names are very descriptive so im just going to mention them:

* ``OnBuffApplied``
* ``OnBuffRemoved``
* ``OnEffectApplied``
* ``OnEffectRemoved``
* ``OnEffectTick``
* ``OnStatFinalValueChanged``
* ``OnStatCurrentValueChanged``
