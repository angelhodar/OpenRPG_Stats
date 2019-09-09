Migration
==========

In this section im going to show you how extremely easy it is to migrate the system into your own project.
Typically, the migration process is very easy, but the problem is that usually, due to assets that references
other ones, you get some assets that you didnt want to have in your project, and you need to remove them manually.
While developing this system, i tried my best to isolate funtionality, making the systems as much modular as possible, which
reduces the dependencies between them, so when migrating you will only get the most important assets.

As you have seen, the project has two folders: ``Demo`` and ``OpenRPG_Stats``. You have 2 options when migrating:

Migrating system only
---------------------

If you want to migrate the system to implement it by your own with no demo content, just follow these steps:

1. Right click the ``OpenRPG_Stats`` folder and in the context menu click on the **Migrate** option. The engine will show you
   a list with all the files that are going to be migrated.
2. Click **Ok** and you will now have to look for the *Content* folder of your project
3. Select it and confirm the migration. 

When done, the system will be completely migrated and you can start implementing it into your project!

.. Note:: If you noticed, in the migration files list, the *Icons* folder of the ``Demo`` folder will be migrated as well, because the data tables
   *DT_Buffs* and *DT_Effects* are using those icons. If you dont want to migrate them because they are horrible, then
   before starting the migration you just need to clear the icons for those data tables and save everything.

Migrating system and demo content
---------------------------------

If you also want the demo implementation, the process is almost the same as before.
The only difference is that you need to right click the ``Demo`` folder, not the ``OpenRPG_Stats``.

If you have any problem just take a look at the `migration documentation <https://docs.unrealengine.com/en-US/Engine/Content/Browser/UserGuide/Migrate/index.html>`__
from the Unreal Engine official docs. Once you have migrated what you want, you are ready to go to the :doc:`./integration` section.
