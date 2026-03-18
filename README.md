<h1 align="center">GitOps for Spring Boot API service</h1>

The following repository contains ArgoCD application definitions along with environment-based values for Spring Boot API deployments

## Deployment

This section describes how to deploy fresh stack.
<br>Spring Boot API service is defined using Application Set with List Generator and `CreateNamespace=true`

### Prerequisites

1. Make sure you setup can reach public GitHub repos
2. Set `ingress.className` in **\*\*/(dev|prd)/values.yaml** to match your ingress controller's name (currently set to `nginx`)
3. ArgoCD has to have `dev-global-cluster-0` and `prd-global-cluster-5` clusters registered

Having that set, run
```bash
kubectl apply -f https://raw.githubusercontent.com/Company-Engineering/spring-boot-api-gitops/main/argocd/root-application.yaml
```

This setup is self-managed and you just have to deploy "root" project, which in turn will deploy two applications based on [spring-boot-api-chart](https://github.com/Company-Engineering/spring-boot-api-chart):
- `dev-spring-boot-api` on **dev-global-cluster-0**
- `dev-spring-boot-api` on **prd-global-cluster-5**


## Releasing 'Spring Boot API' service

Current setup supports two environments: **dev** and **prod**.
<br>There are two repositories involved
- [spring-boot-api-chart](https://github.com/Company-Engineering/spring-boot-api-chart) (contains helm chart)
- [spring-boot-api-gitops](https://github.com/Company-Engineering/spring-boot-api-gitops) (tells which version of chart is used)

**NOTE:** If you don't have GitHub webhooks configured, then you have to **MANUALLY** refresh the app on your ArgoCD after pushing changes to remote

### Releasing on DEV

`DEV` listens to `main` branch of [spring-boot-api-chart](https://github.com/Company-Engineering/spring-boot-api-chart), so every push to main branch results in deployment to dev.

### Releasing on PROD

To make a `PROD` release, you first have to 
1. create a tag (make a release) in [spring-boot-api-chart](https://github.com/Company-Engineering/spring-boot-api-chart)
2. update `chartRevision` (for **prd** env) in '**/aplicationsets/spring-boot-api.yaml' in [spring-boot-api-gitops](https://github.com/Company-Engineering/spring-boot-api-gitops) and merge changes to **main**
 