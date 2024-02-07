Written by Lars Thießen.

Errata, change requests etc. please via:

Mastodon: @LarsThiessen@mastodon.gamedev.place

Discord: lars.thiessen

## Table of contents

[**Table of contents**](#_1fob9te)

[**Basic Perforce processes**](#_3znysh7)

[Login in to the Perforce client](#_2et92p0)

[Create a workspace](#_tyjcwt)

[Synchronize depot and workspace](#_1t3h5sf)

[Connecting Unreal and Perforce](#_4d34og8)

[Submit a changelist](#_2s8eyo1)

[Discard local changes](#_3rdcrjn)

[Submitting a new project for the first time](#_26in1rg)

[Deleting local files](#_lnxbz9)

[**Best Practices**](#_35nkun2)

[Submit frequently](#_1ksv4uv)

[Sync frequently](#_44sinio)

[Meaningful submit descriptions](#_2jxsxqh)

[Go through the files to be submitted](#_z337ya)

[Do not submit large amounts of data that "might be used"](#_3j2qqm3)

[A change per changelist](#_1y810tw)

[Sync before submitting](#_4i7ojhp)

[English asset and folder names ONLY](#_2xcytpi)

[**Perforce features for advanced users**](#_1ci93xb)

[Use "One File Per Actor" for large levels](#_3whwml4)

[Virtual Assets](#_2bn6wsx)

[Resolve version conflicts](#_qsh70q)

[Invisible conflicts](#_3as4poj)

[Data Assets](#_1pxezwc)

[**Troubleshooting**](#_49x2ik5)

["Select Unreal Engine Version"](#_2p2csry)

["Cannot submit from non-stream client"](#_147n2zr)

["Cannot create a file when that file already exists"](#_23ckvvd)

["The system cannot find the file specified"](#_ihv636)

[After each sync, I am logged into Unreal as Perforce user XY](#_32hioqz)

[The file I deleted is still there](#_1hmsyys)

[Syncing takes forever](#_41mghml)

["The system cannot find the path specified"](#_2grqrue)

[Forgotten password](#_vx1227)

[All my files are read-only](#_3fwokq0)

[Changes were not recognized](#_1v1yuxt)

[Unable to connect to the server](#_4f1mdlm)

[I downloaded the project, but it is empty when I open it](#_2u6wntf)

[My username is wrong/outdated](#_19c6y18)

["Error saving [asset name]."](#_3tbugp1)

["Must resolve before submitting"](#_28h4qwu)

## Basic Perforce processes

### Login in to the Perforce client

When the client (P4V) is started for the first time, the "Open Connection" dialog is displayed:
![P4V Open Connection Dialog](/larst-of-us/assets/images/PerforceSurvivalKit(15).png)

**Server** : The IP address of the server must be entered here. It should end with ":1666", the network port Perforce uses. You've probably received this IP address from your friendly Perforce admin. If you are the admin, you better look up your server's IP now :)

**User** : The user name can be entered manually but should always be selected via the browse button to avoid typing errors.

**Workspace** : At the first start, no workspace (a local copy of the Perforce depot) will exist. A new one can be created via "New...".

### Create a workspace

A new workspace can be created in the dialog that now appears. The first required entry is the workspace name:

![P4V New Workspace Dialog](/larst-of-us/assets/images/PerforceSurvivalKit(14).png)

Workspaces are linked to the computer on which they are created. Therefore, the name of the workspace should, if possible, also describe the computer on which it was created, e.g. "MyNameWorkspaceLaptop". If you switch between different computers, you will also need several workspaces (but only one user account).

The workspace root is therefore the local path in which the workspace is created. This path should be as short as possible, as all project paths are appended to it and it is therefore easy for a file name to exceed Unreal's 260-character limit.

The last required specification is that of the stream. In simple terms, streams are parallel versions of the same depot; in other version control systems they are called "branches".

In smaller projects, only one stream will exist, especially if the project is mainly based on blueprints instead of C++ code. Nevertheless, a stream must be selected in order to enable work within the depot later on.

IMPORTANT: If a stream is not selected, Perforce will not complain but will create a workspace without complaint. Only when submitting files will you notice that something is wrong, as Perforce does not know where to submit the files without a stream.

### Synchronize depot and workspace

After connecting to the Perforce server, we have the option of accessing the depot contents (red); to edit them, we have to copy them to our local workspace (green).

![P4V Workspace/Depot View](/larst-of-us/assets/images/PerforceSurvivalKit(16).png)

In most cases, we do NOT want to download the entire depot, but a specific project. To do this, the project/folder can be selected in the depot view and "Get Latest Revision" can be selected in the context menu.

![P4V Workspace View Context Menu](/larst-of-us/assets/images/PerforceSurvivalKit(18).png)

### Connecting Unreal and Perforce

Now that the project is available locally and we want to work on it, we have to make sure that Perforce also receives changes to this project so that we can then upload the changes again.

Theoretically, you can also manually add changed/deleted/created files to a changelist in Perforce, but this is impractical even for the smallest projects.

Unreal offers a Perforce connection by default. This can be accessed via the Source Control menu at the bottom right of the editor and the "Connect To Source Control..." item that appears:

![UE 5 Source Control Menu](/larst-of-us/assets/images/PerforceSurvivalKit(17).png)

In the dialog that now appears, Perforce can now be selected as the source control service:

![UE5 Source Control Selection](/larst-of-us/assets/images/PerforceSurvivalKit(22).png)

The same data must then be entered as in the "Open Connection" dialog in the Perforce Visual Client: IP address of the server, user name and workspace.

![UE5 Source Control Login](/larst-of-us/assets/images/PerforceSurvivalKit(20).png)

After clicking on Accept Settings, Unreal and Perforce should be connected and all changes to assets made in the Unreal Editor should be collected in the default changelist in Perforce. Please note that changes are only recognized or reported to Perforce as soon as a file is saved. To ensure that all changes are actually in the changelist before submitting, the "Save All" command can be used or the editor can be closed.

### Submit a changelist

![P4V Default Change List ](/larst-of-us/assets/images/PerforceSurvivalKit(25).png)
All changes made locally but not yet submitted can be viewed in the default changelist in the Pending tab and can be submitted by right-clicking-\>"Submit":
![P4V Workspace View Context Menu Submit](/assets/images/PerforceSurvivalKit(23).png)

Before this step, however, you should always check whether all changed files should actually be changed, as it often happens that some files are changed and thus checked out while working, which ultimately should not have been changed at all.

If such files are found, they should be reverted before submitting (see next section).

### Discard local changes

If you realize that the changes you have made may be "not that great" and you know you don't want to submit them, you should discard them as soon as possible so that the files on the server are unlocked again for other users.

To do this, you can right-click on the changelist or individual selected files within a changelist and select the "Revert" menu item. This resets all selected files to the unchanged state or restores deleted files.

![P4V Changelist context menu Revert](/assets/images/PerforceSurvivalKit(16).png)

New files (those with the small red plus icon) are an exception. These disappear from the changelist, but the files remain in the respective folder. This is a security measure to prevent the accidental deletion of files. To ensure that files are actually deleted locally, the corresponding option must be set manually in the revert window:

![P4V Revert File Dialog](/larst-of-us/assets/images/PerforceSurvivalKit(28).png)

### Submitting a new project for the first time

If you are already working in a project managed by Perforce, Unreal will automatically report all changes to Perforce and add them to the default changelist. But what if you have just created a new Unreal project in your own workspace and now want to upload it for the first time? Unreal will not have added any files to a changelist at this point because the connection between Unreal and Perforce did not exist when the project was created.

In this case, the files must be manually added to a changelist and submitted. To do this, switch to the workspace view in the Perforce client and search for your newly created project. If it does not show up in the tree structure, you may have to click the refresh button.
 
![P4V Workspace View, Relevant folders to submit](/larst-of-us/assets/images/PerforceSurvivalKit(32).png)
All required files/folders can be selected there and added to a changelist via right-click -\> "Mark for Add..." and then submitted. Please note that not all files in the project folder should be submitted. Many folders such as "Saved" , "Intermediate" and "DerivedDataCache" contain temporary files that are needed for the project to function on the local computer, but are generated automatically by each computer when the Editor is started. The only files that actually need to be submitted are those in the "Config", "Content" and "Source" folders and the \*.uproject file itself. If you have installed plugins in the project, they usually use the same file structure within their plugin folders for which the same rules apply. With the exception that, in the case of plugins, you also want to submit the "Binaries" folder if you do not want to compile it yourself.
![P4V Workspace View, Relevant folders to submit](/larst-of-us/assets/images/PerforceSurvivalKit(30).png)

### Deleting local files

Especially when working on several projects, the local Perforce workspace can quickly swell significantly:

![P4V Workspace folder details window, Showing workspace size](/assets/images/PerforceSurvivalKit(34).png)

If files or entire folders are no longer needed locally, they can simply be deleted using Explorer (Windows) or Finder (Mac). Neither of these programs communicate with Perforce. Therefore, these deletions are not added to any changelist and the files on the server are not changed in any way.

## Best Practices

### Submit frequently

When should changes be submitted? Short answer: As soon as they are so complete/extensive that they can be useful for other people working on the project.

The earlier your own changes are in the project, the earlier other people can notice problems and the earlier you can react to feedback. At the end of a work session at the latest, you should go through the pending changelist again: Can these changes be submitted as they are without breaking anything? Do I really want to lock these files for everyone else until tomorrow by not submitting them now? Ideally, the pending changelist is empty at the end of the day.

###
 Sync frequently

When should I download the latest version?

Similar to the previous question: As often as possible.
 At least at the start of each work session, it should be ensured that the local project is up to date, but depending on the size of the team, it may also make sense to close Unreal and update the project in between before continuing work.

### Meaningful submit descriptions

Unlike other version control systems, Perforce enforces a description for every submit, and for good reason. There comes a point in every project when you wish you knew why a file is in the state it is in. Or why it exists at all. Or what someone was thinking when this or that change was made. Meaningful submit descriptions make exactly this possible. The idea behind the change is usually more important than the description of the change.
 So instead of "Updated Weapon Damage", it would be better to say "Increased sword damage to make defense missions easier".

Or instead of "Changed Inventory UI" better "Added lazy update to Inventory UI to improve performance".

### Go through the files to be submitted

Programs connected to Perforce (not just the Unreal Editor) automatically check out files by default as soon as they are saved. Especially when experimenting or trying out various changes, this can often be the case for files that you did not actually want to submit in the end. To avoid unnecessarily bloating the file history or even accidentally incorporating half-finished experiments into the finished game, it is advisable to go through all the files in the changelist again before submitting and discard any unwanted changes.

### Do not submit large amounts of data that "might be used"

No matter how much bandwidth an Internet connection has: When a dozen people are syncing a 50 GB project at the same time, things will slow down. In general, game content should not be designed differently just because of the memory footprint of the project files (runtime memory is a different beast!). But anyone who uploads a complete asset pack of megascan assets because they needs a single tile from it and "the rest may also be needed" creates easily avoidable waiting times for the rest of the team.

### One change per changelist

A classic situation, especially in the polishing phase of a project: you fix bugs, replace a texture here and there and one level gets a lighting update. And then you submit all these changes, right? Not if you want to keep the project history traceable.

At some point somebody might want to track down the changelist that broke the lighting in level four and 20 submits that are named "various changes to stuff" wont help this team member. Ideally, each submit should only have one "theme".
 So instead of submitting all the changes of the last hour, prefer submitting several small changes that only have one purpose

**Good** :
 "Fixed crash in tutorial"

"Fixed crash in main menu"

"Replaced placeholder foliage textures"
 "Changed lighting in Level 3 to match previous one"

**Bad:**
 "Fixed some crashes, replaced some textures and some other stuff"

### Sync before submitting

Even without direct file conflicts, different changes can get in each other's way. For example, if person A creates a new weapon blueprint while person B changes the underlying weapon system. Theoretically, person A could submit the finished weapon, which works perfectly for them locally - with the old weapon system - only for it to no longer work in the current version of the project. Therefore, before each submit, the project should be updated and opened again briefly to test whether the files submitted are guaranteed to work in the current project version.

### English asset and folder names ONLY

If you are english-speaking person person, you probably wont need to care about this topic anyway. But as a german who often worked in purely german speaking teams, I can't repeat this piece of advice often enough: Enforce an English-only naming convention! The reason is that Perforce is not a big fan "special characters" like German umlauts (ö, ä, ü, ß) or any other characters that are not part of the english alphabet. In theory servers can be switched to enable Unicode support, but this is not default behavior.

Many languages already have workarounds for this limitation in place. For example in german all umlauts can be replaced by a two letter combination (ö = oe, ä = ae, ü = ue, ß = ss), but experience has shown that it is almost impossible to enforce this rule reliable when working. The consistent use of English names makes this whole category of errors impossible from the outset.

## Perforce features for advanced users

### Use "One File Per Actor" for large levels

Traditionally, each level in Unreal consists of a single file with the file extension .umap, which stores all the actors placed in the level, the level blueprint and its settings. This is an obvious and clear structure, but it leads to problems as soon as more than one person wants to work on a level at the same time. In the end, only one of these people can submit the file at the end; all other versions created in parallel must then be discarded or overwrite the submitted version. This is very annoying if a level has to be completed under time pressure but only one team member is allowed to work on it at the same time. You can use sublevels in both UE4 and UE5, partial levels that you load at the same time to assemble the complete level.
 However, it is more convenient to use the "One File Per Actor" feature introduced with 5.0. As the name suggests, the actors placed in the level are no longer saved in the level file, but in individual files, one per actor. These files can be created, edited and submitted independently of each other, as can the actors in the level.

This feature can also be switched on and off afterwards for individual levels ([https://docs.unrealengine.com/5.3/en-US/one-file-per-actor-in-unreal-engine/](https://docs.unrealengine.com/5.3/en-US/one-file-per-actor-in-unreal-engine/)) but it is advisable to consider in advance whether this feature makes sense for a specific level and just create it in the correct mode in the first place.
![P4V Workspace View, Relevant folders to submit](/larst-of-us/assets/images/PerforceSurvivalKit(36).png)

The disadvantage of the feature: The files created are automatically (and cryptically) named and stored in the folders
 Content/\_ExternalActors/Projectname/Maps/Mapname
 Content/\_ExternalObjects/Projectname/Maps/Mapname

This sometimes results in long changelists of several hundred or thousands of files, in which an unintentional change can quickly get lost.

### Virtual Assets

Especially if the edited project aims for a photorealistic style and therefore consists of many (and large) assets, repeated uploading and downloading of the assets can take longer and longer. If possible, you should avoid the problem altogether by not importing any unnecessary or unnecessarily large assets (8k textures!) into the project. But with large projects at the latest, you can't avoid the project forever. And you might start to ask yourself: Do I actually need all the files in the project? Do I have to download all 14 levels if I only want to work on the first one anyway?

The answer to both questions is almost always: Of course not! An example of this is Guerilla Games, who set up their own file syncing system for their work on the Horizon games, which only downloads files when they are needed:
[https://www.gdcvault.com/play/1028848/Scaling-Tools-for-Millions-of](https://www.gdcvault.com/play/1028848/Scaling-Tools-for-Millions-of)
 Setting up such a system yourself is out of scope for most projects, but since version 5.2 Unreal offers a somewhat comparable system: Virtual Assets.

The idea: Each file in the project is replaced by a (very small) placeholder file that refers to the real, large file. Every time a file has to be loaded in the Unreal editor, for example because it is opened directly or because a level containing it is opened, the editor checks whether the real file is present or "only" the placeholder. If only the placeholder is present, Unreal downloads this required file in the background and then opens it as if the file had always been there. This means that you only have to download the files that you actually need to work with.
 Warning: Converting a project to virtual assets is not trivial and can complicate working with the project. It should only be done if you are sure that the feature is needed for the project.
 See the official Unreal documentation: [Overview of Virtual Assets in Unreal Engine](https://docs.unrealengine.com/5.2/en-US/overview-of-virtual-assets-in-unreal-engine/)

### Resolve version conflicts

By default, Unreal blocks you from saving assets that are already checked out from another account. There is a good reason for this, as it means that another version of the asset already exists and - if you edit this asset in parallel - only one of the two versions can be used, the other will be discarded in any case.

There is, however, one exception to this safety mechanism: text-based files (such as .uproject and .ini files).

"Normal" Unreal assets are binary files, roughly speaking large piles of zeros and ones. If there are two different versions of a binary file, you cannot simply combine them, you simply have to choose one version. Neither Unreal nor Perforce can combine these two files in a meaningful way.

This is different in the case of text files, which can usually be combined without any problems. Example: Let's assume that an .ini file has been edited by two accounts in parallel: In one variant, four lines were added at the beginning and in the other, a spelling mistake in line 34 was corrected. In this case, it would be silly to have to choose one of the two variants. The obviously intended result is to accept the four new lines at the beginning and the changed line 34. Perforce (and virtually every other version of control software such as Git and SVN) is capable of merging text changes. Accordingly, Unreal does not block the simultaneous editing of such files.

This means that conflicts can arise - but they can usually be resolved easily.

If a text file is changed on the server that we have checked out at the same time, a warning appears when the new version is downloaded, drawing our attention to the conflict.

![P4V Warning about version conflict](/larst-of-us/assets/images/PerforceSurvivalKit(35).png)

Perforce has noticed that there is a conflict and wants us to resolve it, or to use Perforce terminology, "resolve" it.

The corresponding file(s) are marked in our changelist with a small red question mark:

![P4V Changelist needs resolve](/larst-of-us/assets/images/PerforceSurvivalKit(37).png)

In this state, the file is still unchanged locally, but Perforce will not allow us to submit it until we have resolved the conflict between our version and the server version.

To do this, simply right-click on the file and select "Resolve" to open the Resolve tool:

![P4V Changelist Context Menu, Resolve](/larst-of-us/assets/images/PerforceSurvivalKit(4).png)

![P4V Resolve Dialog](/larst-of-us/assets/images/PerforceSurvivalKit(5).png)

Here, the conflicting versions are juxtaposed and we have to choose how we want to resolve the conflict (red frame).

"Accept Source" -\> We use the version from the server and discard our changes-

"Accept Target" -\> We use our version and discard the changes on the server

"Accept Merged" -\> We accept the automatically combined version that contains the changes from both versions.

"Run Merge Tool" -\> We merge the changes from both versions manually in Perforce's own merge tool.

**If available, you should always use "Accept Merged"**. In 99% of all cases, Perforce will merge both versions without any problems and we don't have to think about the whole Resolve topic any further. Anyone who works a lot with text files (e.g. C++ code) will use this function many dozens of times a day without thinking about conflicts.

However, as already indicated, it can also happen that automatic merging is not available. This is the case if the two versions are in direct conflict with each other, for example, because both have changed the same line. Perforce cannot resolve these conflicts automatically and forces us to resolve them manually in the merge tool.

![P4V Resolve Tool](/larst-of-us/assets/images/PerforceSurvivalKit(6).png)

Perforce's merge tool (like all merge tools of this type) can seem a little intimidating at first. However, the structure is fairly simple. In the upper section, we are presented with three versions of the file. On the far left is the new version from the server (source), on the far right is our local version (target) and in the middle is the original version from which the other two versions originated.

In the lower part we see the partially merged file, and with the red arrows in the toolbar we can move back and forth between the conflicting parts of the file, and then edit the result ourselves until we get a version that does justice to both versions if possible. When we close the tool, Perforce automatically asks us if we want to save the generated result - if we do, Perforce assumes that the conflict has been resolved and the red question mark disappears. From then on we can submit the file as normal.

#### Invisible conflicts

To Perforce, all text files are the same; it does not understand what the contents of the files are. This can lead to "invisible" conflicts that are not recognized as conflicts from Perforce's point of view, for example when the same lines have not been changed but the contents of those lines are contradictory.

Let's take an excerpt from DefaultEngine.ini as an example:

![Except form DefaultEngine.ini](/larst-of-us/assets/images/PerforceSurvivalKit(7).png)

We insert a line locally at the beginning of the RendererSettings section.

![Except form DefaultEngine.ini with added line at the start](/assets/images/PerforceSurvivalKit(9).png)

At the same time, the same setting is added to the end of the section on the server:

![Except form DefaultEngine.ini with added line at the end](/larst-of-us/assets/images/PerforceSurvivalKit(10).png)

As different lines have been changed in both cases, this is not a conflict from Perforce's point of view. Both new lines can be automatically reconciled with each other: 
![Except form DefaultEngine.ini, with both added lines](/larst-of-us/assets/images/PerforceSurvivalKit(11).png)

In terms of content, this is of course rubbish, in no case did we want to set the same setting to two different values in the same file. In this case, the conflict "only" leads to surprising rendering settings, which are probably quickly noticed. Such a case rarely occurs - what is the probability that two people will change the exact same setting at the same time and in two different lines? In C++ code, the probability of such invisible conflicts is higher, as different lines refer to each other more frequently, but they almost always become apparent as soon as the code is compiled.

Checking for such errors manually after each merge is mostly wasted time, since they happen too rarely and parsing the content manually takes a lot of time. But as a general rule code should be compiled after each sync/resolve to make sure that no obvious compile error is submitted.

#### Data Assets

The previous section describes that only text files (such as .ini or .uproject files) can be merged, but not binary assets such as .uasset files. For the most part, this is still true, but Epic Games has been trying to change this for some time to allow the merging of blueprints, for example. Currently (5.3) this is only possible for data assets ([Data Asset Merging - Unreal Engine Public Road](https://portal.productboard.com/epicgames/1-unreal-engine-public-roadmap/c/1118-data-asset-merging)map) but support for other asset types has been announced ([Data-Only BP Merging - Unreal Engine Public Roadmap](https://portal.productboard.com/epicgames/1-unreal-engine-public-roadmap/c/1282-data-only-bp-merging)).

## Troubleshooting

### "Select Unreal Engine Version"

When opening an Unreal project, you may be surprised by this small window that wants to change the engine version of the project:

![Select Unreal Engine Version Dialog](/larst-of-us/assets/images/PerforceSurvivalKit(8).png)

Why does the window appear? Every Unreal project saves the engine version with which it is being edited in the .uproject file. If the engine version specified there is not installed on the PC on which the project was opened, for example because a 5.2 project was uploaded to Perforce and is now to be opened on a PC on which only 5.1 and 5.3 are installed, the project simply cannot be opened. As a workaround, Unreal opens this window where you can convert the project to another installed version.

I am not sure why it does this, since converting a whole project to a different version just because you don't have the proper version installed is a horrible idea!

Switching to an older version will inevitably lead to a broken project, as all assets of the project cannot be opened in an older version. Switching to a newer version is theoretically possible, but is not easily reversible for the reason just mentioned and forces all other team members to use the new engine version (as soon as the changed .uproject file has been submitted).

And I didn't even start to talk about the technical risk of upgrading to another version - In any non-trivial project, some things will break, which may or may not be obvious, and may or may not be easily fixable.

The correct solution: Close the window, install the correct engine version via the Epic Game Launcher and reopen the project. Then everything should work.

### "Cannot submit from non-stream client"

![Warning: Cannot submit from non-steam Client](/larst-of-us/assets/images/PerforceSurvivalKit(12).png)

When creating the workspace, a stream was forgotten to be selected - Perforce therefore does not know which stream the files should be submitted to. To correct this error, a stream can be selected in the workspace settings:
![P4V, Connection Menu, Edit Current Workspace](/larst-of-us/assets/images/PerforceSurvivalKit(1).png)
![P4V, Workspace Settings, Stream Selection](/larst-of-us/assets/images/PerforceSurvivalKit(13).png)

All further submit attempts should then work as usual.

### "Cannot create a file when that file already exists"

![Warning: Cannot create a file that already exists](/larst-of-us/assets/images/PerforceSurvivalKit(19).png)

This error occurs during syncing if Perforce fails to update a file with a new version despite repeated attempts. This can theoretically have various causes, but in practice it is likely that the Unreal Editor is still open and is currently accessing the file in question, which makes it impossible for Perforce to access it. The solution is simple: close Unreal Editor and try again.

### "Open for read: The system cannot find the path specified"

![Warning: The system cannot find the path specified](/larst-of-us/assets/images/PerforceSurvivalKit(21).png)

This error occurs when an attempt is made to submit a file that does not (or no longer) exist. The most common reason is that a file was created and added to a changelist and then deleted or renamed outside of Perforce. To resolve the problem, the file must be removed from the changelist (right-click -\> "Revert").

### After each sync, I am logged into Unreal as Perforce user XY

This problem occurs when user XY accidentally submits the saved folder of an Unreal project. In addition to some temporary files such as profiling data and autosaves, the Perforce connection data of the editor is also stored in this folder. This entire folder should then simply be deleted via Perforce. Incidentally, this data is not a security problem, as long as the same user is not also logged into the local Perforce client, the login status in Unreal has no effect.

### The file I deleted is still there

Similar to creating or editing files, files must also be deleted via Perforce or a program that is connected to Perforce (Unreal, Visual Studio) so that the deletion process can also be reported to the Perforce server.

### Syncing takes forever

Long sync times occur when large project changes have taken place, but if the sync process doesn't want to stop, the problem is usually a different one: instead of specifically selecting a project folder for syncing, the sync command was probably given for the entire Perforce depot, including all projects that have ever been submitted. In this case, the sync process should be aborted and restarted for the correct folder. And, of course, delete the superfluous files that have already been downloaded.

### "The system cannot find the path specified"

![Warning: The system cannot find the path specified](/larst-of-us/assets/images/PerforceSurvivalKit(24).png)

In rare cases, when downloading or uploading a file, a message may appear stating that the file in question could not be found. This occurs when a file has disappeared from the server itself (it will still appear in the history). This problem cannot be solved by the client, in these cases a Perforce admin must be contacted.

Workaround: If it is only a matter of uploading a file that exists on the client, the problem can be worked around by copying or renaming the file to another location and uploading it from there as a "new" file. Then Perforce does not care that it cannot find the previous version of the file.

### Forgotten password

The password can be reset at any time, contact the nearest available Perforce admin.

### All my files are read-only

This is not a problem, but actually a feature. To ensure that all changes to the files managed by Perforce can actually be noticed by Perforce, Perforce assigns write protection to all files, which initially makes changes impossible.
![Unreal error message: File is read-only](/larst-of-us/assets/images/PerforceSurvivalKit(3).png)

All programs that can communicate with Perforce and are connected to Perforce (Unreal Engine Editor, Visual Studio) will automatically remove this write protection when editing and checking out files. Only when you edit a file outside of these programs will you notice this write protection. The correct workflow in these cases is to check out the file in Perforce itself:

![P4V, Manual Checkout](/larst-of-us/assets/images/PerforceSurvivalKit(27).png)

This removes the copy protection and at the same time moves the file to a changelist so that the changes cannot be lost.

In general, write protection should not be removed manually, as in most cases this leads to unrecognized local changes (see next section below). In rare cases, however, such as temporary network problems, it is possible.

### Changes were not recognized

Despite all the routine, it can quickly happen that you are working without a functioning Unreal Force connection or that you have to work offline due to network problems. The latter should generally be avoided, but what if it happens? How do you find out which changes have been made, or how do you inform Perforce that these changes exist and belong in a changelist?
![P4V, Workspace View, Context Menu, Reconcile Offline Work](/larst-of-us/assets/images/PerforceSurvivalKit(29).png)

Fortunately, Perforce can scan any folder for changes and add them to a changelist. To do this, select the folder in the workspace tree, open the context menu and select "Reconcile Offline Work", after a short wait Perforce will show all changes found.

There are two things to bear in mind when selecting the folder.
 1. not all folders contain files that count as a relevant part of the project. This includes all the files that are only needed for local work (autosaves, log files, temp files...). These files should never be submitted and therefore these folders should not be scanned for changes.

Folders relevant for Version Control:

- Content
- Source
- Config
- Build

Folders NOT relevant for Version Control:

- Intermediate
- Saved
- DerivedDataCache
- Binaries

2. scanning large folders may take some time, depending on the size and number of files they contain. If you already know that the changes are limited to a certain subfolder, only these should be scanned in order to keep the waiting time to a minimum.

### Unable to connect to the server

![P4V Error message: Can't connect to server](/larst-of-us/assets/images/PerforceSurvivalKit(31).png)

This error message can theoretically have a number of causes. Faulty Internet connection, incorrect VPN settings, over-cautious firewall. However, if the Internet connection itself seems to be working, the most likely cause of the error is the server itself, in which case please contact your local Perforce admin. Be nice to them, they might have some stress ahead.

### I downloaded the project, but it is empty when I open it

If the Unreal Editor opens after double-clicking on the .uproject file but the project appears completely empty, this is usually because the .uproject file was opened from the depot view instead of the workspace view. As the workspace file can theoretically differ from the depot version, the local file is not opened in this case, but the file is downloaded from the depot to a temp folder and opened from there. As there is of course no Unreal project in this temp folder, the project appears completely empty in the Unreal Engine Editor.

In this case, you can simply close the Unreal Editor again, switch to the workspace view and open the project from there, then it should open as usual.

### My username is wrong/outdated

Perforce has a few really annoying quirks, but one definitely stands out: usernames cannot be changed afterwards, neither by admins nor by the respective users. In this case, simply create a new account and forget/delete the old one.

### "Error saving [asset name]."

![Error saving asset](/larst-of-us/assets/images/PerforceSurvivalKit(33).png)

Theoretically, there can be a number of causes for these error messages, but in practice it is almost always the same one, which doesn't even have anything to do with Perforce:

Accidentally opening two Unreal editors with the same project. In this case, both editors have read access to all loaded assets, which makes write access impossible when saving an asset from the other editor. The solution is simple, just close the other editor, then saving should work as expected.

### "Must resolve before submitting"

![P4V Warning, "Must Resolve before submitting"](/larst-of-us/assets/images/PerforceSurvivalKit(35).png)

This warning is usually more harmless than it appears - and it will rarely occur in pure blueprint projects. Basically, it simply means that a locally changed file has also been changed on the server and Perforce now wants the conflict to be resolved. See the section "Resolving version conflicts" above.
