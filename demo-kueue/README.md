# Kueue Demo


### Setup 

1. Install the Kueue Operator (Red Hat build of Kueue kueue-operator.v1.0.1 provided by Red Hat, Inc)

Use the console and accept the defaults.

2. Using the console, create an instance of the operator (a.k.a. a Kueue) so the
CRDs get created.

3. Create the cluster queue, resource flavor and local queue.

oc create -f kueues.yaml

### Simple job control demo w/o kueue

Create a simple job that runs for 30 seconds.

```bash
oc create -f job-no-queue.yaml
```

The job should be in a suspended status.

```bash
oc get jobs
```
```console
NAME               STATUS      COMPLETIONS   DURATION   AGE
sample-job-xlgjq   Suspended   0/3                      4s
```

Resume the suspended job.

```bash
kubectl patch job sample-job-xlgjq --patch '{"spec":{"suspend":false}}'
```

```console
job.batch/sample-job-xlgjq patched
```

```bash
oc get jobs
```
```console
NAME               STATUS     COMPLETIONS   DURATION   AGE
sample-job-xlgjq   Running    0/3           6s         35s
```

### Simple job control demo with kueue

In separate terminal windows watch `clusterqueue`, `localqueue`, `jobs`, 
`workloads` and `pods` then
execute the following loop to schedule 10 jobs. Kueue will manage the jobs.

```bash
for i in `seq `10`
 do
  oc create -f job.yaml
 done
```

- 10 jobs (workloads) are submitted.
- Each job requires that (3) pods run in parallel.
- Each pod requires 1 cpu and 200MB of memory.
- The ResourceFlavor cpu quota = 9
- This means only 3 jobs are permitted to run simultaneously.
- Watch kueue manage this.
- As workloads are completed (~30 seconds) pending workloads are admitted.

[Video Screenshot of this demo](https://people.redhat.com/bkozdemb/downloads/kueue-demo-01.m4v)

