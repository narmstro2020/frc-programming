# Subsystems

Every DigiLib subsystem extends **`GoatSubsystem`**, which is deliberately tiny:

```java
public abstract class GoatSubsystem implements Subsystem, AutoCloseable {
    protected final String name;

    public GoatSubsystem(String name) { this.name = name; }

    @Override public String getName() { return name; }

    protected Command currentCommand() {
        Command cmd = getCurrentCommand();
        return cmd != null ? cmd : Commands.none();
    }
}
```

Two things it adds over a plain WPILib `Subsystem`:

- **A name you chose**, not a class name. It shows up in telemetry and on the
  dashboard, so `"Feeder"` beats `CommonTalonSubsystem@4a2f`.
- **`AutoCloseable`**, so hardware gets released properly &mdash; which matters in
  simulation and tests, where subsystems get built and torn down repeatedly.

`currentCommand()` returns `Commands.none()` instead of `null` when nothing is
running. Small thing, saves a whole category of null checks.

## CommonTalonSubsystem

The workhorse. Wraps a leader TalonFX, optional followers, and an optional CANcoder.

```java
new CommonTalonSubsystem(name, leaderMotor)
new CommonTalonSubsystem(name, leaderMotor, cancoder)
new CommonTalonSubsystem(name, leaderMotor, followers)
new CommonTalonSubsystem(name, leaderMotor, followers, cancoder)
```

**Reading the mechanism** &mdash; all of these return WPILib unit types, not bare
doubles, so the compiler catches unit mistakes:

| Method | Returns |
| :--- | :--- |
| `voltage()` | `Voltage` |
| `angle()` | `Angle` |
| `velocity()` | `AngularVelocity` |
| `acceleration()` | `AngularAcceleration` |

**Driving it:**

```java
Command applyControl(ControlRequest request)
Command applyControl(Supplier<ControlRequest> requestSupplier)
```

The request is a Phoenix 6 `ControlRequest` &mdash; `NeutralOut`, `VelocityVoltage`,
`MotionMagicVoltage`, and so on. The `Supplier` overload is for when the target
changes while the command runs; the plain one is for a fixed target.

**Asking whether it got there:**

```java
Trigger atVelocity(AngularVelocity targetVelocity, AngularVelocity tolerance)
```

This returns a `Trigger`, not a `boolean`. That is the point &mdash; you compose it:

```java
Trigger leftReady  = compressorShooterReady(velocity, tolerance);
Trigger rightReady = batteryShooterReady(velocity, tolerance);
Trigger anyReady   = leftReady.or(rightReady);

return parallel(
        runShooters(velocity),
        runIntake().asProxy(),
        repeatingSequence(
                waitUntil(anyReady),
                runSingulators(leftReady, rightReady)
                        .until(anyReady.negate().debounce(0.15))))
        .withName(name);
```

Note `.debounce(0.15)` on the negation &mdash; the shooter dipping below tolerance
for a few milliseconds should not stop the feed. Debounce is how you say that.

## DoubleSolenoidSubsystem

A pneumatic cylinder.

```java
new DoubleSolenoidSubsystem(name, doubleSolenoid)

Command applyValue(Value value)   // kForward, kReverse, kOff
Trigger isAt(Value value)
Value  Value()
```

Same shape as the Talon subsystem: a command to change it, a trigger to ask about it.

## PneumaticHubSubsystem and PowerSubsystem

Thin wrappers over the REV Pneumatic Hub and the Power Distribution board.

```java
new PneumaticHubSubsystem(name, pneumaticHub)
new PowerSubsystem(name, powerDistribution)

Command clearStickyFaults()
```

Both mostly exist so the hardware is a real subsystem with telemetry and a default
command rather than a loose object nobody owns:

```java
public static final PowerSubsystem subsystem =
    new PowerSubsystem(NAME, POWER_DISTRIBUTION);

static {
    subsystem.setDefaultCommand(subsystem.idle().withName("Power-Idle"));
}
```

Sticky faults are worth knowing about: the PDH latches a fault (brownout, a
tripped channel) and keeps reporting it until cleared. `clearStickyFaults()` bound
to a button saves you power-cycling the robot to find out whether a fault is
current or historical.
