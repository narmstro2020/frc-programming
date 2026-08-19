# DigiLib

**DigiLib** is Team 829's own library &mdash; the layer between raw WPILib and the
robot code we write each season. The point is that a season should not start by
rewriting a swerve drive.

!!! info "There are two DigiLibs right now"
    A **2026** line that runs on the roboRIO, and a **2027** line being built
    against the new control system. We work in 2026 this fall, and in December we
    start moving to 2027.

## The 2026 line

This is what we use now, and what runs on the hardware we can put on a desk. It
is also what we will keep using for side projects on older roboRIOs even after
the team moves on.

Roughly 23 classes, organised as:

| Package | What lives there |
| :--- | :--- |
| `digilib.subsystem` | The base a DigiLib subsystem extends |
| `digilib.swerve` | The drivetrain &mdash; the largest piece by far |
| `digilib.swerve.sim` | Simulation for it |
| `digilib.swerve.vision` | Pose estimation from vision |
| `digilib.swerve.vision.limelight` | Limelight-specific implementation |
| `digilib.commontalon` | Shared Talon motor handling |
| `digilib.cylinder`, `digilib.pneumatics` | Solenoids and the pneumatic hub |
| `digilib.power` | Power distribution |

The shape to notice: **swerve and its vision stack are most of the library**, and
everything vision is split so the Limelight-specific parts sit at the edge. That
split is deliberate, and it is the same idea the 2027 line takes much further.

## The 2027 line

Being built now against WPILib 2027 and SystemCore. It is a bigger rethink than a
port &mdash; the design separates *what a mechanism is* from *what hardware it runs
on*:

| Package | What lives there |
| :--- | :--- |
| `digilib.mechanism.motor` | The motor mechanism itself, hardware-agnostic |
| `digilib.mechanism.motor.ctre` | CTRE / TalonFX implementation |
| `digilib.mechanism.motor.rev` | REV / Spark implementation |
| `digilib.mechanism.swerve` | Swerve, hardware-agnostic |
| `digilib.mechanism.swerve.ctre` | CTRE swerve implementation |
| `digilib.mechanism.swerve.generic` | A vendor-neutral implementation |
| `digilib.plants`, `digilib.sims` | Physics models and simulation |

The recurring pattern is an **IO interface plus a state object plus a per-vendor
implementation** &mdash; `MotorIO` with `TalonFXIO` and `SparkIO` behind it. That is
what makes the same mechanism code run on a CTRE robot, a REV robot, or in
simulation without changing the mechanism.

If you want to understand DigiLib, understand that pattern first. Everything else
is detail.

## Getting the code

Both DigiLib lines live in the team's private repositories. Ask for access &mdash;
you will be added. Nothing here links directly to them, because the links would
just 404 for anyone not on the team.

!!! note "This section grows with the library"
    DigiLib is under active development. As pieces stabilise, the how-to-use-it
    pages land here.
