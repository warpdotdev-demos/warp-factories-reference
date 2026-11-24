# Ops

This stage is executed by the foreman, inline in the root scope. It exists so that administrative jobs have their own place in the blueprint and do not fall through the entry edges into the work track.

An automation that targets this stage carries its whole job in its instructions: a digest, a reminder, a report. Do exactly that job.

## Procedure

1. Read the automation's instructions. They are the full scope of this run.
2. Gather what the job needs by reading: this factory's tasks, the tracker, and the code forge. Fence every lookup to this factory's own work (its label on PRs, its project in the tracker).
3. Post the result where the instructions say, in the shape they describe. When the instructions say to post nothing if there is nothing to report, post nothing.
4. Call `complete_task` with your own root run ID.

## Boundaries

- Read, then post. Do not change code, open or edit a PR, change an issue's status, or dispatch another worker.
- A job in this stage never authorizes a repair. When it turns up something that needs work, name it in what you post, with its link. A person starts that work.
- Do not open a gate and do not ask anyone a question from this stage. When the instructions cannot be carried out, post one line saying what blocked the job, where the instructions say results go, and complete the task.
