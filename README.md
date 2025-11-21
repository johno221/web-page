# HTML GitOps Demo  
_HTML page → GitHub Actions → Docker → Helm → ArgoCD → Kubernetes_

Tento projekt demonštruje moderný GitOps/CI-CD flow pre jednoduchú HTML aplikáciu:

- zdroják: statická HTML stránka
- build & publish: **GitHub Actions** → **Docker image** v GitHub Container Registry
- deploy: **Helm chart** verzovaný v samostatnom GitOps repozitári
- GitOps engine: **ArgoCD**
- runtime: **Kubernetes cluster**

---

## Repozitáre

- **Aplikačné repo** – HTML app + Docker + GitHub Actions  
  Príklad: `html-app`
  - `index.html` – statická stránka
  - `Dockerfile` – Nginx kontajner so stránkou
  - `.github/workflows/build-and-publish.yml` – CI pipeline, ktorá:
    - buildne Docker image
    - pushne image do `ghcr.io/<user>/<repo>/html-app:<sha>`

- **GitOps repo** – Helm chart + ArgoCD  
  Príklad: `html-gitops`
  - `html-chart/Chart.yaml` – definícia Helm chartu
  - `html-chart/values.yaml` – konfigurácia (image repository, tag, repliky)
  - `html-chart/templates/*.yaml` – `Deployment`, `Service`, prípadne `Ingress`
  - (voliteľne) `argocd-application.yaml` – ArgoCD `Application` objekt

---

## Architektúra (high-level Flow)

```text
Developer → push do GitHubu (html-app)
        ↓
GitHub Actions:
  - build Docker image
  - push image do GHCR
  - (voliteľne) update image tag v GitOps repo (values.yaml)
        ↓
GitOps repo (html-gitops, Helm chart)
        ↓
ArgoCD:
  - sleduje GitOps repo
  - diff: Git (želanie) vs. cluster (realita

