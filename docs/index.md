# demotest

Node.js golden-path service with Jenkins, Helm, and Sonar

## Pipeline

Jenkinsfile stages: **Install → Lint → Unit tests → SonarQube → Helm lint**.

Point a Jenkins job named `demo/demotest` at this repo. Set credential `SONAR_TOKEN` to run the scanner against `http://sonarqube.sonarqube.svc:9000`.

## Deploy

North-south is **OpenShift Route → Gateway API Gateway → HTTPRoute → app** (the Route targets `istio-ingressgateway`, not this Service). No VirtualService. AuthZ in this chart is **deny-all** plus ALLOW from the ingress gateway. **STRICT mTLS** is namespace-wide (`PeerAuthentication`), created by Kyverno when the namespace is enrolled — not a per-service object.

```bash
oc label namespace demo-dev acme.io/mesh-enroll=true istio.io/dataplane-mode=ambient --overwrite
helm upgrade --install demotest chart -n demo-dev
```

Ingress: `https://demotest-demo-dev.apps.rosa.rosa-rwx5g.f8y9.p3.openshiftapps.com`

The chart labels pods `backstage.io/kubernetes-id=demotest` for Topology.

## Dev Spaces

https://devspaces.apps.rosa.rosa-rwx5g.f8y9.p3.openshiftapps.com#https://github.com/rmallam-rhdh/demotest?new&devfilePath=devfile.yaml
