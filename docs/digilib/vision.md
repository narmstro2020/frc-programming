# Vision

Vision tells the robot where it is on the field. DigiLib's vision stack is built
around the **Limelight 4** and MegaTag2, and it is carefully split so the
Limelight-specific parts sit at the edge.

That split is the design decision worth understanding: `digilib.swerve.vision`
holds the general idea of a camera, and `digilib.swerve.vision.limelight` holds
everything that knows what a Limelight is. Swap cameras and only the edge changes.

## Setting up a camera

`CameraConfig` is a record that describes where a camera is bolted to the robot:

```java
record CameraConfig(String name, Pose3d robotToCameraPoseWPIFrame)

Limelight4Camera camera = config.build();
```

The `Pose3d` is the camera's position **and orientation** relative to robot
center. Measure it properly. A camera pose that is a couple of degrees off in
pitch produces a pose estimate that is wrong by more the further away the tag is,
and it will look like a software bug for a long time before anyone re-measures the
mount.

## Limelight4Camera

```java
PoseEstimate getBotPoseEstimate_wpiBlue()
PoseEstimate getBotPoseEstimate_wpiBlue_MegaTag2()
double[]     getStandardDeviationsRaw()
Pose3d       getCameraPoseRobotWPIFrame()
```

Both estimates are in the **WPI blue** coordinate frame &mdash; origin at the blue
alliance wall, always, regardless of which alliance you are on. Alliance flipping
happens above this layer, not here.

**MegaTag1 vs MegaTag2:** MT1 works from tag geometry alone. MT2 also uses the
robot's gyro heading, which makes it far more stable, especially with a single tag
in view. MT2 is what you want in a match &mdash; but it is only as good as the
heading you feed it:

```java
void setRobotOrientation_NoFlush(double yaw, double yawRate,
                                 double pitch, double pitchRate,
                                 double roll, double rollRate)
static void flush()
```

The `_NoFlush` naming is deliberate: set orientation on every camera, then call
`flush()` **once**. Flushing per camera means a network round trip per camera per
loop, which you will feel.

`setIMUMode(IMUMode)` controls whether the Limelight uses its internal IMU or the
one you give it. `setThrottle(int skipFrames)` makes the camera skip frames &mdash;
which is what `thermalThrottleCameras(boolean)` on the drivetrain uses, because a
Limelight 4 that overheats starts dropping frames on its own terms rather than
yours.

## PoseEstimate and standard deviations

```java
record PoseEstimate(double x, double y, double z,
                    double rollRad, double pitchRad, double yawRad,
                    double timestampSeconds, ...)

Pose2d toPose2d()
enum Validity
```

Note the **timestamp**. A vision measurement is about where the robot was when the
picture was taken, not where it is now. Feeding it to the estimator without the
timestamp puts the robot in the wrong place at speed.

`MT2StandardDeviations` says how much to trust a measurement:

```java
record MT2StandardDeviations(double x, double y, double z,
                             double roll, double pitch, double yaw)

static MT2StandardDeviations fromArray(double[] stddevs)
boolean isValid()
```

Always check `isValid()` before using a measurement. Bigger standard deviation
means less trust; the estimator weights the measurement accordingly. This is the
mechanism behind "the robot teleports across the field once per match" &mdash; a bad
measurement accepted at full confidence.

## Simulation

`Limelight4SimState` simulates a Limelight against an `AprilTagFieldLayout`,
including the things that make real vision hard:

```java
new Limelight4SimState(name, cameraPoseRobotSpace, fieldLayout)
        .withHorizontalFOV(degrees)
        .withVerticalFOV(degrees)
        .withMaxDetectionDistance(meters)
        .withMinDetectionDistance(meters)
        .withPipelineLatency(ms)
        .withCaptureLatency(ms)
        .withPositionNoise(meters)
        .withRotationNoise(degrees)
        .withRebuiltObstacles();

simState.update(robotPose);
```

Two of these deserve attention:

- **Latency.** Real vision is late. Simulating with zero latency produces code that
  works in sim and oscillates on the field.
- **Obstacles.** `Obstacle` is a 3D box with `intersectsLine(...)`, so the sim can
  work out that a tag is *behind something* and should not be visible. Vision code
  that never has to handle a tag disappearing is not tested vision code.

## Telemetry

`CameraTelemetry` logs per camera; `VisionTelemetry` logs the overall picture:

```java
cameraTelemetry.updateMT2(mt2, stdDevX, stdDevY);
cameraTelemetry.updateMT1(mt1);
cameraTelemetry.logRejection(reason, timestampMicros);

visionTelemetry.publishOverview(fusionInitialized, contributingCameras);
```

`logRejection` is the one to reach for when debugging. When the pose is wrong, the
useful question is usually not "what did the camera see" but "what did we throw
away, and why" &mdash; and that is only answerable if rejections are logged.
