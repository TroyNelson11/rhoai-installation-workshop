# 9. Configure distributed workloads

<p align="center">
<a href="/docs/08-configure-rhoai.md">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="/README.md">Next</a>
</p>

### Objectives

- Configuring Kueue (ResourceFlavor, ClusterQueue, and LocalQueue) for distributed workload scheduling
- Understanding the relationship between Ray, Kueue, and the Training Operator

### Rationale

- You can iterate faster and experiment more frequently because of the reduced processing time.
- You can use larger data sets, which can lead to more accurate models.
- You can use complex models that could not be trained on a single node.
- You can submit distributed workloads at any time, and the system then schedules the distributed workload when the required resources are available.

### Takeaways

- [Features](https://kueue.sigs.k8s.io/docs/overview/#features-overview) of Kueue
- Kueue flow [diagram](https://kueue.sigs.k8s.io/docs/overview/#high-level-kueue-operation)
- Kueue [Concepts](https://kueue.sigs.k8s.io/docs/concepts/)
- The distributed workload stack is **Kueue + Ray + Training Operator**
- Kueue was installed as a standalone operator in [step 6](/docs/06-install-rhoai-dependencies.md)

> You can run distributed workloads from data science pipelines, from Jupyter notebooks, or from Microsoft Visual Studio Code files.
> [More Info](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/3.2/html/managing_openshift_ai/managing-workloads-with-kueue)

Components required for Distributed Workloads

- Dashboard
- Workbenches
- Data Science Pipelines
- Kueue (standalone operator, installed in step 6)
- Ray

## Steps

- [ ] Verify the Kueue operator is running (installed as a standalone operator in step 6)

      oc get pods -n openshift-kueue-operator

> Expected output
>
> `NAME                                        READY   STATUS    RESTARTS   AGE`\
> `kueue-controller-manager-xxxxxxxxx-xxxxx    1/1     Running   0          1h`

- [ ] Verify the Ray operator is running

      oc get pods -n redhat-ods-applications | grep kuberay

> Expected output
>
> `kuberay-operator-xxxxxxxxx-xxxxx    1/1     Running   0          1h`

## 9.1 Create a Kueue resource flavor

Resources in a cluster are typically not homogeneous. A ResourceFlavor is an object that describes these resource variations (i.e. Nvidia A100 versus L40S GPUs) and allows you to associate them with cluster nodes through labels, taints and tolerations.

A cluster administrator can create a ResourceFlavor object named `default-flavor` with a toleration for GPU-tainted nodes.

In RHOAI, Red Hat supports only a single cluster queue per cluster (that is, homogenous clusters), and only empty resource flavors.

## Steps

- [ ] Apply the configuration to create the `default-flavor`

      oc apply -f configs/09/rhoai-kueue-default-flavor.yaml

> Expected output
>
> `resourceflavor.kueue.x-k8s.io/default-flavor created`

## 9.2 Create a cluster queue to manage the Kueue resource flavor

The Kueue ClusterQueue object manages a pool of cluster resources such as pods, CPUs, memory, and accelerators. A cluster can have multiple cluster queues, and each cluster queue can reference multiple resource flavors.

What is this cluster-queue doing? The example configures a cluster queue to assign a quota of 9 CPUs, 36 GiB memory, 5 pods, and 5 NVIDIA GPUs.

- The sum of the CPU requests is less than or equal to 9.
- The sum of the memory requests is less than or equal to 36Gi.

> [!TIP]
> Replace the example quota values (9 CPUs, 36 GiB memory, and 5 NVIDIA GPUs) with the appropriate values for your cluster queue in a real world scenario. The cluster queue will start a distributed workload only if the total required resources are within these quota limits, otherwise the cluster queue does not admit the distributed workload. Only homogenous NVIDIA GPUs are supported.

## Steps

- [ ] Apply the configuration to create the `cluster-queue`

      oc apply -f configs/09/rhoai-kueue-cluster-queue.yaml

> Expected output
>
> `clusterqueue.kueue.x-k8s.io/cluster-queue created`

## 9.3 Create a local queue that points to your cluster queue

A LocalQueue is a namespaced object that groups closely related Workloads that belong to a single namespace. Users submit jobs to a LocalQueue, instead of to a ClusterQueue directly. A cluster administrator can optionally define one local queue in a project as the default local queue for that project.

In this example, the `kueue.x-k8s.io/default-queue: "true"` annotation defines this local queue as the default local queue for the `sandbox` project. If a user submits a distributed workload in the `sandbox` project and that distributed workload does not specify a local queue in the cluster configuration, Kueue automatically routes the distributed workload to the `local-queue-test` local queue. The distributed workload can then access the resources that the `cluster-queue` cluster queue manages.

## Steps

- [ ] Apply the configuration to create the local-queue object

      oc project sandbox || oc new-project sandbox

      oc apply -f configs/09/rhoai-kueue-local-queue.yaml

> Expected output
>
> `localqueue.kueue.x-k8s.io/local-queue-test created`

> [!NOTE]
> Users submit jobs to a LocalQueue, instead of to a ClusterQueue directly. Tenants can discover which queues they can submit jobs to by listing the local queues in their namespace.

- [ ] Verify the local queue is created

      oc get -n sandbox queues

> Expected output
>
> `NAME               CLUSTERQUEUE    PENDING WORKLOADS   ADMITTED WORKLOADS`\
> `local-queue-test   cluster-queue   0                   0`

## Validation

![](/assets/09-validation.gif)

## Automation key (Catch up)

- [ ] From this repository's root directory, run below command

```sh
./scripts/setup.sh -s 9
```

<p align="center">
<a href="/docs/08-configure-rhoai.md">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="/README.md">Next</a>
</p>
