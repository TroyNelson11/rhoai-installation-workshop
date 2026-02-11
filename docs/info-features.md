# OpenShift AI Overview

![](/assets/openshift-ai-3.png)

![](/assets/openshift-ai-1.png)

![](/assets/openshift-ai-2.png)

Overview of the features in Red Hat OpenShift dependencies.

| Component               | Sub-components | Training | Serving | Description                                                           |
| ----------------------- | -------------- | -------- | ------- | --------------------------------------------------------------------- |
| RHOAI Operator          |                | x        | x       | Deploys and maintains the components for RHOAI                        |
| `dashboard`             |                | x        | x       | Admin and user primary interface                                      |
| `workbenches`           |                | x        |         | Notebooks images (i.e. Jupyter, code-server, RStudio)                 |
| `aipipelines`           |                | x        |         | Schedulable multi-step ML workflow execution graph                    |
| `distributed-workloads` |                | x        |         | Scalable ML library for distributed training and fine-tuning          |
|                         | Kuberay        | x        |         | Manages remote Ray clusters on K8s for running distributed workloads  |
|                         | Kueue          | x        |         | Manages quotas, queuing and how distributed workloads consume them    |
| `kserve`                |                |          | x       | Serverless inference w/Triton, HuggingFace, PyTorch, TF, LightGBM... |
| `modelregistry`         |                | x        | x       | Central registry for managing ML model metadata and versions          |
| `ray`                   |                | x        |         | Distributed compute framework for ML training and inference           |
| `trainingoperator`      |                | x        |         | Manages distributed training jobs (PyTorch, TensorFlow, etc.)         |
| `trustyai`              |                |          | x       | Model explainability and bias detection                               |

[Supported Configurations](https://access.redhat.com/articles/rhoai-supported-configs)

## Cluster Worker Node Size

| Qty | vCPU | Memory | Qty | GPU Arch  | Notes                                                                                     |
| --- | ---- | ------ | --- | --------- | ----------------------------------------------------------------------------------------- |
| 3   | 4    | 16     | 0   | --------- | not enough resources                                                                      |
| 2   | 8    | 32     | 0   | --------- | minimum required to install all the components                                            |
| 4   | 4    | 16     | 0   | --------- | minimum required to install all the components                                            |
| 1   | 16   | 64     | 0   | --------- | minimum required to install all the components                                            |
| 5   | 4    | 16     | 0   | --------- | minimum required to create a data science project with a `small` workbench container size |
| 1   | 16   | 64     | 0   | --------- | minimum required to create a data science project with a `small` workbench container size |
| 6   | 4    | 16     | 0   | --------- | minimum required to run the distributed workloads demo `0_basic_ray.ipynb`                |
| 6   | 4    | 16     | 1   | nvidia t4 | minimum required to run the distributed workloads demo `1_cluster_job_client.ipynb`       |
| 1   | 16   | 64     | 2   | nvidia t4 | minimum required to run the distributed workloads demo `2_basic_interactive.ipynb`        |
| 1   | 16   | 64     | 2   | nvidia t4 | minimum required to run the distributed workloads demo `2_basic_interactive.ipynb`        |
