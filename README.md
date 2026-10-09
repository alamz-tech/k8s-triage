# k8s-triage

Live debugging exercises for **The Pod That Won't Start: Debugging Kubernetes Live, With the Room**, presented at DevFest Abuja 2026 on **31 October 2026**.

This repository contains five broken Kubernetes workloads designed for live diagnosis in a browser-based environment such as the free Killercoda Kubernetes playground.

## Core Rule

Read **STATUS** first, then **describe**, then **logs** only if a container actually started.

Most engineers run `kubectl logs` first. When a container never started, there are no logs to read.

---

## The Five Rounds

| Round | Namespace | Workload | Layer | Symptom | When |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `round-1` | `checkout-api` | Image | `ImagePullBackOff` | Live masterclass (DevFest Oct 31) |
| 2 | `round-2` | `orders-worker` | Container | `CrashLoopBackOff` | Live masterclass (DevFest Oct 31) |
| 3 | `round-3` | `reports-web` | Networking | Running but never Ready (0/1) | Live masterclass (DevFest Oct 31) |
| 4 | `round-4` | `reports-batch` | Scheduling | Pending forever, 0 restarts | Take-home assignment (3-day bounty) |
| 5 | `round-5` | `image-resizer` | Resources | OOMKilled, exit code 137 | Take-home assignment (3-day bounty) |

During the live masterclass on 31 October 2026, we diagnose and resolve Rounds 1 to 3 together live. Rounds 4 and 5 are given as take-home challenges. Official solutions, root-cause explanations, and fixed manifests publish to this repository after the 3-day scholarship bounty window closes on 3 November 2026.

---

## Live Setup (Rounds 1 to 3)

Open a terminal on [Killercoda Kubernetes Playground](https://killercoda.com/playgrounds/scenario/kubernetes) and apply the broken workloads:

```bash
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/broken.yaml
```

If working from a cloned repository:

```bash
kubectl apply -f broken.yaml
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
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/assignment-round-4.yaml
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/assignment-round-5.yaml
```

If working from a cloned repository:

```bash
kubectl apply -f assignment-round-4.yaml
kubectl apply -f assignment-round-5.yaml
```

### 🏆 Grand Bounty: First 3 Engineers Win 100% Full-Tuition Apprenticeship + Career Accelerator

The **first 3 engineers** to successfully solve the two take-home triage assignments (Rounds 4 & 5), capture verified terminal proof, and get back to us within **3 days** (by **3 November 2026, 11:59 PM WAT**) by opening a GitHub Issue or sending a direct message will receive:

1. 🎓 **100% All-Paid Access to the 3-Month Cloud & DevOps Apprenticeship** (valued at ₦180,000 / $180) — all 12 production engineering sprints, zero-cost LocalStack labs, live weekend masterclasses, and oral capstone defense completely free.
2. 🚀 **100% Free Lifetime Access to the Career Accelerator** (normally ₦100,000 / $100) — direct engineering mentorship, AI mock technical interview loops, CV positioning, and exclusive platform leads at zero extra cost.
3. 💬 **Direct 1-on-1 Review & Feedback** on your diagnosis from the lead systems engineer.

**All other verified submissions within the 3-day window will receive:**
1. Invite to the Career Accelerator & Community WhatsApp Groups
2. 20% Discount to The Production Cohort & Apprenticeship
3. Personalized architectural feedback on your diagnosis and fix

### Submission Instructions

1. **Fix the Manifest:** Update the workload YAML so the pod reaches a healthy `Running` state and `1/1` Ready condition.
2. **Capture Proof with Terminal Watermark:** Run the following command in your terminal so your GitHub username appears directly above the healthy pod table:
   ```bash
   echo "Verified by @<your-github-username>" && kubectl get pods -n <namespace>
   ```
   *(Example terminal output: `Verified by @johndoe` followed by `reports-batch ... 1/1 Running`)*.
3. **Write Your Explanation:** Formulate one sentence naming the failure layer and the exact change you made.
4. **Submit:** Either open a **[New Submission Issue](https://github.com/alamz-tech/k8s-triage/issues/new/choose)** using the pre-formatted submission form or send a message with your verified terminal output.
5. **Bounty Window / Deadline:** Exactly **3 days** from the DevFest masterclass — **Tuesday, 3 November 2026 at 11:59 PM WAT**.

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
