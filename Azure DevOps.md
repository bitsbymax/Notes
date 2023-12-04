# Azure DevOps

--Software as a service (SaaS) platform from Microsoft
--Platform to implement all DevOps processes
DevOps is a combination of Concepts, Tools, Practices or anything that makes developing and releasing applications Fast, Automated and High-quality

Azure DevOps is a technological implementation of DevOps process
Software development licecycle: DEV[plan, code, build, test] - OPS[release, deploy, operate, monitor]

Boards - plan
Repos - code
Artifacts - build
Test Plans - test
Pipelines - deploy

(CI) Continious Integration[Test -> Build image(Docker), Push Artifacts to Repo] -> (CD) Continious Delivery/Deployment[Deploy(Development, Testing, Production)]

Stages in Azure Pipelines

- A stage is a logical boundary in the pipeline, for example Build stage or Deployment stage
- Each stage contains 1 or more jobs
- By default they run one after the other
