# IDP Stack

This repository contains the infrastructure code for deploying an Internal Developer Platform (IDP) to any one of three public cloud providers (GCP, AWS, Azure) or locally using Kind Kubernetes cluster. Kind is the default. The stack is experimental. Hence, it was written for experimenting, and built as functional scripts using Taskfile to orchestrate the implementation workflows.

## Layout

The repository is structured as follows:

```
.
├── README.md
├── examples
│   ├── README.md
│   ├── advanced
│   └── workflows
├── idp-builder
│   ├── README.md
│   ├── Taskfile.yaml
│   ├── configs
│   ├── docs
│   ├── examples
│   ├── scripts
│   └── taskfiles
└── idp-usage
    ├── README.md
    ├── Taskfile.yaml
    ├── apps
    ├── docs
    ├── examples
    ├── taskfiles
    └── templates
```

- `examples/`: Contains example configurations and workflows for deploying the IDP stack.
- `idp-builder/`: Contains scripts and configurations for building the IDP stack.
- `idp-usage/`: Contains scripts and configurations for using the deployed IDP stack

Install `Task` CLI from https://taskfile.dev/#/installation and start with:

```bash
cd idp-builder
task or task help
```

This project is a work in progress. Please refer to the [examples/README.md](examples/README.md) for usage patterns, learning paths, and contribution guidelines.

