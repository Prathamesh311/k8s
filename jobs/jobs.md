
# Jobs

- Kubernetes Jobs are resources used to run batch tasks in a Kubernetes cluster.
- They ensure that a specified number of Pods successfully complete a task before marking the Job as done.
- Jobs are crucial for executing one-time or recurring tasks, such as data migrations, backups, or batch processing.

## Types of Kubernetes Jobs

Kubernetes supports different ways of running Jobs depending on how the task should execute.

### Non-Parallel Jobs

Non-Parallel Jobs execute tasks sequentially, meaning one Pod runs at a time.

If we specify:

completions: 3

The Job will complete only after 3 Pods have successfully finished execution.

In this case:
- The first Pod runs and completes.
- Then the second Pod runs.
- Then the third Pod runs.
- After all 3 Pods complete successfully, the Job is marked as completed.

This type of Job is useful for tasks that should not run in parallel.

---

### Parallel Jobs with a Fixed Completion Count

In this type, multiple Pods run at the same time to complete the task faster.

If we specify:

completions: 6  
parallelism: 3  

This means:
- 3 Pods will run simultaneously (because parallelism = 3).
- After those 3 Pods complete their task, the next set of Pods will start.
- The Job is considered complete only after 6 successful executions.

Even though only 3 Pods run at a time, the total number of successful completions must reach 6.

This type is useful when tasks can be divided and processed in parallel.

---

### Parallel Jobs with a Work Queue

In this type, Pods run in parallel but we do not define a fixed number of completions.

If we specify:

parallelism: 3

This means:
- 3 Pods will start at the same time.
- These Pods coordinate with each other or an external system to fetch tasks from a shared queue.
- The Job continues running until all tasks in the queue are processed.

This type is useful when the workload is dynamic and tasks are not fixed in advance.

---

## restartPolicy in Jobs

restartPolicy defines how the container behaves after it exits.

- Always -> The container always restarts (commonly used in Deployments).
- OnFailure -> The container restarts only if it fails.
- Never -> Never restarts the container, even if it fails.

---



