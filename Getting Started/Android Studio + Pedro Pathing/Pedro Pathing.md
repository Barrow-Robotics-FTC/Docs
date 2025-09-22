---
order: 14
name: Pedro Pathing
---

Pedro Pathing is a library for FTC which allows you to follow paths on the field with your robot.

> Unlike conventional pathing systems such as RoadRunner, Pedro Pathing leverages Bézier curve generation to produce smoother, faster, and more efficient trajectories. Its primary focus is on enhancing the adaptability of robots during autonomous operation by reacting dynamically to environmental changes, reducing error margins, and ensuring optimal path execution. You can learn more about Pedro Pathing [here](https://pedropathing.com/docs/pathing).

For this tutorial, we will use a mecanum chasis with a GoBilda Pinpoint Odometry Computer for feeding position to Pedro Pathing. The process is very similar with other localization systems.

Notes (these will be useful to know for the following steps):
1. Open the `Constants` file located at `TeamCode/src/main/java/org/firstinspires/ftc/teamcode/pedroPathing/Constants.java` in Android Studio
2. Read [this](https://pedropathing.com/docs/pathing/constants) page, it will explain what the `Constants` file entails
3. Replace the current imports of the `Constants` file with the following (Pedro Pathing is not very clear about imports in their documentation):
```java
import com.pedropathing.follower.Follower;
import com.pedropathing.follower.FollowerConstants;
import com.pedropathing.ftc.FollowerBuilder;
import com.pedropathing.ftc.drivetrains.MecanumConstants;
import com.pedropathing.ftc.localization.constants.PinpointConstants;
import com.pedropathing.paths.PathConstraints;
import com.pedropathing.control.PIDFCoefficients;
import com.pedropathing.control.FilteredPIDFCoefficients;
import com.qualcomm.hardware.gobilda.GoBildaPinpointDriver;
import com.qualcomm.robotcore.hardware.DcMotorSimple;
import com.qualcomm.robotcore.hardware.HardwareMap;

import org.firstinspires.ftc.robotcore.external.navigation.DistanceUnit;
```
!!!
Make sure you don't remove the `package` line
!!!
!!!
You may have to replace the `PinpointConstants` import with the localization method of your choice
!!!
4. Set up some of your robot's characteristics using [this](https://pedropathing.com/docs/pathing/tuning/setup)
5. Use [this](https://pedropathing.com/docs/pathing/tuning/localization/pinpoint) page to set ip Pinpoint localization, if you aren't using a Pinpoint, select your localizer [here](https://pedropathing.com/docs/pathing/tuning/localization) and set it up accorrdingly, make sure you change the import statement
!!! Reminder
Don't plug your i2C into port 0 of the control hub, this is used for the IMU and can cause problems
!!!
6. Run the localization test if you haven't already at the bottom of the [localizers](https://pedropathing.com/docs/pathing/tuning/localization#localization-test) page
7. The [Automatic Tuners](https://pedropathing.com/docs/pathing/tuning/automatic) are extremely simple tuners that perform a test and output a value which you can put in your `Constants` file
8. [This](https://pedropathing.com/docs/pathing/tuning/pids) page explains how PIDs work in Pedro Pathing, you should read this page to guide your setup. Using the Dual PID system is recomended for accuracy.
7. Watch [this video](https://www.youtube.com/watch?v=qe2eo_Mhtes) to tune the Translational PIDF controller, once you complete the tuning, save the values as shown [here](https://pedropathing.com/docs/pathing/tuning/pids/translational#update-tuned-values-into-your-code)
7. Watch [this video](https://www.youtube.com/watch?v=-7M8puRdnfA&t=1s) to tune the Heading PIDF controller, once you complete the tuning, save the values as shown [here](https://pedropathing.com/docs/pathing/tuning/pids/heading#update-tuned-values-into-your-code)
8. Use [this](https://pedropathing.com/docs/pathing/tuning/path-constraints) guide to tune the Drive PIDF and braking constants. Braking strength and braking start are the last 2 values of `PathConstraints` in the `Constants` file. Don't worry about filter tuning, just use defaults.
7. Watch [this video](https://www.youtube.com/watch?v=728GLkqy9yY&t=156s) to tune the centripetal factor, once you complete the tuning, save the values as shown [here](https://pedropathing.com/docs/pathing/tuning/pids/centripetal#update-tuned-values-into-your-code)

Thats it for tuning, try all of [these](https://pedropathing.com/docs/pathing/tuning/tests) test programs to make sure you did the tuning correctly, after this, we're ready to code!