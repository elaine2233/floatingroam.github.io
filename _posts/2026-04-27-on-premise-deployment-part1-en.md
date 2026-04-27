---
layout: post
title: "On-Premise Deployment (Part 1): Who Needs It? How to Choose?"
date: 2026-04-27
author: floating roam
categories: [Technology, Deployment]
tags: [On-Premise, Intranet, Model Selection]
excerpt: "When the client says 'data cannot leave the intranet,' how do you deploy your AI system? From model selection and inference frameworks to vector databases—a practical guide to on-premise deployment choices."
version: 2
---

# On-Premise Deployment (Part 1): Who Needs It? How to Choose?

"Data cannot leave the intranet."

This is the first requirement from certain clients—and the most non-negotiable one. No room for discussion.

For these clients, their data cannot leave the intranet—not even a single bit. This means: **every component must be deployed on-premise**. Models, vector databases, application services—all running within the intranet environment.

## Who Needs On-Premise Deployment?

Not all clients need on-premise deployment. What types of clients have this requirement?

### Heavily Regulated Industries

Finance, government, healthcare, defense—these industries have hard requirements for data security. Regulations clearly state: sensitive data cannot cross borders, cannot be stored on third-party servers.

This isn't a question of whether the client "wants to" or not—it's whether they "can." Without compliance, business cannot operate.

### Data-Sensitive Enterprises

Even without regulatory requirements, some companies' data is their core competitive advantage. Intellectual property, trade secrets, customer information—once leaked, the losses are immeasurable.

For these companies, data security is the bottom line. Use cloud APIs? Don't even think about it.

### Organizations with Compliance and Audit Requirements

Some organizations need to pass ISO 27001, SOC 2, or other certifications, or meet internal audit requirements. Data trail, traceable operations, documented security—these are hard indicators for audits.

On-premise deployment keeps all data processing within controllable boundaries, meeting compliance and audit requirements.

### An Interesting Phenomenon

Some clients need on-premise deployment but are reluctant to use truly open-source models from smaller companies. The reason: no maintenance support, no one to turn to when problems arise.

Instead, they're more accepting of non-open-source, paid models that can be deployed within their intranet. Because there's a vendor to contact, contracts to enforce, service guarantees.

This sounds contradictory—since it's on-premise deployment anyway, why pay for models? But for clients, **accountability** matters more than **free**.

## Why Is On-Premise Deployment Difficult?

Cloud deployment and on-premise deployment may seem like just "different locations," but they represent completely different technology stacks.

### Cloud Conveniences, Intranet Doesn't Have

In the cloud, many things are "out of the box":

- **Models?** Call APIs, no need to worry about underlying infrastructure
- **Vector databases?** SaaS services, pay as you go
- **Scaling?** A few clicks, auto-scaling
- **Updates?** Vendors handle it for you

Move to an intranet, and all these conveniences disappear:

- **Models**: Need to select, download, deploy, and optimize yourself
- **Vector databases**: Need to set up, maintain, and backup yourself
- **Hardware**: GPU resources are limited, need careful planning
- **Updates**: Intranet environment makes every update an "expedition"
- **Dependencies**: Many open-source libraries need online downloads—what about intranet environments?

### The Parallel Dilemma in Intranets

This is an easily overlooked problem.

Some clients have sufficient resources to deploy large models like Qwen3-235B or even DeepSeek. But parallelism is a disaster.

Externally, inference services can handle high concurrency—request queuing, batch processing, GPU utilization maxed out. But internally, network architecture, firewall policies, and load balancing configurations can all become bottlenecks. Turn up parallelism a bit, and the service crashes.

This isn't a model problem—it's an infrastructure problem. But clients often don't realize this, only feeling that "the model deployment has issues."

### The Compounding Effect of Access Controls

Clients needing on-premise deployment often have strict access controls in their intranet environments:

- Developers cannot directly log into servers; they need to go through bastion hosts
- Code cannot be directly uploaded; it needs to go through approval processes
- Logs cannot be freely accessed; permissions need to be requested

These constraints compound, making on-premise deployment exponentially more difficult.

## Model Selection: Open-Source vs Commercial

On-premise deployment first requires solving the model problem.

### Open-Source Model Choices

Currently mainstream open-source models:

| Model | Characteristics |
|-------|-----------------|
| Qwen Series | Strong Chinese capability, multiple parameter sizes, high domestic acceptance |
| GLM-4 / GLM-5 | Zhipu AI, excellent Chinese capability, actively updated |
| Gemma | Google open-source, lightweight, suitable for resource-constrained scenarios |
| DeepSeek | Domestic large model, strong reasoning capability, deployable on-premise |

While the Llama series has an active community, its Chinese capability is average, with low acceptance among Chinese enterprise clients. Early models like ChatGLM and Baichuan have gradually been replaced by newer, stronger models.

### Resource vs Model Trade-offs

Different clients have vastly different resources:

- **Resource-constrained clients**: Can only deploy small models like 7B, 14B, need careful planning
- **Resource-abundant clients**: Can deploy 72B, 235B, or even larger models, pursuing performance

This isn't simply a "bigger is better" question. Large models need more memory, longer inference time, more complex deployment solutions. Clients need to find a balance between performance, cost, and latency.

### Model Sources: Not Just HuggingFace

Open-source models are typically hosted on HuggingFace, but domestic access is unstable, and intranets cannot access it at all.

Fortunately, there are domestic alternatives:

- **ModelScope**: Alibaba Cloud's model platform, many open-source models have mirrors
- **WiseModel**: Domestic model hosting platform
- **Vendor Websites**: Some model vendors provide direct downloads

Of course, HuggingFace models can also be downloaded externally and packaged for transfer to intranets, just with more cumbersome processes.

## Inference Frameworks: Each Has Pros and Cons

Model selected, next comes the inference framework. Different frameworks have different characteristics:

### vLLM

**Pros:**
- PagedAttention technology, high throughput
- Supports continuous batching, high GPU utilization
- Active community, comprehensive documentation

**Cons:**
- Support for some models may lag
- Memory management needs tuning
- Limited quantization support

### TGI (Text Generation Inference)

**Pros:**
- HuggingFace official, good model compatibility
- Feature-rich, supports multiple decoding strategies
- Relatively simple deployment

**Cons:**
- Throughput lower than vLLM
- Higher resource usage

### llama.cpp

**Pros:**
- Extremely lightweight, can run on CPU
- Good quantization support, low memory usage
- Simple deployment, few dependencies

**Cons:**
- Low throughput, not suitable for high concurrency
- Limited features

### TensorRT-LLM

**Pros:**
- NVIDIA official, extreme performance
- Supports multiple optimization techniques

**Cons:**
- Steep learning curve
- Requires NVIDIA technical support
- Has requirements for model format

### How to Choose?

There's no best framework, only the most suitable one. Consider:

- **Concurrency needs**: High concurrency choose vLLM or TensorRT-LLM
- **Resource constraints**: Limited resources choose llama.cpp or quantization solutions
- **Model compatibility**: For stability choose TGI
- **Team capability**: With NVIDIA support choose TensorRT-LLM, otherwise vLLM

## Vector Databases: On-Premise Solutions

RAG systems cannot function without vector databases. Cloud solutions like Pinecone or Zilliz Cloud are available, but intranets require self-hosting.

Mainstream choices:

| Database | Characteristics |
|----------|-----------------|
| Milvus | Distributed, feature-rich, many domestic users |
| Qdrant | Rust-based, lightweight, easy to use |
| Chroma | Embedded, simple, suitable for small scale |
| Weaviate | Hybrid search, feature-rich |

Consider data scale, performance needs, and operational capability when choosing.

---

Selection is just the first step. The next post will cover the pitfalls in actual deployment: network isolation, security compliance, model updates, performance gaps—these are the real headaches.

---

**Series Navigation**:
- Previous: [Domain Fine-Tuning: From General LLMs to Domain Experts]({{ site.baseurl }}{% post_url 2026-04-16-domain-fine-tuning-en %})
- This: On-Premise Deployment (Part 1): Who Needs It? How to Choose?


---

*This article is based on actual project experience and has been anonymized. Feel free to share with attribution.*
