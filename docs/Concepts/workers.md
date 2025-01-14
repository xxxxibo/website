---
sidebar_position: 5
title: Workers
sidebar_label: Workers
---
## Worker

Example of a local model service running models through arcadia to drive rapid deployment of various fastchat-based model services.Worker resource is defined:
```golang
// WorkerSpec defines the desired state of Worker
type WorkerSpec struct {
    CommonSpec `json:",inline"`

    // Normal worker or vLLM Worker(inference acceleration)
    Type WorkerType `json:"type,omitempty"`

    // Model this worker wants to use
    Model *TypedObjectReference `json:"model"`

    // Resource request&limits including
    // - CPU or GPU
    // - Memory
    Resources corev1.ResourceRequirements `json:"resources,omitempty"`

    // Storage claimed to store model files
    Storage *corev1.PersistentVolumeClaimSpec `json:"storage,omitempty"`
}

// WorkerStatus defines the observed state of Worker
type WorkerStatus struct {
    // PodStatus is the observed stated of Worker pod
    // +optional
    PodStatus corev1.PodStatus `json:"podStatus,omitempty"`

    // ConditionedStatus is the current status
    ConditionedStatus `json:",inline"`
}
```
#### Spec Field Details
* CommonSpec: Basic descriptive information
* Type: Type of model worker node, currently classified as：
    - fastchat
    - fastchat-vllm
* Model: The CR of the model run by the model worker node, which records the storage information of the model and is used to load the model file when the model is deployed.
* Resource: The resources requested by the model worker node when it is running, including：
    - CPU
    - Memory
    - GPU: "nvidia.com/gpu: "1" # request 1 GPU"
* Storage: Requested persistent storage (If it is empty, emptydir is used by default to share data within the pod)

Worker startup process, registration session, and invocation process are illustrated in the following figure:

![](./images/2024-01-05-15-27-01.png)
