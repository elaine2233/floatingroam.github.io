---
layout: post
title: "On-Premise Deployment (Part 2): Pitfalls in Practice"
date: 2026-05-08
author: floating roam
categories: [Technology, Deployment]
tags: [On-Premise, Intranet, Security Compliance, Performance Gap]
excerpt: "Network isolation, security compliance, model updates, and the hidden performance gap between cloud APIs and on-premise deployment—these are the real headaches."
version: 2
---

# On-Premise Deployment (Part 2): Pitfalls in Practice

The previous post covered who needs on-premise deployment and how to select solutions. This post covers the pitfalls in actual deployment—network isolation, security compliance, model updates, performance gaps. These are the real headaches.

## Network Isolation: Challenges of Intranet Environments

Intranets are "islands"—no external internet access, no dependency downloads, no external API calls.

### Dependency Issues

Open-source libraries typically need to be downloaded from PyPI. What about intranet environments?

Solution: **Offline installation packages**.

```bash
# Download dependencies in external network
pip download -r requirements.txt -d ./packages

# Package and transfer to intranet
tar -czf packages.tar.gz ./packages

# Install in intranet environment
pip install --no-index --find-links=./packages -r requirements.txt
```

Even more troublesome: Some libraries dynamically download model files (like HuggingFace's transformers). These files can be several GB and need to be downloaded in advance and packaged for transfer.

### Logging and Monitoring

Cloud deployment can use Prometheus + Grafana for monitoring. In intranet environments, this stack needs to be self-hosted.

Lightweight solution:
- Logs: Loki + Grafana
- Metrics: Prometheus + Grafana

Complete solution:
- Logs: ELK Stack
- Metrics: Prometheus + Grafana
- Alerts: Self-built alert service

## Security and Compliance: Clients' Core Concerns

On-premise deployment is just the first step; security and compliance are clients' core concerns.

### Data Trail and Traceability

Clients needing on-premise deployment care deeply about:

- **Data trail**: All data processing has records
- **Auditable**: Complete operation logs, can pass audits
- **Traceable**: Every result can be traced back to original data
- **Documented**: Key operations have evidence, can be reviewed

This falls under AI Governance—ensuring AI system usage complies with regulations and internal policies.

### Access Control

- **Principle of least privilege**: Each user granted only necessary permissions
- **Separation of duties**: Development, operations, and audit roles separated
- **Audit logs**: All operations recorded, traceable

### Data Stays in Intranet

This is the most basic requirement. Checklist:

- Are model calls through intranet APIs?
- Is vector search completed within the intranet?
- Are logs desensitized?
- Are backups encrypted?

## Model Updates: The "Expedition" in Intranet Environments

In cloud deployment, model updates are simple—swap the image, redeploy.

In intranet environments, every update is an "expedition":

1. Test new model in external network
2. Package model files (possibly tens of GB)
3. Go through approval process, transfer to intranet
4. Re-test in intranet environment
5. Canary deployment, gradual replacement

This process takes a week at best, a month at worst.

### Version Management Strategy

- **Major version updates**: Quarterly, significant feature upgrades
- **Minor version updates**: Monthly, bug fixes and small optimizations
- **Emergency updates**: As needed, security vulnerability fixes

### Rollback Mechanism

Updates can fail; a rollback mechanism is essential:

- Keep recent model versions
- Each version has independent Docker image
- Rollback is just switching images, completed in minutes

## Performance Gap: The Hidden Pitfall of Cloud API vs On-Premise

This is the most overlooked yet deadliest issue in on-premise deployment.

Many assume: download an open-source model, deploy it internally, and the results should be similar to the official API. After all, the model weights are the same, right?

**Reality may surprise you.**

### The Rerank Model "Anomaly"

A real case: the same Rerank model, calling the official API externally vs self-hosting internally—completely different ranking results.

Same query, same batch of chunks:

- External API: chunk A ranked 1st
- Internal deployment: chunk A ranked last

The difference is baffling.

### Possible Causes

Where does the problem lie? There's no definitive answer yet, but several possibilities exist:

**1. Model Version Differences**

The official API may use the latest version, while the downloaded open-source weights may be an earlier release. Same version number doesn't mean identical—the official version may have additional fine-tuning or optimization.

**2. Deployment Framework Differences**

Different inference frameworks may have subtle differences in implementing the same model. Tokenizer handling, padding strategies, batch processing logic—these details all affect final output.

**3. Different Parameter Defaults**

External API parameter defaults may differ from internal deployment framework defaults. Temperature, top_p, and other parameters, even with small differences, can produce significant variations in ranking-sensitive tasks like Rerank.

**4. Official Has Extra Optimizations**

The official API may have distillation, quantization tuning, or other optimizations not reflected in open-source weights.

**5. Floating Point Precision Issues**

Different frameworks may have slightly different floating-point calculation precision, which in extreme cases can lead to different results.

### Other Common Differences

**The "Default Trap" of Parameter Settings**

When calling external APIs, many parameters have default values you might never notice. Take temperature:

- External API defaults to 0.7
- Internal deployment framework defaults to 1.0

Seems like just 0.3 difference? In RAG scenarios, this can dramatically reduce answer stability.

**The "Performance Discount" of Deployment Methods**

The same model, different deployment methods, can yield vastly different results:

| Deployment Method | Inference Speed | Result Stability | Memory Usage | Recommended Scenario |
|-------------------|-----------------|------------------|--------------|----------------------|
| Native Deployment | Slow | High | High | Performance-first, ample resources |
| Optimized Framework | Fast | Medium | Medium | Balanced, most scenarios |
| Quantized | Very Fast | Low | Low | Resource-constrained, acceptable trade-off |

Quantized models save memory and run fast, but performance takes a hit. Especially for sensitive information like numbers and dates, quantized models are more error-prone.

**The "Hidden Differences" in Databases**

Vector databases have pitfalls too:

- **Different index types**: Managed services might default to HNSW; self-hosted might choose IVF_PQ, losing a few percentage points in recall
- **Different distance metrics**: Cosine similarity vs Euclidean distance changes result ranking
- **Different sharding strategies**: With large data volumes, sharding methods affect retrieval quality

### How to Handle It?

**1. Establish Baseline Testing**

In the external environment, run a batch of test data using the official API and record results. After internal deployment, run the same data and compare differences.

**2. Document Parameter Configurations**

Record all parameter configurations from external APIs, including defaults. Cross-check each item when deploying internally.

**3. Align Versions**

When downloading models, confirm version numbers, try to match the official API.

**4. Compare Multiple Frameworks**

Deploy the same model using different frameworks and run comparative tests. Choose the solution with results closest to the official API.

**5. Accept the "Performance Discount"**

Some differences may be impossible to eliminate. At this point, communicate with clients: on-premise deployment may have slightly lower performance than cloud APIs, but in return, you get data security and compliance. This is a trade-off, not a bug.

## Common Issues and Solutions

### Dependency Version Conflicts

Symptoms: Errors in intranet environment, external environment works fine.

Cause: Intranet environment has different Python versions and system library versions.

Solution: Dockerize everything, encapsulate the entire runtime environment to ensure consistency.

### Memory Fragmentation

Symptoms: Out of memory after running for a while.

Cause: Inference framework's KV Cache fragmentation.

Solution: Periodically restart services, or enable prefix caching mechanism.

### Network Timeouts

Symptoms: Occasional service timeouts, but no obvious errors in logs.

Cause: Intranet firewall disconnects long-idle connections.

Solution: Configure heartbeat keep-alive, or shorten timeout values.

### Log Explosion

Symptoms: Disk space filled by logs.

Cause: Inference framework's default log level is too verbose; log volume is huge with many requests.

Solution: Adjust log level, configure log rotation.

```bash
# /etc/logrotate.d/inference-service
/var/log/inference/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    copytruncate
}
```

## When to Choose On-Premise Deployment?

Not all scenarios require on-premise deployment.

**Scenarios requiring on-premise:**
- Heavily regulated industries like finance, government, healthcare
- Sensitive data that cannot leave the intranet
- Compliance and audit requirements
- Long-term use, cost-sensitive
- Need data trail, traceability

**Scenarios not requiring on-premise:**
- Startup phase, rapid validation
- Non-sensitive data
- Limited budget, insufficient operational capability
- Short-term projects, not worth the investment

**Core principle: Good enough is enough, avoid over-engineering.**

On-premise deployment is not the goal, but a means. If cloud solutions can meet requirements, there's no need to choose on-premise just to "look more secure"—because the operational costs and complexity that on-premise brings are often underestimated.

## Pre-Launch Checklist

Before going live with on-premise deployment, verify the following:

- [ ] All dependencies packaged offline and verified
- [ ] Model version aligned with external API
- [ ] Parameter defaults cross-checked item by item
- [ ] Baseline testing completed and results documented
- [ ] All data flows closed within intranet
- [ ] Log desensitization configured
- [ ] Rollback mechanism verified
- [ ] Heartbeat keep-alive configured

---

**Series Navigation**:
- Previous: [On-Premise Deployment (Part 1): Who Needs It? How to Choose?]({{ site.baseurl }}{% post_url 2026-04-18-on-premise-deployment-part1-en %})
- This: On-Premise Deployment (Part 2): Pitfalls in Practice

---

*This article is based on actual project experience and has been anonymized. Feel free to share with attribution.*
