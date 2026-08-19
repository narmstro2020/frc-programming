# DigiLib

**DigiLib** is Team 829's own library &mdash; the layer between raw WPILib and the
robot code we write each season. The point is that a season should not start by
rewriting a swerve drive.

!!! info "There are two DigiLibs right now"
    A **2026** line that runs on the roboRIO, and a **2027** line being built
    against the new control system. Everything in this section is the **2026**
    line, because that is what we use this fall. The 2027 move happens in December.

## The one pattern to learn first

Almost everything in our robot code follows the same three-file shape per
mechanism. Learn this and the rest of the codebase stops being mysterious.

| File | Job |
| :--- | :--- |
| `XConstants.java` | Build the hardware. Construct the DigiLib subsystem. Set its default command. |
| `XCommands.java` | Compose `Command`s from what the subsystem exposes. |
| `XState.java` | Expose `Trigger`s for "is it there yet?" conditions. |

The subsystem itself is a `public static final` field on the Constants class, so
the rest of the code refers to one shared instance. Configuration is done once, in
a static block, at class load.

```java
public final class FeederConstants {

    private static final String NAME = "Feeder";
    private static final TalonFXConfiguration TALON_FX_CONFIGURATION =
        new TalonFXConfiguration()
            .withMotorOutput(new MotorOutputConfigs()
                .withNeutralMode(NeutralModeValue.Coast)
                .withInverted(InvertedValue.CounterClockwise_Positive))
            .withCurrentLimits(new CurrentLimitsConfigs()
                .withStatorCurrentLimit(Amps.of(80))
                .withStatorCurrentLimitEnable(true));

    private static final TalonFX LEADER_MOTOR = new TalonFX(15, RIO);

    static {
        LEADER_MOTOR.getConfigurator().apply(TALON_FX_CONFIGURATION);
    }

    public static final CommonTalonSubsystem subsystem =
        new CommonTalonSubsystem(NAME, LEADER_MOTOR);

    static {
        subsystem.setDefaultCommand(
            subsystem.applyControl(new NeutralOut()).withName("Feeder-Neutral"));
    }

    private FeederConstants() {}
}
```

Three things worth noticing, because they are deliberate:

- **Everything is a constant.** Private constructor, `static final` fields. There
  is exactly one Feeder and nothing can make a second one.
- **The default command is set immediately.** A subsystem with no default command
  is a subsystem that keeps doing whatever it was last told to do. Here it goes to
  `NeutralOut` the moment nothing else wants it.
- **Commands get names.** `.withName("Feeder-Neutral")` is what you will see on the
  dashboard when you are trying to work out why the robot is doing something.

## What is in the 2026 line

23 classes. Swerve and its vision stack are most of it.

| Package | Pages |
| :--- | :--- |
| `digilib.subsystem`, `digilib.commontalon`, `digilib.cylinder`, `digilib.pneumatics`, `digilib.power` | [Subsystems](subsystems.md) |
| `digilib.swerve` and `digilib.swerve.sim` | [Swerve](swerve.md) |
| `digilib.swerve.vision` and below | [Vision](vision.md) |

## What is coming in 2027

The 2027 line is a rethink rather than a port. It separates *what a mechanism is*
from *what hardware it runs on*: a `MotorIO` interface with `TalonFXIO` and
`SparkIO` behind it, the same for swerve, plus `plants` and `sims` packages for
physics models.

That means the same mechanism code runs on a CTRE robot, a REV robot, or in
simulation without changing the mechanism. The 2026 line already leans that way
&mdash; notice how everything Limelight-specific sits at the edge of the vision
package &mdash; and 2027 takes it the whole distance.

## Getting the code

Both DigiLib lines live in the team's private repositories. Ask to be added.
Nothing here links to them directly, because the links would 404 for anyone not
on the team.
