# Getting started

This comes **after** the [Java Bootcamp](../java/index.md), not before it. The lab
computers are already installed and ready, so if you are working in the lab there
is nothing here you have to do. Follow these steps when you want your own machine
able to build and deploy too.

!!! info "We are on WPILib 2026.2.1"
    2027 is in alpha and still moving. Install **2026.2.1** &mdash; not the newest
    thing on the download page. Everything we do this fall targets the **roboRIO**,
    which is hardware we already have and can put on a desk.

## 1. Install WPILib 2026.2.1

The WPILib installer brings its own copy of VS Code and its own JDK. Do not try to
reuse a VS Code you already have &mdash; let the installer put down its own.

- Download the **2026.2.1** release from the WPILib GitHub releases page.
- Run the installer and choose **Everything** when asked what to install.
- Choose **Download for this computer only** unless you are setting up a lab cart.
- When it finishes you will have a **2026 WPILib VS Code** shortcut. That is the
  one you open. The plain VS Code shortcut will not have the WPILib commands.

## 2. Make sure gradle can actually build

Open the 2026 WPILib VS Code, open a robot project, and build it once **while on
wifi**. The first build downloads the whole dependency tree, and it is slow. If
you skip this and first try to build at a meeting on a laptop that has never
built before, you will spend the meeting watching a progress bar.

```
./gradlew build
```

A green `BUILD SUCCESSFUL` is the goal. If you get a Java version error, you are
almost certainly not using the WPILib VS Code.

## 3. Deploy

With the roboRIO powered and connected:

```
./gradlew deploy
```

Then open **Driver Station** and **Shuffleboard** or the dashboard of your choice
to confirm the robot code is running and reporting.

## Team number

Team **829**. It lives in `.wpilib/wpilib_preferences.json`:

```json
{
    "currentLanguage": "java",
    "projectYear": "2026",
    "teamNumber": 829
}
```

If your deploy cannot find the robot, check this file first.

## The 2027 transition

**In December or after kickoff**, once 2027 WPILib settles into its final shape,
we move over. This is not a version bump &mdash; the 2027 control system replaces the
roboRIO with **SystemCore**, and the gradle plugin, the deploy target, and parts
of the API all change with it. We will do that transition together, as a meeting,
rather than everyone doing it alone.

Until then: **2026.2.1**.
