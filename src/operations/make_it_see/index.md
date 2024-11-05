```{seo}
:description: How to access the Duckiebot camera stream (or, see what it sees).
:keywords: Duckietown, Duckiebot, camera stream, perception, image sensing
```

(make-it-see)=
# Operation - Make it See

This section describes how to see what your Duckiebot sees.

(view-image-using-rqt-image-view)=
## Viewing the Image Stream on Your Laptop

The camera image is streaming from your Duckiebot by default on startup.
To see it, open a terminal on your laptop and run:

    dts start_gui_tools ![DUCKIEBOT_NAME]

```{attention}
For all operation commands that use the Duckiebot's name - input just the Duckiebot's `hostname`, do not include `.local` part that you used previously to access the dashboard.
```

This will start a container with access to the ROS messages of the Duckiebot, including the image stream from the camera. 

Your terminal has now turned into a command line interface running inside of that container within the Duckiebot. You can exit the container back to your normal terminal interface at any time by running the `exit` command. You will learn more about this tool in the [Operation - Tools](ops-tools) section.

To view the camera stream, run the following:

    rqt_image_view

The command will open a window where you can view the image.

You will have to select the `camera_node/image/compressed` topic from the drop-down menu:

```{figure} ../../_images/assembly_setup/rqt_image_view.png
:name: rqt_dropdown_sub

The rqt image view window with dropdown menu - select the `camera_node/image/compressed` topic.
```

### How to save a picture from `rqt_image_view`

On the top right of the `rqt_image_view` window, there is a button to save the current frame to an image. But don't save it yet. A little extra setup is needed to be able to view that file later.

Create a folder on you laptop for where you would like to have the image saved to, say `~/duckiebot_images/`. Then, launch the `start_gui_tools` with the following command:

```
dts start_gui_tools --mount ~/duckiebot_images/:/duckiebot_images [DUCKIEBOT_NAME]
```

Then, run `rqt_image_view` again, and use the top-right "**Save as image**" button to save to the `/duckiebot_images` folder. To find that folder, you might need to navigate to `Computer` and select `/` directory in the pop-up dialogue.

```{image} ../../_images/operations/rqt_image_view_save_btn.png
:align: center
:name: rqt_save_btn
```

<br/>

```{image} ../../_images/operations/rqt_image_view_save_dialog1.png
:align: center
:name: rqt_save_dialog1
```

<br/>

```{image} ../../_images/operations/rqt_image_view_save_dialog2.png
:align: center
:name: rqt_save_dialog2
```

<br/>

And the saved image from your robot's view should appear in the folder you created!

(image-dashboard)=
## Viewing the image stream on the Dashboard

If you followed the instructions in [](duckiebot-dashboard-setup), you
should have access to the Duckiebot dashboard.

Open the browser and visit the page `http://![DUCKIEBOT_NAME].local/`. Login using your Duckietown token, and select robot panel on the left hand side navigation bar. Once selected you should see mission control page there. If you are unfamiliar with the dashboard, you can find more information here: [](dashboard-overview)

The bottom of the page shows the camera block.
You should be able to see the camera feed in the camera block,
as shown in the image below.

```{figure} ../../_images/assembly_setup/dashboard_mission_control_camera_feed.png
:name: dashboard_mission_control_camera_feed
```

By default, the camera stream is throttled down to 8 frames per second.
This is to minimize the resources used by your browser while streaming
images from the robot.
Feel free to increase the data stream frequency in the **Properties** tab
of the camera block.

Note: If you see a black image in the camera block, make sure that you
removed the protective cap that covers the camera lens of your Duckiebot.

(image-novnc)=
## Viewing the image stream in no-vnc (optional)

For instructions on using the no-vnc tool and viewing the image stream from the remote Desktop, see the [Operation - Software Tools](using-no-vnc) section.

## Troubleshooting

```{trouble}
I see a black image like this:

```{figure} ../../_images/assembly_setup/capon.png
:name: camera_cap_on

What you see if you leave the camera cap on.
%```
---
Remove the cap of the camera.
```

```{trouble}
When I try to do `rqt_image_view`, I don't see the window on my machine.
---
Sometimes the window does not successfully spawn on the first try. You can <kbd>Ctrl</kbd>+<kbd>c</kbd> to terminate the process before trying again.
```

```{trouble}
The images are out of focus.
---
The camera focus can be _manually_ adjusted by rotating the lens of the image sensor. As always with dealing with hardware, exercise care and do not use force.  

If the lens will not rotate, you may need to break the glue. Very occasionally cameras come with the lens glued in place. Apply a bit more force the first time you adjust the lens to break the glue's adhesion.
```

```{trouble}
I'm getting an error related to `libGL` when running `dts start_gui_tools`
---
If you have an error like the following when running `dts start_gui_tools` or another command with a GUI on the Duckiebot, then you are likely having issues with an NVIDIA graphics card:

`libGL error: No matching fbConfigs or visuals found libGL error: failed to load driver: swrast nvidia docker`

This could occur on a computer that has two graphics cards, e.g., a NVIDIA GPU and an integrated Intel card. Switch to the Intel card by following the official guidelines for your OS and graphics card.
```

````{trouble}
I don't see any image.
---
Use `rostopic hz /![DUCKIEBOT_NAME]/camera_node/image/compressed` and see if the image is being published. Images should be published at roughly 30 Hz.  If the message is not being published, 

Check that the `duckiebot-interface` is running

Open [the Portainer interface](dashboard-portainer) and check the running containers. You should see one called `duckiebot-interface`, using image `duckietown/dt-duckiebot-interface:daffy-arm32v7`.

You call also determine this by running:

`docker -H ![DUCKIEBOT_NAME].local ps`

and look at the output to find the Duckiebot interface container and verify that it is running.

If that image is not running, you should update your Duckiebot to restart all continers with 

`dts duckiebot update ![DUCKIEBOT_NAME]`

```{seealso}
For more information about `rostopic`, see [](using-no-vnc). 
You can see the images as your robot sees them with `rostopic echo /![DUCKIEBOT_NAME]/camera_node/image/compressed`. 
<kbd>Ctrl</kbd>+<kbd>c</kbd> on the terminal once you've seen enough to confirm the messages are being populated.
```
````


```{trouble}
The camera is not detected from Duckiebot.
---
(`DB18`, `DB19` only) remove the battery pack and check the camera cable for damage.
```
