# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Helm chart (`poc-template-chart`) that serves as a parent chart for deploying OpenShift AI (Red Hat OpenShift Data Science), GPU operator, and Node Feature Discovery components. It orchestrates the deployment of multiple operators and dependencies on OpenShift clusters.

## Common Commands

### Helm Operations
- `helm dependency update` - Update chart dependencies (downloads NFD and GPU operator charts)
- `helm lint .` - Validate chart syntax and structure
- `helm template .` - Generate Kubernetes manifests without installing
- `helm install poc-template .` - Install the chart
- `helm upgrade poc-template .` - Upgrade an existing installation
- `helm uninstall poc-template` - Remove the installation

### Development and Testing
- `helm dependency build` - Build dependency archive files
- `helm template . --debug` - Generate manifests with debug output
- `helm install --dry-run --debug poc-template .` - Simulate installation with debug output

## Architecture

### Chart Structure
- **Parent Chart**: Manages OpenShift AI deployment with GPU and NFD support
- **Dependencies**: Automatically pulls in NVIDIA GPU Operator (v23.9.1) and Node Feature Discovery (v0.14.0) as sub-charts
- **Operator Subscriptions**: Uses pre-install hooks to set up required operators before main deployment

### Key Components
1. **Pre-install Hooks**: Templates in `templates/operators-subscriptions/` create operator subscriptions with specific hook weights:
   - OpenShift AI operator (`hook-weight: "1-3"`)
   - Authorino operator (`hook-weight: "30"`)
   - Service mesh and serverless operators

2. **CRDs**: Custom resource definitions for DataScienceClusters and AcceleratorProfiles
3. **Sub-charts**: GPU operator and NFD charts are managed as dependencies

### Hook Execution Order
The chart uses Helm hooks with weights to ensure proper operator installation sequence:
1. Namespaces and operator groups
2. OpenShift AI operator subscription  
3. Supporting operators (authorino, service mesh, serverless)

## Important Notes
- This chart is designed for OpenShift clusters specifically
- GPU operator installation requires appropriate node selectors and tolerations
- The `values.yaml` file is currently empty - all configuration uses chart defaults
- All operator subscriptions use `installPlanApproval: Automatic`