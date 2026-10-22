# reelops-gitops

Desired state of the `reelops-dev` EKS cluster. Argo CD watches this repo; nobody runs `kubectl apply` by hand.

| Path | What it holds |
|---|---|
| `bootstrap/root.yaml` | App-of-apps. Applied once by `make argocd` from the reelops repo. |
| `apps/` | One Argo CD Application per component. Number prefix = sync order (sync-wave). |
| `envs/<env>/values.yaml` | Per-environment Helm overrides for the reelops chart. CI updates `image.tag` in staging. |
| `platform/` | Secret store, synced secrets, SLO alert rules, Grafana dashboard, CloudWatch exporter. |

**Deploy to staging:** merge to `main` in the reelops repo. CI pushes the image to ECR and bumps `envs/staging/values.yaml` here.
**Promote to prod:** PR that copies the tag into `envs/prod/values.yaml`, merge, then sync `reelops-prod` in Argo CD.
**Roll back:** `git revert` the commit that changed the tag.
