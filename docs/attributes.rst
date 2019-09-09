Attributes
==========

In this section i will follow the same structure as in the :doc:`stats` section, as the process is very similar. First you will learn how to create
your own attributes and also your own sets of them. Then i will explain the ``BPC_Attributes`` to make sure you understand its functionality.

.. _adding_attributes:

Adding new attributes
---------------------

Adding new attributes, as with stats, is very easy:

1. Go to ``e_Attribute`` under the :ref:`enums` folder and add your new attribute.
2. Then in your widget that displays all the attributes, add a new ``WB_Attribute``, select it and you will see in the *Details*
   panel that it has some exposed variables. Fill the enum variable with your new attribute and configure the rest of variables as you want.
3. Make sure you add that new widget to the widgets that the component has to refresh with the **AddCustomWidgets** function.

.. Tip:: To remove an attribute the process is exactly the inverse, just remove the widget and the entry in the enum.

.. _creating_attributes_sets:

Creating sets of attributes
---------------------------

Once you have created your desired attributes, the process is the same as with stats:

1. Go to :ref:`dt_attributes` and create a new row, you can call it *Warrior* for example.
2. Then just fill the attributes you want to have for that particular set.
3. Go to your class that has the attributes component you want to load that data (lets suppose you have a *BP_Warrior* class).
4. Click on the attributes component and in the *Details* panel you will see the ``AttributesTableRow`` variable under the *Settings* category,
   just put the row name you created before (in this case it would be *Warrior*).

.. Note:: I have used ``DT_Attributes`` as the datatable for the example, if you have created your own one then its perfect, just make sure you put that
   table in the ``AttributesTable`` of the component.

**Done!** Now when your *BP_Warrior* class is initialized, it will load the set of attributes you have configured in the datatable (remember
it will only happen if there is no previous saved data).

.. _bpc_attributes:

BPC_Attributes
--------------

.. _attributes_variables:

Variables
^^^^^^^^^

* ``Attributes``: The updated values of the attributes set. It also keeps the bonuses.
* ``AvailableAttributePoints``: The attribute points that the owning actor can spend on improving attributes.
* ``SpentAttributePoints``: The attribute points that the owning actor has spent already.

.. _attributes_functions:

Functions
^^^^^^^^^

* ``GetAttributeValue``: Returns the final value of the attribute passed as input, with all the bonuses applied and calculated.
* ``AddAttributeBonuses``: It receives flat and percentage bonuses and sums them to the **Attributes** component variable, then it calls
  **CalculateAttributesFinalValues** to recalculate the final attributes values.
* ``RemoveAttributeBonuses``: Same as the previous one, but it will substract the bonuses instead of summing them.
* ``SpendAttributePoints``: It will substract the points passed as input to the **AvailableAttributePoints** variable, then it will call
  **AddPointsToAttribute**.
* ``AddPointsToAttribute``: Adds the points passed as input to the attribute base value, then it recalculates the final value and also
  recalculates the stats bonuses this attribute can have.
* ``ReceiveAttributePoints``: Just sum the points passed as input to the **AvailableAttributePoints** variable.
* ``CalculateAttributesFinalValues``: Recalculates the final values of the attributes it receives as input.
* ``AddAttributesPerLevel``: It will be called by **BPC_Leveling** if the owning actor implements leveling system and it levels up, adding
  the values you have set up in the **AttributesPerLevel** on **AttributesTable**.
* ``ResetAttributes``: It resets the current attributes to the default attributes in the datatable row assigned. It will just reset and recalculate
  the base values. Bonuses will be still applied until they are removed by other functions.
* ``AddAttributeBonusesToStats``: When an attribute is modified, this function will recalculate the stats bonuses if the attribute has any bonuses on stats.
* ``GetAttributesData``: Used to get the data in **AttributesTable** with the row you have set up in the variable **AttributesDataRow**.

.. _attributes_dispatchers:

Dispatchers
^^^^^^^^^^^

* ``OnAttributePointsReceived``
* ``OnAttributesResetted``
* ``OnAttributeFinalValueChanged``
* ``OnAttributePointsSpent``
