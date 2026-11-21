# Lab 3 | Monitoring, Logging & DevSecOps

**DevOps and Automation Lab**  
**Student:** Arghyadipta Goswami | **Roll No.:** 2301010192  
**GitHub:** [`ArghyadiptaGoswami4259`](https://github.com/ArghyadiptaGoswami4259)  
**Repository:** [ArghyadiptaGoswami4259/DEV-OPS-LAB-3](https://github.com/ArghyadiptaGoswami4259/DEV-OPS-LAB-3)

An evidence-backed record of infrastructure configuration, local provisioning, monitoring, centralized logging, pipeline security scanning, feature flags, load testing and browser automation — all seven lab questions in one repository.

> **Quick start:** open [REPORT.md](REPORT.md) for the written answers, [docs/EVIDENCE.md](docs/EVIDENCE.md) for the screenshot proof, and [docs/REPRODUCE.md](docs/REPRODUCE.md) to rerun any experiment yourself.

## Table of contents

| Resource | Contents |
|---|---|
| [Full lab report](REPORT.md) | Q1–Q7: objectives, configurations, execution, results and limitations |
| [Evidence gallery](docs/EVIDENCE.md) | 36 images from the original document plus 2 supplementary ELK screenshots |
| [Architecture](docs/ARCHITECTURE.md) | Actual components, ports and the two application execution modes |
| [Reproduction guide](docs/REPRODUCE.md) | Steps for rerunning experiments without confusing WSL and Docker targets |
| [Command reference](commands.txt) | Commands grouped by question; not a fabricated terminal transcript |
| [Security findings](docs/SECURITY-FINDINGS.md) | Bandit finding, audit scope, Jenkins gate behavior and unresolved risks |
| [Transcribed results](docs/results/) | Load-test table as CSV and machine-readable observations as JSON |
| [Final checklist](docs/FINAL_CHECKLIST.md) | Submission checks, cleanup and explicitly unproven items |
| [References](docs/REFERENCES.md) | Documentation links used while building the lab |
| [Original evidence document](docs/source/Lab3-Evidence.docx) | Unchanged 17-page source document |

## Question map

| Q | Topic | Where the work lives | Result |
|---|---|---|---|
| Q1 | Configure infrastructure & automate app configuration | [`ansible/deploy.yml`](ansible/deploy.yml), [`ansible/inventory.ini`](ansible/inventory.ini), [`app/index.html`](app/index.html) | Nginx installed and page deployed; repeat run `changed=0` |
| Q2 | Provision scalable infrastructure | [`terraform/main.tf`](terraform/main.tf), [`terraform/.terraform.lock.hcl`](terraform/.terraform.lock.hcl) | 2 resources applied: one image, one container on port 8085 |
| Q3 | Monitoring | [`monitoring/`](monitoring/) — Flask app, `prometheus.yml`, Compose with Grafana | Prometheus target UP; request counter climbed from 22 to ~72 |
| Q4 | Centralized logging | [`elk/compose.yaml`](elk/compose.yaml), [`elk/logstash/pipeline/logstash.conf`](elk/logstash/pipeline/logstash.conf) | 32 JSON events indexed and parsed in Kibana Discover |
| Q5 | DevSecOps pipeline | [`Jenkinsfile`](Jenkinsfile), [`bandit-report.txt`](bandit-report.txt), [`pip-audit-report.json`](pip-audit-report.json) | Build #1 SUCCESS, both scan reports archived |
| Q6 | Feature flags, load testing, Selenium | [`monitoring/app.py`](monitoring/app.py), [`tests/test_selenium.py`](tests/test_selenium.py), [`docs/results/load-tests.csv`](docs/results/load-tests.csv) | Flag OFF/ON verified; 1,681.71 req/s at p95 15 ms; Selenium PASS |
| Q7 | Report & evidence | This repository | Markdown report plus image gallery and manifest |

## Recorded results

| Experiment | Observed result | Evidence |
|---|---|---|
| Ansible configuration | First run `changed=2`; repeat `changed=0`; both `failed=0` | [E05 / E08](docs/EVIDENCE.md#q1-ansible) |
| Terraform provisioning | 2 resources added: one image and one container | [E09–E15](docs/EVIDENCE.md#q2-terraform) |
| Prometheus / Grafana | Target UP; request counter increases from 22 to approximately 72 | [E18–E23](docs/EVIDENCE.md#q3-monitoring) |
| ELK logging | 32 JSON sample events indexed and visible as parsed fields | [E24–E25](docs/EVIDENCE.md#q4-logging) |
| Bandit | 1 Medium B104 finding; 0 High findings in the saved report | [E26](docs/evidence/26-bandit-medium-finding.png) |
| pip-audit | 8 resolved dependencies; no known vulnerabilities reported at scan time | [E27](docs/evidence/27-dependency-audit-report.png) |
| Jenkins | Build #1 finishes SUCCESS and archives both scan reports | [E30](docs/evidence/30-jenkins-scans-success.png) |
| Feature flags | OFF returns the original message; ON returns `NEW FEATURE ENABLED` | [E32–E33](docs/EVIDENCE.md#q6-testing) |
| ApacheBench, run B | 1,000 requests, 20 concurrent, 0 failures, 1,681.71 requests/s, p95 15 ms | [E34](docs/evidence/34-apachebench-run-b.png) |
| Selenium | Homepage and enabled-feature checks pass in a remote Chrome session | [E36](docs/evidence/36-selenium-passed.png) |

These are recorded results, not live service-health claims. The two ApacheBench runs are reported separately in [REPORT.md](REPORT.md#q6-feature-flags-load-testing-and-selenium).

## Implementation map

```text
ansible/                     Local Nginx configuration playbook and inventory
app/index.html               Static page managed by Ansible
terraform/main.tf            Local Docker image/container provisioning
terraform/.terraform.lock.hcl Provider dependency lock
monitoring/                  Flask app, Dockerfile, Compose and Prometheus config
elk/                         Elasticsearch, Logstash and Kibana configuration
elk/logstash/pipeline/       TCP JSON-lines ingestion pipeline
tests/test_selenium.py       Remote Chrome browser test
Jenkinsfile                  Checkout, scanner installation, Bandit and pip-audit
bandit-report.txt            Saved original static-analysis report
pip-audit-report.json        Saved original dependency audit
README.md / REPORT.md        Submission landing page and full report
commands.txt                 Command reference
docs/                        Evidence, results, architecture and review notes
scripts/                     Read-only capture and repository-check helpers
```

The Logstash configuration is four directory levels below the repository root, so it does not appear in a shallow `find ... -maxdepth 3` listing. It is present at [elk/logstash/pipeline/logstash.conf](elk/logstash/pipeline/logstash.conf).

## Tools and versions recorded

| Tool | Version / configuration | Evidence |
|---|---|---|
| Ubuntu on WSL2 | Working directory `/home/user/devops-lab3` | Terminal context |
| Docker / Compose | 29.8.0 / v5.5.1 | [E01](docs/evidence/01-docker-compose-and-hello-world.png) |
| Python (Ansible host) | 3.14.4 | [E02](docs/evidence/02-ansible-version.png) |
| Ansible | core 2.20.1 | [E02](docs/evidence/02-ansible-version.png) |
| Terraform / Docker provider | 1.14.0 / 3.9.0 | [E03](docs/evidence/03-terraform-version.png), [E09](docs/evidence/09-terraform-init.png) |
| Flask base image | `python:3.12-slim` | [monitoring/Dockerfile](monitoring/Dockerfile) |
| Prometheus / Grafana | `prom/prometheus:latest`, `grafana/grafana:latest` | [monitoring/compose.yaml](monitoring/compose.yaml) |
| Elasticsearch / Logstash / Kibana | 8.17.3 | [elk/compose.yaml](elk/compose.yaml) |
| Jenkins | Existing local service on port 8080 | [E30](docs/evidence/30-jenkins-scans-success.png) |
| Selenium | Remote Chrome container, headless | [tests/test_selenium.py](tests/test_selenium.py) |

## Scope and honest limitations

This is a local teaching environment, not a production-secure deployment.

- Terraform demonstrates initial provisioning, not a recorded multi-replica scaling exercise.
- The ELK records are deliberately generated sample events, not an automatically forwarded stream of Flask access logs.
- The current `Jenkinsfile` allows Bandit exit code 1 for **any findings**, not only an approved B104 exception; a green build is not a clean static-analysis report.
- The dependency audit covers its resolved Python requirements, not every image or service in the lab.
- Monitoring evidence comes from the Docker application. Feature-flag and final load/browser tests were performed after stopping that container and running Flask in WSL. A fresh Grafana scrape of that WSL process has not been demonstrated. See [the two execution modes](docs/ARCHITECTURE.md#application-execution-modes).

## Before submitting

Read [FINAL_CHECKLIST.md](docs/FINAL_CHECKLIST.md). In particular, untrack `terraform/tfplan` while preserving the local copy, avoid publishing credentials or Terraform state, and review the unauthenticated Elasticsearch port binding. The included `.gitignore` prevents future accidental staging but cannot remove a file already tracked by Git.

Run the read-only repository check at any time:

```bash
python3 scripts/check-submission.py
```

Submit the repository URL — <https://github.com/ArghyadiptaGoswami4259/DEV-OPS-LAB-3> — not a local Jenkins/Grafana URL, and never publish local admin passwords, tokens, Terraform state or binary plan files.
