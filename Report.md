# Final Report

## Contents
1. [Results](#results)
2. [Work](#work)
3. [Lessons](#lessons-learned)
4. [Self-Evaluation](#self-evaluation)

## Results

I spent the summer working on two main projects: improving the Xbox control system for the Movo and creating various tasks for the Movo in simulation.

### [Xbox control system](https://github.com/joshuawisc/movo-control-3)

I built upon the Xbox control system I worked on previously and added various different control schemes to test out. There are 6 different control schemes in total. The specific controller mappings are described in detail in the documentation for the project. Each control scheme places an emphasis on a different part of control. Here are some descriptions:

1. **Normal :** Can control almost all the degrees of freedom for both the arms at the same time. One of the rotation directions for each of the arms is unable to be controlled. The grippers are also controllable.
2. **Point with sticks :** Similar to 1 (Normal) but the emphasis is on controlling rotations, so they are put on the sticks.
3. **Mimicked control :** Places an emphasis on synchronized movements. Similar to 1 (Normal) for the right side (right arm) but the left side of the controller controls both arms simultaneously.
4. **One Arm :** Places an emphasis on controlling the position of one arm, which is why position controls are on the stick. The current arm being controlled must be switched by the user.
5. **One Arm with Rotation :** Places an emphasis on controlling the rotation of one arm. Similar to 4 (One Arm) but the sticks control rotation instead of movement.
6. **Plane Control :** Emphasis is on controlling arm movement along a plane. Similar to 1 (Normal) for the left side but the right side now controls the plane that the arm moves along.

All these control methods provide direct control along the degrees of freedom of the end effector, so there is no automation or shared control. The user also has to manually switch between modes and in some cases the arm being controlled.

### [Tasks in Simulation](https://github.com/joshuawisc/movo-sim-tasks)

To test out the control schemes I was building, I created various different tasks in CoppeliaSim that required different types of control. I created 11 different tasks totally, which are described here. The general goal of the tasks are :

1. **Multiple transfer :** move multiple blocks from a starting location to a designated area
2. **Moving Blocks :** remove blocks from a moving conveyor belt
3. **Stack :** move blocks across the table and stack them
4. **Large Block :** move a large block that requires two arms to pickup
5. **Double Pour :** pour marbles back and forth between a pair of glass cups
6. **Marker :** pick up a marker and draw on the table
7. **Point Touch :** pick up a block of a particular color while pointing at another block of the same color with the other arm
8. **Door :** open and close a door
9. **Drawer :** open and close a drawer
10. **Fork :** stab objects with a fork to pick them and drop them
11. **Spoon :** scoop marbles with a spoon

Although the task goals are achievable in all the tasks, there are still some issues that come up with the physics and the interactions. For example in the spoon task the marbles bounce around wildly when on the spoon and don’t settle on the spoon properly. In the door and drawer tasks the handle isn’t physically constrained to the door, so the arm that grabs can still move around freely and the door will still open even if the arm is not in the exact path where the handle should be.

I also created various objects in simulation for these tasks which can be found in another [repository](https://github.com/uwgraphics/CopeliaSimObjects) and can be reused for other scenes. The scripts and joints are part of the model so the interaction should technically still work.

## Work

At the start of the summer, I had just gotten the Movo working in simulation and I had a basic xbox control scheme. Most of the summer was spent in creating new tasks and new control schemes for those tasks in tandem. Since most of the task types that we had come up with involved interactions with objects, the first thing I worked on was getting grasping to work. I tried to get the natural grasping to work using the physics engine provided in the simulator, but that was too challenging. The items would just fly out of the gripper as the objects didn’t provide enough resistance to the gripper or they would just slip out of the gripper presumably due to the friction calculations at the point of contact. So I decided to just emulate the grasping instead.

Once I got the grasping working I started working on creating different types of tasks. I initially created a simple task requiring some blocks to be moved into a designated area. Then, I started creating tasks depending on what aspect of control we wanted to test. The blocks on the conveyor belt were created to better test the precision of the controls, as the blocks would be continuously moving. The marker scene was created to test how drawing on a plane worked. Since none of these tasks explicitly required bimanual control, I then worked on some tasks that did. The large block scene was created to test using both arms at the same time to move an object. I created the mimicked control scheme at the same time to be able to control both arms synchronously while they were grasping the object. I created the pouring scene to evaluate how well the rotation control worked, and made some rotation specific control schemes at the same time. The stacking scene was created to test whether self-handovers worked with the two arms. The point touch scene is anomalous as it is not a task that would be replicable in real life, but my idea was to create something that would test as many elements as possible simultaneously. Since this tasks requires the subject to pick up an object and point at another object, it tests position, rotation and bimanual control at the same time.


We then started thinking about constrained control. Since many tasks had some physical constraint to them, we thought about adapting the control schemes to tasks and whether that would make the control scheme more intuitive. This lead to me to work on a couple constrained tasks, i.e., the door and the drawer. Although the arms aren’t actually constrained in their movement, the objects still work interactively and move in a constrained path. I also created the spoon and fork scenes as I wanted some scenes with object to object interactions like stabbing something with a fork or scooping “food” with a spoon.

We also worked on creating a task list so that we would know what type of tasks to simulate and create controls for and what type of constraints we would encounter. We created a list of around 40 tasks. Of these tasks, around 38% were constrained. The 3 main types of constraints were revolute (knob, tap), along a line (door, drawer), and on a plane (marker on board).

Over the summer, we also discussed ways of improving the control system that we had by adding some automation or shared control, or by restricting some dimensions of control and placing an emphasis on the important degrees of freedom. We discussed various aspects of good control systems and what we wanted to test out. Some things that we discussed are listed here:

1. **Environmental geometry :** finding planes / surfaces for control constraints by scanning the environment.
2. **Primary / secondary dimensions of control :** for eg, controlling a marker in a plane primarily and then adjusting the angle of the marker on the plane as a secondary control dimension
3. **Control mode switching :** should the program automatically switch modes? Should the program notify the user? Should the user manually switch? Should the program allow the user to manually switch at a particular time? Since research generally shows that mode switching has a high cognitive load, we wanted to find a way to circumscribe this.
4. **Consistency :** maintaining control types across modes, for eg, one side of the controller always controls position while the other always controls orientation
5. **Continuity :** having smooth transitions between modes. For eg, when switching from free space to plane control, position control should be along the same directions instead of some random direction.
6. **Alignment :** how should the direction of stick movement align with the direction of actual movement? screen space vs. world space? How does this change when controlling along a surface?
7. **Sensitivity :** changing sensitivity in different situations. Users may want to make large movement in free space but smaller, accurate movements when on the plane.
8. **Feedback :** how to let the user know when something changes or when some new options are available to the user.
9. **Dominant hand :** should primary dimensions be mapped to the dominant hand of the user?
10. **Pre-mapped movements :** should some movements be recorded and mapped to some buttons. For eg, press A to scoop with a spoon.
11. **Controller types :** how do these aspects apply to an xbox controller vs. a VR controller? What are the pros and cons of each? VR controller allows for complete 6dof control and feels more natural as it is controlled in 3D space. Xbox controller allows for more precision with analog sticks and requires less physical exertion for complex / repeated tasks. It is also cheaper and easier to setup.

## Lessons Learned

I mainly worked in CoppeliaSim for the summer, so I got to learn a lot more about it. I had to find ways to mimic behavior that I wanted and display information that I wanted to see. I learned what features and data CoppeliaSim provided and how I could put it together to build the scenes. Also, working for home was a pretty interesting experience. I usually go to the lab to work even if I don’t have to. Being in a lab where everyone else is also working encourages you to be more focused. I had to learn how to stay more focused while at home as there are many distractions present. It is tempting to not have a start or end work time and just work sporadically throughout the day, but I tried to maintain a schedule as best I could. The online meetings were also a pretty big change. Unlike real life, you aren’t able to have smaller discussions simultaneously when in a large group so everyone usually stays quiet so that the main conversation can take place. Because of this, we had to use Slack for the smaller discussions. So we all had to learn to adapt to this change. I also started reading more papers and taking more notes, so I had to organize them. Since [notion.so](https://notion.so) was popular and we used it for the robot task list, I decided to use it for my general notes as well. Learning Notion was very useful, it is versatile and allows you to arrange your information in various ways and format it to make it easy to understand.


## Self-Evaluation

This summer was quite different for me compared to previous semesters. Because the lab closed, we had to change to simulation pretty quick. The plan was also a bit more vague for this semester. Since we are trying to come up with something new and not just building a predefined project, the work was much more exploratory. I think I could have done better in being more flexible and trying more stuff out. I don’t think I came up with enough good ideas that were strong enough to build upon in the early part of the summer. Though I am generally content with what I accomplished over the summer, it would be more exciting if I can use what I worked on till now to create something greater. I will have to wait and see what happens.
