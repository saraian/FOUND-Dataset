# Exploration schedules

One trajectory per scene: the waypoints a robot drives to explore it, and the order to drive them.
A schedule is the ONLY motion policy the FOUND runner has (owner ruling, 2026-09-11), so a scene
without one cannot be run.

## What a schedule is

Waypoints on the **generalized Voronoi diagram** of the scene's navmesh — the set of points
equidistant from two or more obstacles, which is the line running down the middle of every corridor
and doorway. The waypoints are visited **depth-first** from the busiest junction, and the robot
turns a **full circle** at each one. One lap is the file; a run repeats the same lap
`exploration_laps` times, so a difference between two laps is a difference in the world and not in
the route.

One file holds every **storey** of its scene. Storeys are found from a height histogram of
navigable samples, and a storey is skipped when it is a stair landing or a gallery rather than a
floor to tour.

## Two naming conventions in one directory

| Name | Which scenes | Where the navmesh comes from |
|---|---|---|
| `00800-TEEsavR23oF.schedule.json` … | the 100 HM3D val scenes this repository ships | `habitat/hm3d-val-habitat-v0.2/` |
| `hm3d_00861`, `hm3d_00337`, `hm3d_00770`, `mp3d_17DRP` | the four scenes the test harness launches by name | outside this repository |

`hm3d_00861.schedule.json` and `00861-GLAQ4DNUx5U.schedule.json` are the **same scene**: the two
navmeshes are byte-identical and the two trajectories are identical too. Both files exist because
the harness looks a schedule up by the name it was given on the command line. `hm3d_00337`,
`hm3d_00770` and `mp3d_17DRP` are NOT in the val split and have no dataset-id twin.

## index.json

Covers the 100 dataset scenes only: for each one, its storey count, how many storeys got a
schedule, the number of stops and the total frame budget. The four harness scenes are not in it.

## Regenerating

```bash
python3 lost3dsg/test/schedule_batch.py \
    --scene-root habitat/hm3d-val-habitat-v0.2 --out-dir schedules
```

from a checkout of GRAPH-API, with the conda environment that has `habitat_sim`. About 3 seconds a
scene. The generator is deterministic: the same navmesh and the same settings give byte-identical
trajectories, and only `scene_id` and `navmesh` change with the path you pass.

**`settings_sha256_16` does NOT cover the generator code.** It hashes the command-line settings
only, so a schedule built before a change to `voronoi_roadmap.py` carries the same digest as one
built after it and `--ensure` will reuse the old file. Pass `--regenerate` after any change to the
roadmap code.

## Room coverage

Every storey here reaches 100% of its ground-truth rooms except one: storey `+0.00` of
`hm3d_00337`, at 77%. That scene also has two levels its storey detector did not tour.
