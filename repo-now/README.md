# k8s-triage

Live debugging exercises for **The Pod That Won't Start: Debugging Kubernetes Live, With the Room**, presented at DevFest Abuja 2026.

This repository contains five broken Kubernetes workloads designed for live diagnosis in a browser-based environment such as the free Killercoda Kubernetes playground.

## Core Rule

Read **STATUS** first, then **describe**, then **logs** only if a container actually started.

Most engineers run `kubectl logs` first. When a container never started, there are no logs to read.

---

## The Five Rounds

| Round | Namespace | Workload | Layer | Symptom | When |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `round-1` | `checkout-api` | Image | `ImagePullBackOff` | Live masterclass |
| 2 | `round-2` | `orders-worker` | Container | `CrashLoopBackOff` | Live masterclass |
| 3 | `round-3` | `reports-web` | Networking | Running but never Ready (0/1) | Live masterclass |
| 4 | `round-4` | `reports-batch` | Scheduling | Pending forever, 0 restarts | Take-home assignment |
| 5 | `round-5` | `image-resizer` | Resources | OOMKilled, exit code 137 | Take-home assignment |

Solutions, root-cause explanations, and fixed manifests publish to this repository after the session on the evening of 24 October 2026.

---

## Live Setup (Rounds 1 to 3)

Open a terminal on [Killercoda Kubernetes Playground](https://killercoda.com/playgrounds/scenario/kubernetes) and apply the broken workloads:

```bash
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/repo-now/broken.yaml
```

If working from a cloned repository:

```bash
kubectl apply -f repo-now/broken.yaml
```

Check the status of the three workloads:

```bash
kubectl get pods -n round-1
kubectl get pods -n round-2
kubectl get pods -n round-3
```

---

## Take-Home Assignments (Rounds 4 and 5)

Apply each assignment manifest individually:

```bash
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/repo-now/assignment-round-4.yaml
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/repo-now/assignment-round-5.yaml
```

If working from a cloned repository:

```bash
kubectl apply -f repo-now/assignment-round-4.yaml
kubectl apply -f repo-now/assignment-round-5.yaml
```

### Submission Instructions

1. Fix the manifest so the pod reaches a healthy `Running` state and `Ready` condition.
2. Take a screenshot showing `kubectl get pods` in the respective namespace with status `Running` and `1/1` Ready.
3. Write one sentence naming the failure layer and the exact change you made.
4. Submit via a GitHub issue on this repository or message Hussein Alamutu on LinkedIn at [linkedin.com/in/hussein-alamutu](https://linkedin.com/in/hussein-alamutu).
5. Deadline: 31 October 2026 (one week from session date).

Submissions with incorrect diagnoses will still receive detailed feedback if your reasoning is explained.

---

## Cleanup / Reset

To delete all exercise namespaces and start fresh:

```bash
kubectl delete ns round-1 round-2 round-3 round-4 round-5 --ignore-not-found
```

---

## License

MIT License. See [LICENSE](LICENSE) for details.
