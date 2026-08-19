# Reference

Things you will look up more than once.

## Official docs

| Link | What it is |
| :--- | :--- |
| [WPILib docs](https://docs.wpilib.org/) | The manual. Start here for anything WPILib. |
| [WPILib API (Java)](https://github.wpilib.org/allwpilib/docs/release/java/) | Javadoc for every WPILib class. |
| [WPILib releases](https://github.com/wpilibsuite/allwpilib/releases) | Where the installer comes from. We are on **2026.2.1**. |
| [FIRST Robotics Competition](https://www.firstinspires.org/robotics/frc) | Game manual, rules, events. |
| [Chief Delphi](https://www.chiefdelphi.com/) | Where FRC problems get solved in public. |

## Vendor libraries we use

| Vendor | Library | Used for |
| :--- | :--- | :--- |
| CTR Electronics | [Phoenix 6](https://v6.docs.ctr-electronics.com/) | TalonFX motors, CANcoder, Pigeon |
| REV Robotics | [REVLib](https://docs.revrobotics.com/revlib) | Spark Max / Spark Flex |
| Limelight | [Limelight docs](https://docs.limelightvision.io/) | Vision and pose estimation |
| Choreo | [ChoreoLib](https://choreo.autos/) | Autonomous path planning |
| AdvantageKit | [AdvantageKit docs](https://docs.advantagekit.org/) | Logging and replay |

## Java

| Link | What it is |
| :--- | :--- |
| [Perscript](https://snakestongue.github.io/Perscript/) | Practice drills. Where you start. |
| [Java tutorials (Oracle)](https://docs.oracle.com/javase/tutorial/) | The reference when you need the exact rule. |

## Commands you will type constantly

```bash
./gradlew build      # compile, and download deps the first time
./gradlew deploy     # push code to the robot
./gradlew simulateJava   # run it without a robot
./gradlew clean      # when you suspect the build is lying to you
```

## Quick answers

**"My deploy can't find the robot."** Check the team number in
`.wpilib/wpilib_preferences.json` &mdash; it should be `829`. Then check you are on
the robot's network, not the school wifi.

**"It won't build and the error mentions a Java version."** You opened the wrong
VS Code. Use the **2026 WPILib VS Code** shortcut.

**"It built yesterday and not today."** `./gradlew clean` then build again, on wifi.

**"Which WPILib do I install?"** 2026.2.1. Not the newest one on the page.
The lab computers are already set up; ask before installing on your own machine.
