# Swerve

The drivetrain is the largest single piece of DigiLib, and the one you are most
likely to touch.

## SwerveDriveSubsystem

Wraps a CTRE `SwerveDrivetrain<TalonFX, TalonFX, CANcoder>` and adds pose
estimation, vision fusion, telemetry, and Choreo autonomous support.

```java
Command applyRequest(SwerveRequest request)
Command applyRequest(Supplier<SwerveRequest> request)
```

Same idea as `CommonTalonSubsystem.applyControl` &mdash; the `Supplier` version is
what driver control uses, because the joystick values change every loop:

```java
drivetrain.applyRequest(() -> drive
        .withVelocityX(-driver.getLeftY() * MAX_SPEED)
        .withVelocityY(-driver.getLeftX() * MAX_SPEED)
        .withRotationalRate(-driver.getRightX() * MAX_ANGULAR_RATE));
```

**Pose management:**

| Method | What it does |
| :--- | :--- |
| `seedFieldCentric()` | Make "forward" mean the direction the robot currently faces |
| `resetPoseDirect(Pose2d)` | Set the pose to a known value |
| `resetPoseFromVision()` | Set the pose from what the cameras see |
| `tare()` | Zero the drivetrain |
| `resetForAuto(AutoTrajectory)` | Place the robot at the start of a trajectory |
| `getState()` | The current `SwerveDriveState` |

`setStateStdDevs(Matrix<N3,N1>)` tunes how much the estimator trusts the wheel
odometry relative to vision. Turning that dial is how you fix a pose that either
jitters (trusts vision too much) or drifts (trusts it too little).

## Drive requests

Beyond the stock CTRE requests, DigiLib adds two of its own, both extending
`GoatFacingAngleBase`:

| Request | Heading behaviour |
| :--- | :--- |
| `FieldClockDrive` | Point at an angle in **field** coordinates |
| `RobotClockDrive` | Point at an angle relative to the **robot** |

Both are builders, so they read as a chain:

```java
new FieldClockDrive()
        .withVelocityX(vx)
        .withVelocityY(vy)
        .withTargetDirection(Rotation2d.fromDegrees(60))
        .withHeadingPID(kP, kI, kD)
        .withDeadband(0.1)
        .withRotationalDeadband(0.1)
        .withTargetRateFeedforward(omega);
```

The velocity and deadband setters are overloaded for both bare doubles and unit
types (`LinearVelocity`, `AngularVelocity`). **Prefer the unit versions.** A
`double` that turns out to be in the wrong unit is one of the more expensive bugs
you can write, and the type system will catch it for free.

`withTargetRateFeedforward` matters when the thing you are aiming at is moving:
feedforward gets you most of the way there, and the heading PID only has to clean
up the remainder.

## SwervePathController

Follows a Choreo trajectory. Three independent PID controllers &mdash; x, y, theta:

```java
var controller = new SwervePathController(
        new PIDConstants(kPx, kIx, kDx),
        new PIDConstants(kPy, kIy, kDy),
        new PIDConstants(kPt, kIt, kDt));

ChassisSpeeds speeds = controller.calculate(currentPose, sample);
```

It exposes feedback, feedforward, setpoint, and error for each axis separately
(`getXFeedback()`, `getXFeedforward()`, `getXSetpoint()`, `getXError()`, and so on).

That is not clutter &mdash; it is how you tune a path. When a path is off, you need
to know *which* axis, and whether the miss is feedforward (the path is asking for
something the robot cannot do) or feedback (the gains are wrong). Guessing at that
from a single error number does not work.

## Simulation

`SimSwerveDrivetrain` models the drivetrain so you can run without hardware, with
`DCMotorSim` for drive and steer plus the properties that actually matter:

- `driveGearing`, `steerGearing`
- `driveFrictionVoltage`, `steerFrictionVoltage`
- inversions for drive, steer, and encoder
- `encoderOffset` and `encoderType`

```
./gradlew simulateJava
```

Friction voltage is the one people leave at zero and then wonder why the sim robot
accelerates like it is on ice. It is the voltage needed before the mechanism moves
at all.

## Telemetry

`SwerveDriveTelemetry` publishes drivetrain state; `SwervePathTelemetry` publishes
the active trajectory and the controller's feedback:

```java
telemetry.setActiveTrajectory(poses);
telemetry.publishFeedback(controller);
telemetry.clearTrajectory();
```

Push the trajectory to the dashboard and you can watch the robot's actual path
against the planned one. That comparison finds path problems far faster than
reading numbers.
