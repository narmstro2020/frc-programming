# MotorSubsystems

Most mechanisms on the robot are a motor turning something &mdash; a shooter wheel,
an intake roller, an arm, an elevator stage. **MotorSubsystems** is the part of
DigiLib that covers all of them, and it is the first piece to learn. Everything
else in the library is a variation on this shape.

!!! note "Being written as the rework lands"
    DigiLib 2026 is being modified for our purposes right now. This page gets
    filled in from the actual code as it settles, rather than from last season's
    version, so that every signature here matches what is on disk.

## What this page will cover

- **Building one** &mdash; constructing the hardware and the subsystem, and where
  that construction lives
- **Configuration** &mdash; current limits, neutral mode, inversion, and the
  settings you get wrong once and never again
- **Reading the mechanism** &mdash; position, velocity, and why these come back as
  unit types rather than bare `double`s
- **Driving it** &mdash; the command that applies a control request, and the
  difference between a fixed target and one that changes every loop
- **Default commands** &mdash; what a mechanism does when nothing else wants it
- **Asking whether it got there** &mdash; conditions as `Trigger`s, so they compose
- **Simulation** &mdash; running the mechanism without hardware on the desk

Until then, the [Java Bootcamp](../java/index.md) is the thing to be working on.
