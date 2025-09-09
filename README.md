# IDP Stack

This repository contains the infrastructure code for deploying an Identity Provider (IDP) stack to any one of three public cloud providers (GCP, AWS, Azure) or locally using Kind Kubernetes cluster. Kind is the default. The stack is experimental. Hence, it was built to be deployed as functional scripts using Taskfile to orchestrate the implementation.

## Layout

The repository is structured as follows:

```
.
├── README.md
├── examples
│   ├── README.md
│   ├── advanced
│   └── workflows
├── idp-builder
│   ├── Taskfile.yaml
│   ├── configs
│   ├── docs
│   ├── examples
│   ├── scripts
│   └── taskfiles
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
