AutoResilience — Project Overview

1. Project Summary

AutoResilience is an automated Chaos Engineering platform designed to evaluate the resilience of Kubernetes-based applications.

Instead of only injecting failures, the platform manages the complete experiment lifecycle:

Baseline
   ↓
Safety Validation
   ↓
Controlled Failure
   ↓
Monitoring
   ↓
Recovery Analysis
   ↓
Resilience Score
   ↓
Report and Recommendation

The goal is to make resilience testing repeatable, measurable, understandable, and easier to use through a product-oriented web interface.

2. Problem

Modern applications increasingly use microservices, containers, and Kubernetes.

These systems are designed for availability, but they can still fail because of:

pod crashes

CPU or memory pressure

network latency

packet loss

service dependency failures

infrastructure problems

incorrect retry, timeout, replica, or resource configuration

Traditional testing mainly checks whether software behaves correctly under expected conditions. It usually does not answer:

What happens when part of the system fails?

Chaos Engineering addresses this by deliberately introducing controlled faults so weaknesses can be discovered before a real incident occurs.

Existing chaos tools are powerful, but users may still need to manually configure experiments, monitor several systems, analyze results, calculate impact, and prepare reports.

AutoResilience is intended to automate and unify that workflow.

3. Product Goal

The product should allow a user to:

choose a Kubernetes target

select or configure a fault

validate the experiment before execution

control the blast radius

capture the target's normal baseline

inject the fault through LitmusChaos

observe the system during the experiment

identify degradation and recovery

calculate an explainable Resilience Score

compare the result with previous experiments

generate a report and actionable findings

optionally use the result as a CI/CD reliability gate

The frontend should therefore behave as a reliability testing workbench, not simply as a graphical wrapper around LitmusChaos or Grafana.

4. Proposed Architecture

User
  ↓
React / Next.js Product Interface
  ↓
FastAPI Orchestration API
  ↓
Experiment Manager
  ├── Safety Validator
  ├── Baseline Collector
  ├── Experiment State Manager
  └── Recovery Analyzer
  ↓
LitmusChaos
  ↓
Kubernetes Target Application
  ↓
Prometheus / Alertmanager
  ↓
Metrics Analysis
  ↓
Resilience Scoring Engine
  ↓
PostgreSQL
  ↓
Reports / History / Product Dashboard

GitHub Actions will later provide CI/CD integration.

5. Initial Technology Stack

Layer

Technology

Frontend

React / Next.js + TypeScript

Backend

Python + FastAPI

Chaos Engine

LitmusChaos

Container Platform

Kubernetes

Containers

Docker

Metrics

Prometheus

Alerting

Alertmanager

Visualization

AutoResilience UI + optional Grafana deep views

Database

PostgreSQL

CI/CD

GitHub Actions

Local Cluster

kind / local Kubernetes environment

Dependency Management

uv for Python, npm for frontend

The project intentionally relies on proven open-source infrastructure tools instead of rebuilding chaos injection or monitoring from scratch.

The original contribution is the orchestration, safety layer, scoring model, product experience, analysis, reporting, and experiment workflow built around those tools.

6. MVP Scope

The first release targets a single Kubernetes cluster.

Planned experiment categories include:

pod failure

CPU stress

memory stress

network latency

packet loss

The exact supported experiment set may be reduced if necessary to ensure the implemented scenarios are reliable and well evaluated.

The project should prefer three well-tested experiment types over five incomplete implementations.

7. Experiment Lifecycle

Every experiment should move through explicit states.

A preliminary lifecycle is:

CREATED
   ↓
VALIDATING
   ↓
BASELINING
   ↓
INJECTING
   ↓
OBSERVING
   ↓
RECOVERING
   ↓
COMPLETED

Possible failure states include:

VALIDATION_FAILED
INJECTION_FAILED
ABORTED
UNKNOWN

An UNKNOWN state is important because network or platform failures must not cause AutoResilience to report a false successful recovery.

8. Safety Model

Chaos Engineering intentionally causes failures, so experiment safety is a first-class product feature.

Planned checks include:

allowed namespace

valid target workload

healthy target before execution

maximum affected replicas

maximum experiment duration

maximum blast radius

monitoring availability

chaos-engine availability

conflicting experiment detection

automatic experiment termination

clear experiment logging

The platform should block invalid or unsafe experiments before they reach LitmusChaos.

9. Observability Model

AutoResilience compares three phases:

BASELINE → FAILURE → RECOVERY

Primary measurements include:

availability

latency

error rate

CPU usage

memory usage

pod status

detection time

recovery time

alert behavior

The product UI should show only the metrics that are useful for understanding the active experiment.

Grafana may remain available for deeper exploration, but the main product interface should present experiment-specific evidence rather than acting as an embedded Grafana dashboard.

10. Resilience Score

The Resilience Score is one of the project's most important components.

Instead of showing only:

Resilience Score: 73

the platform should explain the result, for example:

RESILIENCE SCORE                73 / 100

Availability                   27 / 30
Recovery                       18 / 30
Latency degradation            13 / 20
Error behaviour                11 / 15
Alerting                        4 / 5

The final weighting model still needs to be formally defined and validated.

The scoring model must be:

deterministic

documented

reproducible

comparable where appropriate

defensible during evaluation

traceable back to observed experiment data

The scoring engine should never depend on an LLM.

11. Product Interface Direction

The main frontend areas are expected to include:

Overview

High-level resilience status, recent experiments, service risk, and trend information.

Experiment Builder

A guided flow for:

Target → Fault → Safety → Baseline → Review

Live Experiment Room

The most important operational screen.

It should combine:

experiment state

live metrics

baseline/failure/recovery markers

Kubernetes and chaos events

alert events

recovery detection

abort controls

provisional experiment result

Experiment History

Search, filter, inspect, and compare previous experiments.

Explainable Score

Allow the user to understand exactly why a score was awarded.

Service Risk View

Show which services or fault categories have produced the weakest measured resilience.

Reports

Summarize the experiment, evidence, weaknesses, score, and recommendations.

12. AI Usage

AI is optional and should not be added until the deterministic platform is stable.

If implemented, the two most useful AI components are:

Resilience Analyst

Receives already-computed structured metrics and produces a human-readable explanation of observed weaknesses.

Experiment Copilot

Converts natural-language experiment intent into a structured proposed configuration.

The AI must not directly:

execute chaos experiments

bypass safety validation

calculate the official Resilience Score

mark an experiment successful

modify production infrastructure automatically

13. Initial Development Strategy

The team should not begin by implementing all fault types.

The first milestone is:

Local Kubernetes Cluster
        ↓
Target Microservices
        ↓
Prometheus
        ↓
LitmusChaos
        ↓
One Pod Failure
        ↓
Observed Degradation
        ↓
Observed Recovery

Once this flow is reproducible and understood by the team, the FastAPI orchestration layer can begin automating it.

This reduces the risk of building a large application around infrastructure that has not yet been proven to work reliably.

14. Current Status

Phase: Environment setup and architecture scaffolding

Completed so far:

source repository created

documentation repository created

frontend scaffold created

FastAPI backend scaffold created

Python dependencies managed with uv

first backend health endpoint running

initial documentation structure created

Next technical milestone:

Run and understand the first complete Kubernetes chaos experiment manually before automating the workflow.

15. Project Principle

AutoResilience should not become:

React → LitmusChaos → Grafana

The intended product is:

Target
  ↓
Safety
  ↓
Baseline
  ↓
Controlled Chaos
  ↓
Evidence
  ↓
Recovery Analysis
  ↓
Explainable Score
  ↓
Comparison
  ↓
Actionable Result

That distinction is what turns the project from a collection of integrated tools into a coherent resilience-testing platform.