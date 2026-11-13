# Student Autos — FRC Team 8352 CODE Gold

This is the **practice repo** for building and testing autonomous routines ("autos").
It's a copy of our drivebase code ([SDS-Kraken-Base-Drive](https://github.com/CodeGold8352/SDS-Kraken-Base-Drive)),
so anything you build here runs on the real robot. Nothing you do here changes the team's real code.
When an auto is good, a mentor moves it over.

**The rules**
- Work on your own branch, never on `main` or `autos` (GitHub won't let you push there anyway).
- Every change goes in through a pull request. A mentor (Michael, Carrie, Govind or Alex) has to approve it.
- Test in simulation first. Only run on the real robot with a mentor there.
- Don't change the robot settings: `generated/TunerConstants.java`, PID gains, or the **Robot Config** page in PathPlanner.
  They were measured on the real robot. If something seems wrong, ask a mentor.

---

## 1. Install the tools (one time)

| Tool | What it's for | Where to get it |
|---|---|---|
| **WPILib 2026** | VS Code + Java + the robot simulator | [WPILib installer](https://github.com/wpilibsuite/allwpilib/releases), pick the 2026 release and choose "Everything" |
| **PathPlanner** (2026 version) | Draw paths and build autos | Microsoft Store (search "PathPlanner") or [GitHub releases](https://github.com/mjansen4857/pathplanner/releases) |
| **Git** | Download code and send your changes | [git-scm.com](https://git-scm.com/downloads) |
| **GitHub account** | Ask a mentor to add you to the `students` team | [github.com](https://github.com) |

Use the **2026 WPILib VS Code** (it's on your desktop as "2026 WPILib VS Code"), not regular VS Code.

## 2. Get the code

Open a terminal (in WPILib VS Code: **Terminal → New Terminal**) and run:

```
git clone https://github.com/CodeGold8352/student-autos.git
cd student-autos
```

Then **File → Open Folder** and open the `student-autos` folder.

Check it builds (the first build downloads a lot and can take a few minutes):

```
./gradlew build
```

You should see `BUILD SUCCESSFUL`.

## 3. Make your own branch

Always start from the latest `autos` branch. Name your branch `autos-<your name>-<what it does>`:

```
git switch autos
git pull
git switch -c autos-jordan-s-curve
```

Use a dash, not a slash (`autos-jordan-s-curve`, not `autos/jordan-s-curve`).

## 4. Open the project in PathPlanner

1. Open PathPlanner.
2. Click the menu (☰) → **Open Project** and pick the `student-autos` folder (the top folder, the one with `build.gradle` in it).
3. You should see the **Square Test** paths and autos. Those are the mentors' tuning autos — look, but don't change them.

PathPlanner saves straight into `src/main/deploy/pathplanner/`. There is no "export" step.
Every change you make in the app is a file change Git will see.

### How the field works
- The origin (0, 0) is the **blue alliance** corner. **X** goes away from the blue wall, **Y** goes to the left.
- **Always draw paths for the blue side.** The code flips them automatically when we're on red.
- Rotation 0° means the robot faces away from the blue wall (toward the red side).

## 5. Make a path

A **path** is one smooth drive from point A to point B.

1. Go to the **Paths** tab. Make a folder with your name (right-click → **New Folder**) so your work stays together.
2. Click **+** to add a path. Give it a clear name like `Jordan - Start to Middle`.
3. Drag the start point (green) and end point (red) to where you want them. Click on the path to add waypoints in between.
4. Set the speeds under **Global Constraints**. **Start slow: 1.5 m/s max velocity, 1.5 m/s² max acceleration** — that's what the Square Test uses.
   Speed up only after it works slowly.
5. Set **Goal End State → Rotation**: which way the robot faces when it finishes.
6. Optional extras:
   - **Rotation Targets**: make the robot face a certain way partway along the path.
   - **Constraint Zones**: slow down on just part of the path (good for tight turns).
   - **Event Markers**: run a command partway along the path (see section 7).

Tip: the robot is a swerve drive, so it can turn while it drives. It doesn't have to point the way it's moving.

## 6. Make an auto

An **auto** is the whole 15-second routine: paths and commands put together in order.

1. Go to the **Autos** tab. Make a folder with your name.
2. Click **+** to add an auto. Name it like `Jordan - S Curve`.
3. Drag commands into the list:
   - **Follow Path**: drive one of your paths.
   - **Wait**: pause for some seconds.
   - **Named Command**: run robot code (see section 7).
   - **Sequential Group**: do things one after another. **Parallel Group**: do things at the same time.
4. Leave **Reset Odometry** checked. At the start of the auto it tells the robot "you are at the start of the first path."
   That's why the robot must really be placed at that spot.

**You don't need to write any Java to add an auto.** The code reads every auto in the folder and puts it in the
**Auto Mode** list on the dashboard automatically.

## 7. Named commands (making the auto do things besides drive)

Named commands connect a name in PathPlanner to a piece of robot code. These are the ones that exist now:

| Name | What it does |
|---|---|
| `Vision On` | Turn on Limelight AprilTag corrections to the robot's position |
| `Vision Off` | Turn them off (the robot uses only wheel odometry) |

The name in PathPlanner must match **exactly**, including capitals and spaces.

Right now the robot is only a drivebase, so these are the only ones. When we add mechanisms (intake, shooter, etc.),
a new named command gets registered in `RobotContainer.java`:

```java
// Must be registered BEFORE AutoBuilder.buildAutoChooser(...) runs, or PathPlanner won't find it
NamedCommands.registerCommand("Vision On", vision.setEnabledCommand(true));
```

If you want to add one, talk to a mentor first and explain what it's for.

## 8. Test it in simulation

Always do this before the real robot.

1. In WPILib VS Code press **Ctrl+Shift+P** → **WPILib: Simulate Robot Code** → pick **Sim GUI**.
2. In the Sim GUI:
   - **NetworkTables → SmartDashboard → Auto Mode**: pick your auto.
   - **NetworkTables → Pose**: opens a field picture that shows where the robot thinks it is.
   - In **Robot State**, click **Autonomous**.
3. Watch the robot drive your auto on the field picture. Click **Disabled** to stop.
4. Did it end where you expected? Facing the right way? Does it finish in well under 15 seconds?

You can also watch it live in PathPlanner: menu → **Telemetry** (set the host to `127.0.0.1` in Settings for the simulator).

If the auto isn't in the list, check that PathPlanner saved it and that you restarted the simulator.

## 9. Test it on the robot (with a mentor)

1. **Clear the area** around the path. Have someone ready on the disable button (Enter = disable, Space = emergency stop).
2. Deploy: **Ctrl+Shift+P → WPILib: Deploy Robot Code**.
3. Place the robot **exactly** at your auto's starting point, facing the right way.
4. Pick your auto in **Auto Mode** on the dashboard, enable Autonomous, and watch.
5. Measure how far off the robot ends up. Run it a few times — one good run isn't proof.

## 10. Save your work and open a pull request

```
git add src/main/deploy/pathplanner
git commit -m "Add S-curve auto"
git push -u origin autos-jordan-s-curve
```

Then on GitHub, open a pull request **into `autos`** (not `main`). In the description, write:
- What the auto does and where it starts.
- Whether you tested it in sim, on the robot, or both.
- How far off it ended up, if you measured.

Before pushing, run `git status` and check you only changed **your** paths and autos.
`settings.json` changes when you make a new folder — that's fine. Changes to the robot size, mass or other
Robot Config values are not.

---

## Common problems

| Problem | Likely cause |
|---|---|
| Auto isn't in the Auto Mode list | Not saved in PathPlanner, or the code wasn't rebuilt/redeployed |
| "Named command not found" warning | The name in PathPlanner doesn't match exactly (check capitals and spaces) |
| Robot drives to the wrong place | It wasn't placed at the start point, or Reset Odometry is off |
| Robot goes the wrong way on red | Path drawn for red. Redraw it for blue — the code flips it |
| Robot shakes or overshoots | Path is too fast. Lower max velocity/acceleration |
| `BUILD FAILED` | Read the first error line. Usually a typo in Java. Ask for help |

## Learn more
- [PathPlanner docs](https://pathplanner.dev/home.html)
- [WPILib docs](https://docs.wpilib.org/)
- `CLAUDE.md` in this repo has notes on how the robot code is set up.
