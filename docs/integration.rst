Integration
===========

In the previous section you learned how to migrate the system into your own project. Now im going to
show you the necessary steps to get it integrated into your project.

If you migrated the system with the demo content, the only thing you need to do is telling your project
to use the demo classes already built. You can achieve it by setting the ``BP_DemoGI`` as your game instance in your project
settings (just search game instance and select it) and in your **GameMode** set the ``BP_DemoCharacter`` as
the **Pawn** class. With that, you have everything ready to go!

On the other hand, if you have migrated the system with no demo content, there are more things to do, but i promise
they are easy and fast (just as done in the demo content). Lets go step by step.

Widgets setup
-------------

First you will need a widget that contains all of the other widgets you have created to show your stats, attributes and/or
leveling data. In your widgets just make sure you implement the proper :ref:`interfaces` that provides the data you want to display on them.
If you dont have one main widget then just simply create a new widget blueprint (lets call it ``WB_MainWidget``) and add the widgets into its canvas panel.
Adjust them to fit your needs and the widgets setup will be completed!

Character setup
---------------

In your character blueprint add the components you want using the green button *Add Component* at the top left side. When done, click on each component
and configure the variables under the *Settings* category in the details panel (check the explanation of each variable in the :ref:`settings_variables`).
Now go to the **BeginPlay** event (or whatever event you have that is called when game initializes), then you need to create a widget and that will be
the ``WB_MainWidget`` we created before, then save it in a variable. Now for each component you have to set them up like explained in the :ref:`initialization` section.
Remember that now you have access to all the widgets because we saved the main one in a variable. Then if you want to put some keyboard controls thats completely
up to you, for example you can set the C key to save all the data.

.. Note:: Its not 100% necessary to have a ``WB_MainWidget`` that has all of your other widgets. If you have added some widgets directly to the viewport in your project,
   its ok as long as you can give the proper component a reference to the widget you want, using the **AddCustomWidgets** function.

With that done, **everything is finished and you have completely integrated the system into your project!** Easy, right? Now its your turn to create your own stats, attributes,
leveling profiles, buffs, effects and everything you need to suit your game requirements ;)
