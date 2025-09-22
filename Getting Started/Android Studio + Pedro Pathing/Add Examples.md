---
order: 13
name: Add Examples
---

Before you start coding, we recomend that you create a new folder in your teamcode folder called `examples`, here you can store any examples/tempaltes you want to use. We have modified versions of the official Pedro Pathing Autonomous and TeleOp sample codes shown below which are great starting points for your own projects. We converted the samples to Linear OpModes and improved code/comment quality.

!!! 
To use these samples, you must remove the `@Disabled` annotation to display them on the Driver Hub. They are also marked with `// REMOVE THI LINE TO SEE ON DRIVER HUB`
!!!

Autonomous
```java
package org.firstinspires.ftc.teamcode.examples;

import com.pedropathing.follower.Follower;
import com.pedropathing.geometry.BezierLine;
import com.pedropathing.geometry.Pose;
import com.pedropathing.paths.Path;
import com.pedropathing.paths.PathChain;
import com.pedropathing.util.Timer;

import com.org.firstinspires.ftc.teamcode.pedroPathing.Constants;

import com.qualcomm.robotcore.eventloop.opmode.Autonomous;
import com.qualcomm.robotcore.eventloop.opmode.LinearOpMode;

@Autonomous(name = "Example Autonomous", group = "Examples")
@Disabled // REMOVE THI LINE TO SEE ON DRIVER HUB
public class AutoExample extends LinearOpMode {
    // Initialize poses
    private final Pose startPose = new Pose(28.5, 128, Math.toRadians(180)); // Start Pose of our robot.
    private final Pose scorePose = new Pose(60, 85, Math.toRadians(135)); // Scoring Pose of our robot. It is facing the goal at a 135 degree angle.
    private final Pose pickup1Pose = new Pose(37, 121, Math.toRadians(0)); // Highest (First Set) of Artifacts from the Spike Mark.
    private final Pose pickup2Pose = new Pose(43, 130, Math.toRadians(0)); // Middle (Second Set) of Artifacts from the Spike Mark.
    private final Pose pickup3Pose = new Pose(49, 135, Math.toRadians(0)); // Lowest (Third Set) of Artifacts from the Spike Mark.
    
    // Initialize variables for paths
    private Object scorePreload;
    private Object grabPickup1;
    private Object scorePickup1;
    private Object grabPickup2;
    private Object scorePickup2;
    private Object grabPickup3;
    private Object scorePickup3;
    private Object returnHome;
    
    // Other variables
    private Pose currentPose; // Current pose of the robot
    private Follower follower; // Pedro Pathing follower
    private Timer pathTimer;
    private Timer actionTimer;
    private Timer opmodeTimer;
    private int pathState; // Current state machine value

    @Override
    public void runOpMode() {
        // Initialize timers
        pathTimer = new Timer();
        opmodeTimer = new Timer();
        opmodeTimer = new Timer();
        
        // Initialize Pedro Pathing follower
        follower = Constants.createFollower(hardwareMap);
        follower.setStartingPose(startPose);
        
        // Create paths
        buildPaths();
        
        // Log completed initialization
        telemetry.addData("Status", "Initialized");
        telemetry.update();
        
        // Wait for the game to start (driver presses START)
        waitForStart();
        runtime.reset();
        
        // Reset timer and set path state to 0 after start
        opmodeTimer.resetTimer();
        setPathState(0);

        while (opModeIsActive()) {
            // Update Pedro Pathing follower
            follower.update();
            currentPose = follower.getPose(); // Update the current pose
            
            // Update the state machine
            updateStateMachine();
            
            // Log to driver station
            telemetry.addData("Path State:", pathState);
            telemetry.addData("X: ", currentPose.getX());
            telemetry.addData("Y: ", currentPose.getY());
            telemetry.addData("Heading: ", currentPose.getHeading());
            telemetry.update();
        }
    }
    
    public void shootArtifacts() {
        // Put your shooting logic here
        return;
    }
    
    public void intakeArtifacts() {
        // Put your instake logic here
        return;
    }
    
    public void buildPaths() {
        // Move from the starting position to the scoring position
        scorePreload = new Path(new BezierLine(startPose, scorePose));
        scorePreload.setLinearHeadingInterpolation(startPose.getHeading(), scorePose.getHeading());
  
        // Move to the first artifact pickup pose from the scoring pose
        grabPickup1 = follower.pathBuilder()
                .addPath(new BezierLine(scorePose, pickup1Pose))
                .setLinearHeadingInterpolation(scorePose.getHeading(), pickup1Pose.getHeading())
                .build();
    
        // Move to the scoring pose from the first artifact pickup pose
        scorePickup1 = follower.pathBuilder()
                .addPath(new BezierLine(pickup1Pose, scorePose))
                .setLinearHeadingInterpolation(pickup1Pose.getHeading(), scorePose.getHeading())
                .build();
    
        // Move to the second artifact pickup pose from the scoring pose
        grabPickup2 = follower.pathBuilder()
                .addPath(new BezierLine(scorePose, pickup2Pose))
                .setLinearHeadingInterpolation(scorePose.getHeading(), pickup2Pose.getHeading())
                .build();
    
        // Move to the scoring pose from the second artifact pickup pose
        scorePickup2 = follower.pathBuilder()
                .addPath(new BezierLine(pickup2Pose, scorePose))
                .setLinearHeadingInterpolation(pickup2Pose.getHeading(), scorePose.getHeading())
                .build();
    
        // Move to the third artifact pickup pose from the scoring pose
        grabPickup3 = follower.pathBuilder()
                .addPath(new BezierLine(scorePose, pickup3Pose))
                .setLinearHeadingInterpolation(scorePose.getHeading(), pickup3Pose.getHeading())
                .build();
    
        // Move to the scoring pose from the third artifact pickup pose
        scorePickup3 = follower.pathBuilder()
                .addPath(new BezierLine(pickup3Pose, scorePose))
                .setLinearHeadingInterpolation(pickup3Pose.getHeading(), scorePose.getHeading())
                .build();
                
        // Move to the starting pose from the scoring pose
        returnHome = follower.pathBuilder()
                .addPath(new BezierLine(scorePose, startPose))
                .setLinearHeadingInterpolation(scorePose.getHeading(), startPose.getHeading())
                .build();
    }
    
    public void updateStateMachine() {
        switch (pathState) {
            case 0:
                // Move to the scoring position from the start position
                follower.followPath(scorePreload);
                setPathState(1); // Move to the second path state
                break;
            case 1:
    
                /* Ways to check for path completion:
                - Follower State: "if(!follower.isBusy()) {}"
                - Time: "if(pathTimer.getElapsedTimeSeconds() > 1) {}"
                - Robot Position: "if(follower.getPose().getX() > 36) {}"
                */
    
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Shoot preloaded artifacts
                    shootArtifacts();
    
                    // Move to the first artifact pickup location from the scoring position
                    follower.followPath(grabPickup1, true);
                    setPathState(2); // Move to the third path state
                }
                break;
            case 2:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Intake first set of artifacts
                    intakeArtifacts();
    
                    follower.followPath(scorePickup1,true);
                    setPathState(3); // Move to the fourth path state
                }
                break;
            case 3:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Shoot first round of collected artifacts
                    shootArtifacts();
                    
                    follower.followPath(grabPickup2,true);
                    setPathState(4);
                }
                break;
            case 4:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Intake second set of artifacts
                    intakeArtifacts();
                    
                    follower.followPath(scorePickup2,true);
                    setPathState(5);
                }
                break;
            case 5:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Shoot second round of collected artifacts
                    shootArtifacts();
                    
                    follower.followPath(grabPickup3,true);
                    setPathState(6);
                }
                break;
            case 6:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    // Intake third set of artifacts
                    intakeArtifacts();
                    
                    follower.followPath(scorePickup3, true);
                    setPathState(7);
                }
                break;
            case 7:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                     // Shoot third round of collected artifacts
                    shootArtifacts();
                
                    follower.followPath(returnHome, true);
                    setPathState(8); // Set the path state to 8
                }
                break;
            case 8:
                // Wait until we have passed all path constraints
                if (!follower.isBusy()) {
                    setPathState(-1); // Set the path state to -1 to stop execution
                }
                break;
        }
    }
    
    // Chnages path state and resets path timer
    public void setPathState(int pathState) {
        pathState = pathState;
        pathTimer.resetTimer();
    }
}
```

TeleOp
```java
// TeleOp coming soon
```