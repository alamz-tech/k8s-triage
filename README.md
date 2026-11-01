# k8s-triage

Live debugging exercises for **The Pod That Won't Start: Debugging Kubernetes Live, With the Room**, presented at DevFest Abuja 2026 on **31 October 2026**.

This repository contains five broken Kubernetes workloads designed for live diagnosis in a browser-based environment such as the free Killercoda Kubernetes playground or a local cluster (minikube, kind, k3s).

---

## Core Rule

Read **STATUS** first, then **describe**, then **logs** only if a container actually started.

Most engineers run `kubectl logs` first. When a container never started, there are no logs to read.

### The Canonical Triage Sequence

Follow this four-step diagnostic order whenever a pod misbehaves:

```bash
# 1. Read STATUS, READY, and RESTARTS columns
kubectl get pods -n <namespace> -o wide

# 2. Inspect Kubernetes lifecycle events and container state (run this FIRST on failure)
kubectl describe pod <pod-name> -n <namespace>
# Or query via label selector:
kubectl describe pod -n <namespace> -l app=<app-name>

# 3. Read application logs (ONLY if RESTARTS > 0 or the container started)
kubectl logs <pod-name> -n <namespace>
# If the container crashed and is cycling in CrashLoopBackOff, inspect the crashed instance:
kubectl logs <pod-name> -n <namespace> --previous

# 4. If pod is Running but 0/1 Ready, check whether the Service has endpoints
kubectl get endpoints <service-name> -n <namespace>
```

> **Why this order matters:** If `RESTARTS` is 0 and `STATUS` is `ImagePullBackOff` or `Pending`, the container never started. Running `kubectl logs` will only return an error. `kubectl describe` gives you the answer immediately.

---

## Repository Structure

```text
.
├── live/
│   ├── broken.yaml              # Broken workloads for live Rounds 1, 2, and 3
│   ├── fix-round-1.yaml         # Fix manifest for checkout-api (Image layer)
│   ├── fix-round-2.yaml         # Fix manifest for orders-worker (Container layer)
│   ├── fix-round-3.yaml         # Fix manifest for reports-web (Networking layer)
│   └── fix.yaml                 # Combined fix for all three live rounds
├── assignments/
│   ├── assignment-round-4.yaml  # reports-batch (Scheduling layer)
│   └── assignment-round-5.yaml  # image-resizer (Resources layer)
├── .github/
│   └── ISSUE_TEMPLATE/          # Structured submission template for solutions
├── LICENSE
└── README.md
```

---

## The Five Rounds

| Round | Namespace | Workload | Layer | Symptom | When |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `round-1` | `checkout-api` | Image | `ImagePullBackOff` | Live masterclass (DevFest Oct 31) |
| 2 | `round-2` | `orders-worker` | Container | `CrashLoopBackOff` | Live masterclass (DevFest Oct 31) |
| 3 | `round-3` | `reports-web` | Networking | Running but never Ready (0/1) | Live masterclass (DevFest Oct 31) |
| 4 | `round-4` | `reports-batch` | Scheduling | Pending forever, 0 restarts | Take-home assignment (3-day window) |
| 5 | `round-5` | `image-resizer` | Resources | OOMKilled, exit code 137 | Take-home assignment (3-day window) |

During the live masterclass on 31 October 2026, we diagnose and resolve Rounds 1 to 3 together live. Rounds 4 and 5 are given as take-home challenges. Official solutions, root-cause explanations, and fixed manifests publish to this repository after the 3-day scholarship submission window closes on 3 November 2026.

---

## Getting Started: Two Ways to Run

Choose the workflow that matches your setup:

### Workflow A: Browser Playground or Quick Terminal (Killercoda / Local curl)

If working on [Killercoda Kubernetes Playground](https://killercoda.com/playgrounds/scenario/kubernetes) or running directly without cloning:

**1. Download the broken live manifests:**
```bash
curl -O https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/broken.yaml
kubectl apply -f broken.yaml
```

*(Alternatively, apply directly from URL without downloading:)*
```bash
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/broken.yaml
```

**2. Check the three workloads:**
```bash
kubectl get pods -n round-1
kubectl get pods -n round-2
kubectl get pods -n round-3
```

**3. For take-home assignments (Rounds 4 and 5):**
```bash
# Download assignment manifests
curl -O https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/assignments/assignment-round-4.yaml
curl -O https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/assignments/assignment-round-5.yaml

# Apply them to your cluster
kubectl apply -f assignment-round-4.yaml
kubectl apply -f assignment-round-5.yaml
```

---

### Workflow B: Git Clone (Edit Locally and Track Changes)

If you have Git and kubectl on your laptop (or want to clone inside Killercoda):

**1. Clone the repository and enter the directory:**
```bash
git clone https://github.com/alamz-tech/k8s-triage.git
cd k8s-triage
```

**2. Apply the live broken workloads:**
```bash
kubectl apply -f live/broken.yaml
```

**3. Inspect pod states:**
```bash
kubectl get pods -n round-1
kubectl get pods -n round-2
kubectl get pods -n round-3
```

**4. Apply the take-home assignments:**
```bash
kubectl apply -f assignments/assignment-round-4.yaml
kubectl apply -f assignments/assignment-round-5.yaml
```

You can edit manifests locally with your favorite editor (`nano`, `vim`, VS Code), make adjustments, and re-apply with `kubectl apply -f <path>`.

---

## Live Fix Manifests (Rounds 1 to 3)

After diagnosing each round during the masterclass, apply the corresponding fix:

### Round 1 Fix (`checkout-api`)
```bash
# Via remote URL
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/fix-round-1.yaml

# Or via local clone
kubectl apply -f live/fix-round-1.yaml
```

### Round 2 Fix (`orders-worker`)
```bash
# Via remote URL
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/fix-round-2.yaml

# Or via local clone
kubectl apply -f live/fix-round-2.yaml
```

### Round 3 Fix (`reports-web`)
```bash
# Via remote URL
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/fix-round-3.yaml

# Or via local clone
kubectl apply -f live/fix-round-3.yaml
```

### All-in-One Fix (Rounds 1 to 3)
```bash
kubectl apply -f https://raw.githubusercontent.com/alamz-tech/k8s-triage/main/live/fix.yaml
```

---

## Take-Home Assignments & Apprenticeship Sponsorship

The first 3 engineers to successfully solve both take-home triage assignments (Rounds 4 & 5), capture verified terminal proof, and submit within 3 days (by Tuesday, 3 November 2026, 11:59 PM WAT) will receive:

1. **Full-Tuition Sponsorship for the [3-Month Cloud & DevOps Apprenticeship](https://ewd.alamztech.com/apprenticeship)** (valued at ₦180,000 / $180) - all 12 production engineering sprints, zero-cost LocalStack labs, live weekend masterclasses, and capstone oral defense.
2. **1-Year Access to the Career Accelerator** (normally ₦100,000 / $100) - direct engineering mentorship, AI mock technical interview loops, CV positioning, and curated leads.
3. **Direct 1-on-1 Review & Feedback** on your diagnosis from the lead systems engineer.

**All other verified submissions within the 3-day window will receive:**
1. Invite to the Career Accelerator and community WhatsApp groups
2. 20% discount on the Production Cohort & Apprenticeship
3. Personalized architectural feedback on your diagnosis and fix

---

## Submission Instructions

1. **Fix the Manifest:** Update the workload YAML so the pod reaches a healthy `Running` state and `1/1` Ready condition.
2. **Capture Proof with Terminal Watermark:** Run the following command in your terminal so your GitHub username appears directly above the healthy pod table:
   ```bash
   echo "Verified by @<your-github-username>" && kubectl get pods -n <namespace>
   ```
   *(Example terminal output: `Verified by @johndoe` followed by `reports-batch ... 1/1 Running`)*.
3. **Write Your Explanation:** Formulate one sentence naming the failure layer and the exact change you made.
4. **Submit:** Either open a **[New Submission Issue](https://github.com/alamz-tech/k8s-triage/issues/new/choose)** using the pre-formatted submission form or send a message with your verified terminal output.
5. **Submission Deadline:** Exactly 3 days from the DevFest masterclass - Tuesday, 3 November 2026 at 11:59 PM WAT.

Submissions with incorrect diagnoses will still receive detailed feedback if your reasoning is explained.

---

## Cleanup / Reset

To delete all exercise namespaces and start fresh:

```bash
kubectl delete ns round-1 round-2 round-3 round-4 round-5 --ignore-not-found
```

---

## Go Beyond Syntax: The Cloud & DevOps Apprenticeship

Debugging broken pods is an essential skill, but real-world cloud engineering goes far beyond syntax and ad-hoc troubleshooting.

Production infrastructure demands:
- Designing self-healing, distributed Kubernetes architectures.
- Managing reliable cloud environments with Infrastructure as Code (Terraform).
- Constructing secure CI/CD deployment pipelines under zero-downtime constraints.
- Simulating enterprise cloud environments locally without costly cloud provider bills.

If you are preparing for certifications like KCNA and CKAD or looking to transition into production-grade infrastructure roles, explore the **[Cloud & DevOps Apprenticeship](https://ewd.alamztech.com/apprenticeship)** at [ewd.alamztech.com/apprenticeship](https://ewd.alamztech.com/apprenticeship).

Hands-on engineering sprints, local-first architectures, portfolio capstone projects, and direct mentorship to help you build resilient systems with confidence.

---

## License

MIT License. See [LICENSE](LICENSE) for details.
