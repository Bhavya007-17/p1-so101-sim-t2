# Bhavya007-17/p1-so101-sim-t2

80 simulated SO-101 demonstration episode(s), recorded in MuJoCo by a scripted privileged-state expert.

## Read this before you train on it

**These demonstrations were produced by a scripted expert, not human teleoperation.** A four-phase differential-IK state machine drove every episode. They are cleaner, more consistent and less multimodal than human demonstrations, and a policy trained on them will look better in simulation than the same policy trained on human data will look on hardware. The benchmark protocol states this up front as a declared limitation (§8) rather than discovering it later.

**Grasping is a MuJoCo equality weld constraint — there is no friction, no slip and no real contact.** The gripper does not hold the cube; the constraint is switched on at a phase boundary and off at release. Grasp success is imposed, not simulated, so nothing in this dataset carries information about contact dynamics. The one exception is T4, a push task with no grasp at all, where the cube moves only because a gripper geom touches it and the contact solver resolves the push.

**The expert reads ground-truth object pose straight out of MuJoCo.** It is a privileged-state controller. The demonstrations it produced contain two RGB streams and joint state, so a policy trained on them is strictly information-poorer than the expert that generated them — which is the comparison the benchmark exists to make, and the reason the expert is reported as a ceiling rather than as an opponent.

**T3, C3 and C4 are deferred.** They are in the protocol and they are not in this dataset:

- T3 — T3 needs distractor cubes to place and §4 names distractor placement as part of what a seed fixes; the scene holds one cube, so there is nothing to seed and a T3 state would be a T1 state wearing a different label
- C3 — unseen instructions are deferred to October (2026-08-07 deviation); the scene carries one frozen instruction string and no sealed paraphrase set
- C4 — the held-out object is deferred to October (2026-08-07 deviation); the scene holds one cube in one colour, so nothing can be held out

## Episodes per task

| Task | Episodes kept | Seeds attempted | Yield | Conditions | Dataset path |
|---|---|---|---|---|---|
| T2 | 80 | 181 | 44% | C1=80 | `t2` |
| **Total** | **80** | | | | |

## Coverage — the demonstrations **do not cover C1 evenly**

Only checker-successful episodes enter the dataset, so the demonstrations lie wherever the scripted expert succeeds — which is a **strict subset** of the C1 region that evaluation samples from. A policy trained here is scored on C1 poses it has no demonstration anywhere near. That is a property of the data, not of the policy, and it is stated here so it is not mistaken for one in the results.

**T2** — kept / attempted per cell of a 3×3 grid over C1, with the expert's success rate. Rows are x (near to far), columns are y.

| x range (m) | y -0.107 … -0.036 | y -0.036 … +0.036 | y +0.036 … +0.107 |
|---|---|---|---|
| 0.137 – 0.159 | 0/24 (0%) | 0/15 (0%) | 18/20 (90%) |
| 0.159 – 0.181 | 0/25 (0%) | 2/16 (12%) | 16/17 (94%) |
| 0.181 – 0.203 | 10/26 (38%) | 4/8 (50%) | 30/30 (100%) |

**3 of the 9 cells hold no demonstration at all.** Every one of them was sampled and the expert failed every seed drawn there (0/24, 0/15, 0/25), so collecting more seeds does not fill them — the expert cannot do the task there.

Only episodes the automated checker scored as **successes** are in the dataset. Failures are kept beside it in `collection_failures.jsonl` with their §5 failure label, so the collection yield above is a measured number rather than a missing one.

## Provenance

| | |
|---|---|
| Protocol SHA-256 | `84b90c11487a827b8dca5bf82da964d192c7fab16a496572353e93b57c841e05` |
| Protocol | `docs/benchmark-protocol.md`, frozen 2026-08-06, first committed 2026-08-07 |
| Robot | `so101_follower_sim` (SO-101, 5 arm DOF + gripper) |
| Simulator | MuJoCo, physics 240 Hz, control 60 Hz, recorded at 30 Hz |
| Cameras | 2 × 480×640 (external, wrist) |
| Observation | `observation.state`, `observation.velocity` (6 joints), plus one video stream per camera |
| Demonstration seed pool | 1000000–1000999, disjoint from the evaluation and development pools by construction |
| Seeding version | 1 |
| Demonstration region (C1) | 66.7 × 213.3 mm, centred (0.170, 0.000) m — measured against this arm's reachable set, not chosen |
| Card generated | 2026-08-09 |

## How to load it

**This dataset is not loadable by repository id.** `lerobot-train --dataset.repo_id=...` resolves against the Hugging Face Hub; there is nothing for it to resolve here. Clone the repository and point LeRobot at the clone:

```bash
git clone https://github.com/Bhavya007-17/p1-so101-sim-t2.git
lerobot-train --dataset.root=p1-so101-sim-t2 \
              --dataset.repo_id=Bhavya007-17/p1-so101-sim-t2 \
              --policy.type=act
```

`--dataset.repo_id` is still required as a name; `--dataset.root` is what actually gets read. No Git LFS is used, so a plain clone is the whole dataset.

## What this dataset is for

It is the training set for the sim phase of P1, a pre-registered comparison of three ways of turning an instruction into robot motion. The sim phase is a toolchain validation and a distillation study; the headline result is deferred to hardware in November, with human teleoperation. Anyone reproducing the benchmark needs the protocol as well as the data.

## Licence and citation

Apache-2.0. Cite the protocol by its git commit date, 2026-08-07, which is the earliest independently verifiable timestamp on it.
