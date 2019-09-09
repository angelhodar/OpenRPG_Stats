System Core: Components
================================
When we refer to the system functionality, the majority is implemented in ``BPC_Stats``, ``BPC_Attributes``
and ``BPC_Leveling``. However, when we refer to the system core, that is the ``BPC_Stats`` component,
because ``BPC_Attributes`` and ``BPC_Leveling`` can be considered as extensions to the main purpose of
the whole system, which is giving stats functionality to an actor.

.. Note:: Each component work as isolated as they can, they just communicate the minimum to have as less
   dependencies between them as possible.

Lets say you want to use ``BPC_Attributes`` and ``BPC_Stats`` in an actor. When ``BPC_Attributes``
gets one attribute modified, it will search if the **owning actor** has a ``BPC_Stats`` component. If not,
it wont do anything related to the stats, but if it is found, it will tell the ``BPC_Stats`` component
about that attribute modification (attributes can affect stats). For example if you get +1 point of
*Strenght*, you can set it to increase *Attack Value* by +15.

General Behaviour
-----------------

In this section we will see that even though the components have different functionality, there are a few aspects that all of them share.

.. _settings_variables:

Settings Variables
^^^^^^^^^^^^^^^^^^

Every component has a few variables to configure it. The names of the variables will vary a bit depending
on the component, but the functionality is the same for all of them:

* ``DataTable``: The datatable from which to extract the data.
* ``TableDataRow``: The name of the row data in ``DataTable``
* ``DebugEnabled?``: If true, the component will print messages on the screen
  showing possible errors and warnings when its initialized.

These settings are enough to setup the component, because all the data is in the assigned datatable,
so you only need to write the name of the row in ``TableDataRow`` and the component will load the data
from there.

.. _widgets_refreshing:

Widgets Refreshing
^^^^^^^^^^^^^^^^^^

When communicating with widgets to send them data, all the components uses some of the interfaces showed in the ``Blueprints`` section,
depending on the data it is going to send. Also, all components have a ``Widgets`` variable, which contains the widgets that the component
is going to send data when some events happen.. There are also 2 common functions:

* ``AddCustomWidgets``: Before any component is initialized, we need to pass it the widgets it has to refresh as said before, so this function
  just adds widgets to the ``Widgets`` variable mentioned before. For example, the ``BPC_Stats`` component in ``BP_DemoCharacter`` will refresh
  the ``WB_DemoDebugStats`` and its stats subwidgets, but it doesnt refresh the attributes or the current experience widgets. Its a simple way
  to link a component with the widgets you want.

* ``RefreshWidgets``: Its called when the component has loaded the neccessary data, either from saved before or by datatable, to refresh its widgets with
  all the data it has. For example, the ``BPC_Attributes`` will refresh every attribute and the available attribute points.

.. _saving:

Save / Load
^^^^^^^^^^^

All the components have built-in save / load functionality. They can save and load data either by ``GameInstance`` or using a ``SaveGame`` class.

.. Note:: If you dont know about ``GameInstance`` and ``SaveGame`` classes, `here <https://www.youtube.com/watch?v=5w594D3qtLs>`__ you have a nice YouTube
   video from Mathew Wadstein explaining the ``GameInstance`` class and `this <https://docs.unrealengine.com/en-US/Gameplay/SaveGame/index.html>`__ other
   link explains perfectly how the ``SaveGame`` class work.

There are 4 functions used for this purpose:

* ``CreateSaveData``: It gets the current component data and creates the proper struct (you can see it as a packet) with all the data,
  depending on the component: ``s_AttributesSaveData`` , ``s_StatsSaveData``  or ``s_LevelingSaveData``.

* ``SaveData``: It will send the data created by ``CreateSaveData`` to the corresponding container that will store the data with an "identifier" of that data,
  given by the ``SaveName`` input. The container will save the data with the value from the ``SaveName`` variable, and it will be different
  depending on the ``ToGameInstance`` boolean input. If it is true, it will get the game's ``GameInstance`` object to send the data there.
  Remember that this class is persistent across levels, so if we go from one level to another, we can send the data to that object and retrieve it in
  the new level. If the boolean is false, then it will get the ``.sav`` file on disk  (if it doesnt exists it will create it)
  to save the data there.

* ``LoadData``: Exactly the inverse of the ``SaveData``, it will get the data that has the label given by the input ``SaveName`` assigned to it,
  also depending on the container it will ask for that data to ``GameInstance`` or ``.sav`` file. When it gets the data it will update the
  component variables with it, and it will also set the boolean ``HasLoadBeenSuccessful?`` to true (false if no saved data was found). And if no data was found,
  what do we do? Here the ``LoadTableData`` function comes to the rescue!

* ``LoadTableData``: It will be called in both ``BPC_Stats`` and ``BPC_Attributes`` **only** when the component hasnt received saved data before. If so,
  the function will take the value of ``TableDataRow`` mentioned before to extract the data from the assigned datatable, just like default data. In ``BPC_Leveling``
  it will be called always **but** the value of ``HasBeenLoadSuccessful?`` will branch inside the function. Thats because we need to construct the needed
  experience for each level, because that data is not saved by the ``SaveData`` function.

.. Note:: Typically you would only save data to the .sav file disk when the user clicks on some menu button to save the current game state or if you have
   a menu button that closes the game, you can save there to the disk before closing the game. Anyway, it depends on how have you configured the saving
   and loading options in your game.

.. _consistency:

Consistency
^^^^^^^^^^^

Every component has a function called ``CheckConsistency``. This function will tell you if there is any unconsistency in the component settings.
For example if the variable ``DataTable`` doenst have a valid value, or if the row assigned in ``TableDataRow`` doesnt exist in the datatable if you
have assigned a valid value to it. It will check more consistency errors depending on the component, because each of them has its own consistency settings.
This function is also callable from the editor, that means that you can call it without running the game. To try it just go to the ``BP_DemoCharacter``
blueprint, select one of the system components in the left side and you will see at the component details that you have a button with the same name as
the function, just click it.

.. _initialization:

Initialization Pipeline
^^^^^^^^^^^^^^^^^^^^^^^
At this point, you have seen a lot of functions and what they do, so the last thing to clarify is when some of them are called and in what order. Typically
the initialization process is done in the event BeginPlay of your character or player controller. In the demo content, it has been implemented in the character.
The steps would be the following ones:

1. Get the component and call the function AddCustomWidgets and pass it an array of the widgets it has to refresh.
2. Call the functin LoadData and give it a SaveName value, for example if you have a main menu and the user can give a name to the character, that would be
   a perfect value to put here.
3. Call the Initialize event of the component.

Every component has an event called ``Initialize``. The pipeline of this event is the following one:

1. Searches for other system components in the owning actor. For example ``BPC_Leveling`` will search for ``BPC_Attributes`` and ``BPC_Stats`` to perform
   some actions when the owning actor levels up. If the component hasnt found other components, nothing happens.
2. Calls the function ``CheckConsistency`` if the variable ``DebugEnabled?`` is true, which will print some strings in the screen if there is any
   configuration error.
3. Calls ``LoadTableData`` if ``HasLoadBeenSuccessful?`` is false (with the special case of the ``BPC_Leveling`` component mentioned before) to load
   the default data from the proper datatable.
4. Refresh all the widgets with the loaded data.

.. Tip:: The previous process is the same for all the components.

Now that you have understood the main idea about how the components work, lets see the details of each component in the next sections.
