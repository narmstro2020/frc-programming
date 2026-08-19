# DigiLib

**DigiLib** is Team 829's own library &mdash; the layer between raw WPILib and the
robot code we write each season. The point is that a season should not start by
rewriting a swerve drive.

!!! warning "This section is being rebuilt"
    DigiLib 2026 is being reworked for how we want to use it this fall. Nothing on
    these pages describes last season's code, and nothing here is final until the
    rework lands. Pages fill in as each piece settles.

## What is here

| Page | Covers |
| :--- | :--- |
| [MotorSubsystems](motor-subsystems.md) | Motor-backed mechanisms &mdash; the first thing to learn |

## After that

Swerve, vision, and the rest come once MotorSubsystems is solid. They are the
larger pieces, and they read as variations on the same idea, so there is no point
starting there.

## The 2027 move

The 2027 line is a rethink rather than a port: it separates *what a mechanism is*
from *what hardware it runs on*, so the same mechanism code runs on CTRE hardware,
REV hardware, or in simulation. We take that on **in December or after kickoff**,
not before.

## Getting the code

DigiLib lives in the team's private repositories. Ask to be added. Nothing here
links to it directly, because the links would 404 for anyone not on the team.
