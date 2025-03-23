# Week 5 -- Input & Movement --

This practical covers the following:

* How to import sprites (including pixels per unit)
* How to move and rotate an object using Transform.Translate and deltaTime.
* The difference between Self and World modes for translation.
* How to using Quaternion.AngleAxis to create a rotation about Z.
* How to use the Input System to create an abstraction between controls and actions.
* How to poll input using a custom input node (as Vector2 and boolean)
* The difference between IsPressed and WasPressedThisFrame for reading button input.
* How to use If and Select control nodes.

## Foreword

Today we are going to circle back to Visual scripting, and create something from scratch. You were able to learn a bit about creating nodes and navigating a Visual Scripting graph before, but this prac should go over those things, as well as teach some things that are a bit more complex.

Since our repository is empty, we will also be starting from scratch. This project should open in Unity, and when you have opened your project, you will need to download some new sprites.

## Importing Sprites into Unity

While we could simply use empty sprites, it might be nice to add a bit more colour & flair to our game. Sourcing assets is great for this, and thankfully there's a nice set of sprites on Kenny.NL's website that suit our need for our space game. This time however, we're going to have you import the sprites yourself.

### Downloading the asset pack

1. Go to the website and download the sprites [here](https://kenney.nl/assets/space-shooter-redux).
   a) Again, you should always be verifying the license before downloading; these show on the website that the license is under Creative Commons, which means they're fine for use.
   b) Upon clicking `Download`, you will prompted to donate. You can in your own time, but for now select `Continue without downloading.`
2. Using File Explorer/Finder, **Cut and Paste** the download inside your Unity `Assets` folder.
3. Create a new folder called `Sprites`, inside your Asset folder.
4. Move the .zip file into the new `Sprites` folder, and unzip the .zip file.
5. Once the .zip file has been unzipped, delete the .zip file

## Import Settings

Explore the folder and you'll find various files you could use. Take a look at the`PNG` folder specifically, you'll find that there are several sprites neatly categorised and are ready to be placed into your scene.

<img src="PracResources\images\00_PNGFolder.png">

Simply **Drag and Drop** them into your scene from the Project folder, and a new object will be placed into the **Scene** (and the **Hierarchy**) using that sprite. Note that you may want to rename your sprite to something a bit more simple (e.g. "**Player**")

While this sprite could work without issue, it's worth taking a closer look at the **Import Settings** in the **Inspector**. These contain several different options you may need to change when making your own game

#### Import Settings -- Texture Type & Sprite Mode

In the **Inspector**, take a look at the options under **Sprite Mode**. The default settings are fine, and you can leave these as is if you'd like, but you may need to interact with these in the future.

<img src="PracResources\images\00_ImportSettings.png">

If you are curious on what each setting does:

* `TextureType` -- when importing images,  you may sometimes need to click this dropdown and set the type to `Sprite (2D and UI). Needing to do this is extremely common when importing UI assets, for example. If your image will not **Drag and Drop** into your scene, this is usually the first thing you should check.
* `Sprite Mode` has 3 settings that each do various things. By default, it is set to **Multiple**, which is fine for most cases, and can be left on. Changing to another sprite mode should not affect anything, but it's still good to know what each one does
  * `Sprite Mode > Single`: Simply uses the sprite image as is. **
  * `Sprite Mode > Multiple`: As you can likely infer, this should be chosen if the source file has several images, like a sprite sheet. When setting this, you can easily do things like create animation frames using a single sheet that contains multiple poses
  * `Sprite Mode > Polygon`: Choose this when you want to clip the sprite according to the sprites mesh, as defined in the **Sprite Editor** (More on this below).
* `Pixels Per Unit`: Think of this being the base import scale of the sprite. It specifically is asking how many pixels should be displayed for 1 unit in Unity. Try moving your ship to `Position: (0.5, 0.5, 0)`, and change the `Pixels Per Unit` to see this in action. Some further notes:
  * By default. the `Pixels Per Unit` is `100`, which means that, if we had a sprite that was a perfect square, a grid space in Unity would then be able to fit 100x100 pixels of this sprite.
  * In the case of our ship, assuming we left `Pixels Per Unit: 100`, our ship with a width of 100 pixels would also be 1 unit wide.
* `Mesh Type` is not relevant to us now, but this determines how Unity automatically calculates the objects mesh (i.e. the physical boundary/shape of the object).
* `Extrude Edges` is also not relevant to us now, but controls how much area to leave around the sprite in the generated mesh.
* `Pivot` determines where the center of the image should be, in terms of "local coordinates" (more on this later in the prac).
  * If you set your Ship to `Position: (0,0,0)` and change the Pivot of the imported sprite, and you will notice how the part of the ship that intersects (0,0,0) will change
* `Generate Physics Shape`, again, not relevant to us, but generates a physics shape based on your defined Custom Physics Shape.
* The `Open Sprite Editor` button will open up an editor where you can modify the sprite within the Unity Engine itself. This can be used to "**Slice**" images (like breaking up a spritesheet into smaller images), cropping images, or setting up colliders for automatic import (using "Custom Physics Shape").

Again though, most of this is not useful to us right now. None of the other options in this sheet are really relevant to us right now either (there are several quality settings for example). The one you might want to quickly try out is `Advanced > Alpha is Transparency`, which, when toggled on, automatically removed the background of an image and makes it transparent.

## Making our Ship Move

Let's get our ship moving, by making a Visual Script graph. This time around, we'll be doing it from scratch.

In your **Project** window, right click the "**Assets**" folder (or right-click in an empty space) and select `Create > Visual Scripting > State Graph`. Alternatively, you can do this by selecting the top bar in Unity, and selecting `Assets > Create > Visual Scripting > State Graph`.

Before editing this to make our ship move though, we should ensure that our folder structure stays organised. Make a new folder within your Assets folder by right clicking and going `Create > Folder` (or, again, use the top bar in Unity to make your folder), and name your folder `VisualScripts`.

<img src="PracResources\images\03_GraphLoc.png">

Once you've done that, place your "**Player**" Graph inside the new folder. Before you edit your graph, we should save our progress. A good practice to get into is regularly saving our work, as well as committing regularly (as you gathered last week). Save the game scene with the ship in it by making another new folder called `Scenes`, if it does not already exist. Use the save shortcut, or go to the Unity top bar and select `File > Save`, and give your scene a simple name, like `Game`. Once your scene is saved, make a commit in GitHub Desktop (with a meaningful summary/name, like "`Imported Sprite Package`"), and push your work.

Once you've saved and pushed your work, select the new "**Player**" graph, and click the `Edit Graph` button in the **Inspector**.

### Making the Ship Move Forward

Let's start off simple, and simply make the ship move forward constantly, automatically. Let's have a think:

* We want to make it move constantly/often. Therefore we would need something other than simply an `OnStart` node, as this only happens once our game is played. There are a few options to do this, but for now let's simply use an `OnUpdate` event node.
* For the purpose of demonstration, we want it to simply move forward. Considering that X would move it left and right, and Y would move it forwards or backwards, we would probably want to move it along the Y axis.
  * Specifically, we would want to give an input to a `Transform: Translate` node. You can find this node by simply searching `transform translate`. You can check what the node does yourself as well; If you mouse over the `Transform: Translate` node, it'll tell you that it moves this object based on our input, along each axis.
  * All of our inputs should be 0, except for our Y axis. Considering that `OnUpdate` will run every frame, lets make this difference fairly small, like 0.01

<img src="PracResources\images\04_Translate.png">

Now that we have a movement script set up, lets hook this up to our Player.


|   | Consider the following                                                                                                                                                                       |   |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | Would it be a good idea to make our player a prefab? If you decide to this, or have done it already, make sure you're changing the prefab itself, not the**Player** that exists in the scene |   |
|   |                                                                                                                                                                                              |   |

Select your Player object (or Player prefab), and in the Inspector, select `Add Component` and find `Script Machine` (This can be found by searching, or navigating to `Visual Scripting > Script Machine`

<img src="PracResources\images\05_ScriptMachine.png">

This will add both a `Variables` component, and a `Script Machine` component. To actually add our Player graph to our player object though, you'll need to manually drag it into the `Graph` field, under **Script Machine**, or select the `Object Picker button` to the right of the field, and find the Player Graph in the popup box.

Once it is hooked up, it should look something like this:

<img src="PracResources\images\06_ScriptMachineComponent.png">

While we're here, give the `Title` field under `Script Machine` a meaningful title (like "Main Player script"). **Play** your game (by clicking the ▶️ button in the top Unity toolbar), and you should notice your ship move. Quite quickly in fact.


|   | Basic troubleshooting                                                                                                                                                                                                                                       |   |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | If you cannot see your player at all when selecting the**Game** window, make sure that the ship and the camera have an appropriate position. It is recommended that the ship starting Position be (0,0,0), and the Main Camera is set to Position (0,0,-10) |   |
|   |                                                                                                                                                                                                                                                             |   |

You may also notice that if your game slightly stutters, your ship movement might be delayed. This is due to an issue with our logic -- using OnUpdate inherently means that the script runs every frame. But if our computers performance suddenly drops drastically, our player movement will also decrease drastically (as we're moving it by a static amount, every frame).

We can fix this by:

* Instead of a static input for the Y axis, use a **Multiply** Node (which will simply multiply two numbers together)
* Set A of the Multiply Node to be linked to a `Time: Get Delta Time` node (you can find this by simply searching `deltatime` in the Add Node menu).
* Set B of the multiply node to be a value. Just set it to 0.01 for now.

**Play** your game (again, click the ▶️ button), and you'll probably notice that your ship isn't moving anymore, or is it?

Let's find out why. Look at the **Inspector**, and you'll notice that the Position **is** changing. But just at a very small rate. The reason is that the now we're not changing the position enough per frame. This is because `DeltaTime` measures the time in between frames, so we're multiplying 0 by an extremely small number (at 60FPS, it would be 0.0166 recurring). This behaviour isn't ideal.

The obvious fix here is to instead change our multiply node and increase the B input. Let's future proof this a bit though:

* While the player (or player prefab) is selected, in the **Inspector** a new variable for speed by adding a new variable (with Name: "`speed`") to your Player, and set its type to be a float.
* In your Player Graph, change the input of B to instead be `speed`. If you followed the above step, this would be an `Object` variable
* Give `speed` a small value, like 2.

If you've been following along, your graph should look something like this:

<img src="PracResources\images\07.png">

Again, test that this works **Playing** your game. If you're up for a bit of fun, try changing the **Speed** variable during run time, and you'll notice your ship increase or decrease in speed as it moves.

## Rotating the ship

Set the speed back to a fairly small number for now (e.g. 0.5), we'll revisit movement shortly. Let's now try and make our ship rotate.

Find the `Transform: Rotate` node, specifically the one that contains inputs for (Eulers, Relative To).

<img src="PracResources\images\07_RotateSearch.png">

As one could assume, this will use the Transform component, and change the rotation of our object. It should look something like this when connected to the `Translate` Node:

<img src="PracResources\images\08_Rotate.png">

Rotating an object can be a bit complicated, so it's understandable that this part may be confusing. Just note that we won't be covering more complicated topics like Quaternions in depth.

#### The difference between Self and World Mode when translating.

Before we properly set up our rotation, lets briefly talk about relativity when manipulating Transforms. Looking at our rotate node again, you should see how the input for `Relative To` has a dropdown that currently says `World`.

There are two relativity settings here we can choose from, `World` and `Self`:

* `Relative To: World` changes an object based on its Transform and the scene axes. **Using World ignores the objects own orientation, and instead changes the Transform relative to the origin of the game scene (0,0,0)**.
  * For example, if we have an object and use `Relative To: World` and set its Position to (1,0,0), it will simply snap to that position. This could be troublesome if it a child of an object, as it would move **directly to that position in the scene**, regardless of the orientation of the parent.
* `Relative To: Self` changes an object based on its its *local* Transform. **It uses its own orientation to apply Transform manipulation**.
  * The result would be different to the example above, since it would not move independently to its parent.
  * Copying the above example, if we had a parent that was at (2,2,0), our child object would move to (3,2,0) if set to `Self`. Again though, this would be different if we instead set this to `World`, as the child object would simply move to `(1,0,0)`

This is quite tricky to visualise, and especially hard to demonstrate when we're only rotating along one axis (which we discussed in our first prac, typically we should only want to rotate along the Z axis when working with 2D). We're going to temporarily break this rule just to help demonstrate this rule.

Keep `Relative To` set to `World` for now, but change your node so the third input field for `Eulers` is set to `1` (We won't be explaining Eulers in depth,  as it's a complicated topic).

<img src="PracResources\images\08_Rotate001.png">

**Play** your game, and select your player and watch it rotation, you should notice that *only* the Z rotation moves.

Now while your game is playing, open up the Graph Editor and change `Relative To` `World` to `Self`. Look at the **Inspector** again, and you'll notice that... nothing's really changed.

Stop play mode, and in the **Inspector**, change the **Rotation **of your players X Y and Z axis (for example, `Rotation: (50,50,50`). Play again and you'll notice that even though the sprite looks a bit strange in this rotated state, its Z axis is still updating accordingly.

Finally, while still in play mode, change `Relative To:` back to `World` and look at the **Inspector**. You'll notice that the Rotation field is behaving quite strangely. Keep changing between the two and you'll realise how different they behave from one another now.

We didn't see this before, as our X and Y was set to 0. However, this is essentially what is meant by the difference of rotating based on the origin of the scene, and rotating based on the orientation of your object.

```text
Before moving on, set your Player Rotation back to (0,0,0)
```

### Applying rotation based on the Angle

Now that we've covered that, you should understand which one you should probably use for now (please use `Self`).

We could leave it there and simply let it keep rotating based on Euler, but this isn't very good, as it's hard to visualise how much it's really rotating by. Thankfully, we can use a node to feed in an angle instead.

Find the node `AngleAxis`, and Add the node.

<img src="PracResources\images\11_AngleAxis.png">

Using this node, we can then directly convert a rotation to a "**EulerAngle**, which is the input required for `Transform: Rotate`

Do the following:

* In the **Inspector**...
  * create a new variable for the Player called `rotationPower`, and give it type `Float`. Give it a value of 10
* In your **Player** visual script...
  * near the `Angle Axis` node, create a new `Multiply` node. Similar to how we multiplied for movement, give our multiple node inputs for `rotationPower` and `DeltaTime`
  * Grab the `Axis` input on the `AngleAxis` node, and drag it away. Release your mouse to prompt the `Add Node` window, and add a `Vector 3: Get Forward` node, the equivalent of (0,0,1) (You can see what this looks like in the below image)
  * Feed the `Result: Quaternion` output of the `Angle Axis` node into new `Get Euler Angles` node, Again, you can see what this node looks like in the image below, but it essentially converts our prescribed angle, into value we can pass directly into our `Transform: Rotate` node. (Note: Don't forget to use the Graph inspector to check which output is which, as there are two outputs for this node).
  * Feed the output of the `Get Euler Angles` node into the `Eulers` input of the `Transform: Rotate` node.

Try and do the above without checking the image below, which shows what you should end up with

< Image | InitialFinishedRotation >
<img src="PracResources\images\12_PrelimRotate.png">

**Play** your game and watch your ship in action. Your ship should gradually move forward and gradually turn to the left, before exiting the game view.


|   | For the curious                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |   |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | Take another look at what we've done here, and consider why we used Angle Axis in the first place. It*could* be possible to simply feed in the prescribed angle as a rotation value, by directly changing the Z axis of our rotation angle. While this is certainly doable, this is a circumstance where there are several more upsides to this approach, namely computation efficiency, as well as readability and simplicity -- It would certainly be wasteful considering we are only rotating one axis. |   |
|   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |   |

```text
Don't forget to Save, Commit and Push your work!
```

## Moving our Ship with Inputs

We're nearly done with our basic movement, but we can't really control our movement without reading inputs.

### Reading Input Axes

Let's start slow, by getting Unity to read our Inputs. We can do this by creating a `Debug Log` node.`Debug: Log` allow us to print useful data to the **Console** window, and help us easily see if things are not working

In your **Player** visual script, add a Debug Log node. For now, we want to see if our button presses work at all. Add an `Input: Get Axis` which we've used previously, and link the output of this to the `Debug: Log` node. After you've done that, give the input of your `Get Axis` node the string `Horizontal`, which if you remember, scans for input on the A and D keys.

<Image - Initial Debug log>
<img src="PracResources\images\20_DebugLog.png">

Before you **Play** your game, make sure you can see your **Console** window. Play your game, and look at the **Console** window. You should observe the following:

* When nothing is held, it should show 0
* When Left (A) is held, you should see a negative number, down to -1
* When Right (D) is held, you should see a positive number, up to 1

Go back to your Player visual script, and instead change the string connected to `Get Axis` to `Vertical`, and play your game again but by using W and S. You should notice something similar.

Let's combine these axes together so we can read them both at the same time, by feeding our inputs into a `Vector 2` -- a set of 2 axes. This is quite sensible, as by default all unity object Transforms have a matrix of Vector 3's, but as we aren't using the Z axis, there's no need for our inputs to be a Vector3.

Do the following:

* Add a `Vector2: To String` node. This will print out our Vector2 into a format that our `Debug: Log` will be able to read
* Click and Drag the input of the `Vector2: To String` node, and release to prompt the Add Node window to open with context. You will want to select `Create Vector 2: (X,Y)` Node.
* Make the `X` input of our `Vector2: Create` node an `Input Axes` node, that has an input of string axis `Horizontal`
* Similarly, make the `Y` input of our `Vector2: Create` node an `Input Axes` node that has an input of string `Vertical` -- Note that you can copy paste the node(s) you made before, and simply change the input string

Again, try this yourself. When you've completed that, you should have something like the following:

<Image: both axes being read>
<img src="PracResources\images\21_DebugLogWithVector.png">

**Play** your game, and try holding several combinations of buttons. If you're holding both up (W) and right (D), you should notice that the input becomes `(1.0)(1.0)`.

```text
Don't forget to Save, Commit and Push your work!
```

### Moving our Ship Forwards and Backward

Now that we've tested our controls, we're finally ready to start making them implementing into our game.

Consider the following logic, then try and implement the movement scheme yourself:

* Our ship is already moving based on a `Speed`, and over time (using `DeltaTime`). This is fine, and shouldn't be changed
* The result of our speed and the DeltaTime (time between frames) is a float (number with decimals). Based on what we saw in the console, our input is also a float.
* From our `Debug: Log` test, we learned that we can move positively (using W), and negatively (holding S), by just implementing in one axis. We know that the output of `Speed * deltaTime` will always be a positive number, but maybe we can change it after that multiplication so we can move backwards.

Think about this for a moment, and try and come up with a solution, without looking at the below image.

A simple implementation could look like this, and is completely reasonable.

<Image | MovementWithAxesInput >
<img src="PracResources\images\30_Movement_Verify.png">

Try it out by entering **Play** mode. If done correctly, you should be able to move both forwards and backwards. Don't worry if the movement feels a bit sluggish, we'll change these values after we implement turning.

### Turning our Ship Left and Right

Similar to above, our ship is already rotating. Think about the following logic, then try and implement it yourself:

* We might want to change which direction we rotate, since we want to do this simply. Knowing that our `Input: Get Forward` is set to (0,0,1), and that if we move left, our input is `-1` along the Horizontal axis, we won't need to change `Angle Axis` directly -- `Input: Get Forward` is fine as is.
* Instead we should change our angle. It would make more sense to feed our angle axis a negative or positive angle, similar to how we handled the forwards/backwards movement.

Have a go and see what you come up with. Use **Play** mode to test your implementation.

Your initial implementation might not be intuitive, since our "left" key (A) actually moves the nose of our ship to the right. While this doesn't seem correct at first (we know that the positive direction on a 2D plane, X is positive towards the right, not the left), this is technically correct if you look at the **Inspector**; our object *is* being rotated positively -- this is a result of us rotating along the **Z axis**.

There are several solutions to this issue, and you're welcome to choose whichever suits you best:

1. Simply replace the `Vector3: Get Forward` with a `Vector3: Get Back` node. This is a simple and logical approach, but not an ideal one since, again, we don't really want to change any other input with `Angle Axis`, just the given angle itself.
2. We could set `rotationPower` to be a negative number instead of a positive. This is a fair approach, but arguably unintuitive. If you were working on a game with someone else, it would be fairly easy to think this was a bug, and might lead to accidental issues in future.
3. Multiply our `InputAxis` by integer `-1`, before multiplying it again by the output of `rotationPower * DeltaTime`. Considering we're feeding in a new input in the first place, it would make more sense to change this rather than anything else, since everything else was working fine prior
4. While you *could* just reverse the input of A and D in your Project Settings/the input manager, don't do this. This will cause more work in future steps, and is simply a bad practice.

Personally, the third option makes the most sense, even though it isn't the most elegant, but you're allowed to choose whichever options suits your fancy (except 4.). Just note that the below example uses 3.

<Image - RotationUsingOptionThree>
<img src="PracResources\images\31_RotateWithInput.png">

At this point, you might also want to increase/decrease your speed and rotationPower variables.

```text
Don't forget to Save, Commit and Push your work!
```

## The 'New' Unity InputSystem

The default Unity InputManager (what we're currently using) is fine for most things, but can be quite limiting and slow to work with. It is definitely a bit outdated.

You might remember from the first week that there existed an `InputSystem` file in your Project Directory. Considering this is a prac about inputs and movements, it's about time we had a proper look at it.

The InputActions file (the file called `InputSystem`) can be found in the root of your **Project** folder (i.e. in the main `Assets` folder). Double click the `InputSystem` file, and you should see something like this

<Image - InputSystemFirstGlance>
<img src="PracResources\images\40_OriginalActionMap.png">

If you have a poke around, you'll probably see that this has a lot more features than the default input manager (again, you can find the old input system at `Edit > Project Settings > Input Manager`).

You might also notice that we can filter by control schemes. This is great if we're developing a multi-platform game, as we can quickly filter out which input system we need.  While this might sound a bit nonsensical, you can imagine that this list would fill up quickly if you were making a game for PC, Console and Mobile (that features touch controls), which isn't uncommon nowadays.

While the prescribed InputSystem is technically ready for us to use, it would make more sense if we make one ourselves/from scratch, that way we can learn how to expand it. Delete it, and create a new one.

To create a new InputActions Asset, go to create a new object in your **Project** window (the same way you've created other new files) and select `Input Actions` -- it is located at the very bottom of the list.

You can leave this file at the root of your folder, or place it in a unique folder if you wish (if you make a folder, call it something like "`InputActions`"). Either way, give it a sensible name, like "`PlayerInput`".

Select your new InputSystem, and look at it in the **Inspector**. You should see something similar to the following warning. Press the highlighted `Assign as the Project-wide Input Actions` button (this will assign it as the new default InputSystem, instead of the one we just deleted), and then press `Edit Asset` to open the Input System.

<img src="PracResources\images\42_InputActionSetting.png">

### Creating Input Actions

Before creating the input actions themselves, we need to make an Input `Action Map`. This is quite simple, in the column titled `Action Map`, press the `+` button in the top right, and give your **Action Map** an appropriate name like "Player".


|   | What's the point of Action Maps?                                                                                                                                                                                                                                                                                            |   |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | While seemingly a pointless addition to getting started, Action Maps are useful in categorising your different inputs. Some brief examples could include differentiating touch and gamepad controls (Important for consoles like the Nintendo Switch), or differentiating between Player inputs, and User Interface inputs. |   |
|   |                                                                                                                                                                                                                                                                                                                             |   |

Once you've created your `ActionMap`, you should be able to start making actions in the `Actions` column.

<Image - InputActionsAfterMap>
<img src="PracResources\images\43_BlankInputAction.png">

Considering that before we were using axis, lets reimplement that approach:

* Rename the already existing action `New Action` to `Movement`.
* Select it, and in Action Properties:
  * Set `Action Type` to `Value` -- the other options don't really make sense here
  * Set `Input Type` to `Axis`, again, since we're reimplementing the approach from before

Right-Click `Movement`, and select `Set Positive/Negative Binding`. A sub-category should appear within the `Movement` dropdown called 1D Axis. Click the `>` arrow to the left hand side of this new sub-category, and you should see children of this sub-category: A Positive and Negative field, with no binding

<img src="PracResources\images\44_InputAction1d.png">

Select `Negative: <No Binding>` and change the `Path` dropdown. This will open up a popup menu, similar to the `Add Node` popup menu in our visual scripting graph, where you can find inputs for specific inputs.

We want to map `Negative`to `S` on our keyboard. There are a few ways you could map this (again, similar to `Add Node`). The simplest way to do it would be if we can simply record an input, and link it to that. Let's do exactly that.

Press the `Listen` button in the `Path` popup menu (highlighted below), and press the S key.

<img src="PracResources\images\45_InputActionListen.png">

Two results should appear. Select the one that says `S [Keyboard]`, since the other one obviously just triggers off of any key being pressed.

Repeat the same process for the W key, but for the positive input action. Your Action Map should look something like this when you're done

<img src="PracResources\images\46_InputActionPosNeg.png">

While we're here, lets also add an Action for **Rotation**:

* Make a new Action called `Rotation`.
* Like before, set the **Action Properties** of `Rotation` --  `Action Type: Value` and `Input Type: Axis`
* Right-Click `Rotation`, and select `Set Positive/Negative Binding`
* Set the `Negative` binding to `A [Keyboard]`
* Set the `Positive` binding to `D [Keyboard]`

Before moving on, ensure you have something like this:

<img src="PracResources\images\47_InputActionMoveRot.png">

With this type of file, you need to ensure you save it. You can use the default save shortcut, or press the `Save Asset` button. While you're saving, it wouldn't be a bad idea to click the `Auto-Save` button (highlighted above)

Finally, we need to add our `PlayerInput` **Input Actions** file to our player. Select the Player (or Player prefab), click the `Add Component`, and add the `Player Input` component. It should automatically apply your **Input Actions** asset, and select your `Player` **Action Map**.


<img src="PracResources\images\48_PlayerInputComponent.png">

```text
Don't forget to Save, Commit and Push your work!
```

### Using InputActions in our VisualScript

With our new InputActions file mapped, open up our Player Visual Script again. This is quite a simple change since we've designed our movement system already, but we're simply going to just swap out out `Input: Get Axis` nodes for `OnInput System Event Float` Nodes.

There are three types of `OnInput System Event` Nodes:

* `On Input System Event Button`: As you might guess, is used for reading button presses
* `On Input System Event Float`: Is used for reading a single float (number)
* `On Input System Event Vector 2` Outputs a vector2. Useful for reading controller inputs where we might need to move omnidirectionally (e.g. move up and left at the same time)

Consider what the output our `GetInput : Axis` nodes are, and replace them with which node type you think is most suitable. The answer is below, but have a think about what you're implementing first.

Remove all of your Input: Get Axis nodes, and replace them with the appropriate `On Input System Event...` node. Make sure you do this for `Transform:" Translate`, `Transform: Rotate`, and your `Debug: Log` nodes.

(Hint: Considering that we made both our `Movement` and `Rotation` both a 1D axis, and we're scanning an axis, not a button press, the answer should be straight forward)

(Additional Hint: Think about what input we're using to make our Vector 2 for our `Debug: Log` node).

**Play** your game once you've completed this step - you may have to change your `speed` and `rotationPower` values again.

```text
Don't forget to Save, Commit and Push your work!
```

## Add a new "boost" control to the control mapping.

Now that we've simplified our input creation pipeline, let's have a go at adding more features. I don't think we can really have a powerful spaceship without adding a **Boost** feature, so lets add that:

* Open up your `PlayerInput` **Input Actions**, and make a new `Action`. Call it `Boost`.
* Set the **Action Properties** of `Boost` to be `Action Type: Button`
* Make `Spacebar [Keyboard]` the binding for `Boost`


<img src="PracResources\images\50_BoostAction.png">

* In your Player/Player prefab, make a new variable called `boostSpeed`. Set its type to `Float`, and make its default value higher than `speed`.
* Make another variable, called `isBoosting`. Set its type to `Boolean`, and make its default value false (unticked).


<img src="PracResources\images\51_BoostVariables.png">

Once you've completed those steps, open up the Visual Graph on your player, and somewhere near the `OnUpdate` node, add **TWO** event nodes for `On Input System Event Button`. (again, this is the most sensible since we want to track a button press).

If you click the dropdown underneath the title for the node, you should see three different settings -- changing this will track what input change our Player is waiting for:

* `On Pressed`: Runs the event sequence when our Input Action is pressed (in our case, `Spacebar` is pressed)
* `On Held`: Runs the event sequence when our Input Action is held.
* `On Released`: Runs the event sequence when our Input Action is no released (be it after being held, or pressed).


<img src="PracResources\images\52_OnInputTypes.png">

For now, we simply want to be boosting while `Spacebar` is held down, and and disabled when released. Add a node for `Set: IsBoosting`. We've used `Get` nodes before which reads an input, but a `Get` variable node lets us change the property of a variable instead.

<img src="PracResources\images\53_SetVar.png">

Drag and release the grey/`New Value` input node for `Set Variable`, and select `Boolean Literal` in the contextual **Add Node** menu. The `Boolean Literal` node allows us to directly set a boolean using whatever value we set in there. In the below image, it is setting a value to `True`


<img src="PracResources\images\54_BoolLiteral.png">

Do the following:

* When `Boost` is changed to `OnHold`, set the variable for `IsBoosting` to `True`
* When `Boost` is changed to `OnReleased`, set the variable for `IsBoosting` to `False`

The implementation should look something like this

<Image | OnHoldAndOnReleasedFinalised>
<img src="PracResources\images\54_BoostTriggers.png">

With the first part done, we just need to add a way that out Update loop reads the boolean (based on what was described above regarding `Set` nodes, you probably know where this was going).

Break the connection between `Get Variable: boostSpeed` and the `Multiply` node, and replace it with a `Select` node (Note: There are a few types of `Select` nodes. The one we're looking for is simply called `Select`)

<Image | Select Node>
<img src="PracResources\images\55_Select.png">

As implied by the name, `Select` nodes lets us select a single value from a set of options, based on a criteria. The default `Select` node lets us simply pass a boolean, perfect in our circumstance:

* Link the **boolean** input of the `Select` node to **Get** the `isBoosting` variable
  * If `isBoosting` is `True`, we want to be using our `boostSpeed`
  * Otherwise, if `isBoosting` is `False`, we want to be using our default `speed`

Have a go at implementing this yourself, but of course, the solution is down below

<Image | Select implementation>
<img src="PracResources\images\56_SwitchImplementation.png">


|   | Why not use If                                                                                                                                                                                                                                                                                                                                                               |   |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --- |
|   | Those who are a bit more programming savvy would look at this implementation and think something along the lines of "isn't this just an IfElse statement? Why not use If?". If you look at the If node however, you will see that it's only outputs are to continue the flow of an operation, and isn't really ideal for switching between an option of variables like this. |   |
|   |                                                                                                                                                                                                                                                                                                                                                                              |   |

**Play** your game, and try out the new boost functionality. At this point our visual script is also getting quite bloated. It's recommended to keep making groups with relevant group headings when necessary. Remember, you can make a group in your visual graph by holding `Ctrl` (or `Cmd` for mac), and dragging the mouse while holding `Left-Click`.

Spend a few minutes grouping your Player functions. It would be ideal to split it up into at least four different groups:

1. Movement
2. Rotation
3. Debugging
4. OnInput isBoosting

<Image | Default PlayerMove finalised>
<img src="PracResources\images\60_FinalMovement.png">

```text
Don't forget to Save, Commit and Push your work!
```

These steps might have taken you a while. If you feel like your ahead, try and attempt one of the challenges for this week (or multiple)

## Challenge (optional): make turn rate based on speed, so the sprite can't rotate on the spot.

## Challenge (optional): Make the boost last for X seconds after pressing the button, even if the button is released.

## Challenge (optional): Make the player use a joystick/gamepad, instead of a keyboard

Ask your tutor if there are controllers available in the lab (there should be).
Using the Input Action system, implement either an additional action map, or additional actions for your control scheme, and make this use a controller.

## Challenge (optional): Make the ship move omnidirectionally (e.g. push the joystick in a direction, the ship moves in that direction)

Make the ship move diagonally, instead of what we've made in the prac (i.e. An implementation of "tank controls"). Note that this is definitely recommended if you are using a controller

## Intermediate Challenge (optional): Add a Second Controller and a Second Player

While you could *technically* implement this fairly easily with just a keyboard, or a keyboard and a controller, it is recommended that you try implementing this with two controllers. Note that you will need two separate player objects in your scene.

However, will you necessarily need a new player script? Or could you simply modify your existing one?

```text
Don't forget to Save, Commit and Push your work!
```

## Major Challenge (optional) - An Enemy Ship

Our game would be a lot more fun if there was more interactivity in it. Using the Custom Node scripts in the `ΙTools` folder, it is possible to make a Ship that tracks the player movement, and rotates towards them:

To start, you'll need to set up the following:

* Add a different color ship, call it **Enemy**
* Give this a new Visual Script Graph, give it an appropriate name
* Add Variable `Player` Type `Transform` to the object

### Enemy Ship Movement

You can move the ship quite easily by doing the original implementation of our Player movement script.

It would make a bit more sense if the enemy ship moves/chases you the further the player is from the enemy. To do this, you will need to calculate the distance between the Player ship and the Enemy ship.

There are a few ways you could implement this, but all solutions would need to manipulate a vector somehow.

* Research Lerp/Lerping, and applying World Space to Local space
* It is recommended you include a minimum distance (as a variable)
* It is recommended that you only move the ship forwards (not backwards). Otherwise, it should stop
* Your initial implementation way start and stop abruptly. Could you make it slow down smoothly as it approaches its target, and stop at the minimum distance?

### Enemy Ship Rotation

You will need to detect if the player ship is on the left or the right of the player. In order to calculate this, give the `IsOnLeft` custom node the position for both the Player and the Enemy.

From here, you have a few options, but you'll likely need to use an If node and rotate based on the output of the `IsOnLeft` node.

Note that the rotation can be done before or after the movement, but is probably harder to do beforehand.
