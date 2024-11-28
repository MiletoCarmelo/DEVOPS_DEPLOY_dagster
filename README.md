# Guide de Création d'une Pipeline Dagster

## 1. Création du Repo Code (dagster_ma_pipeline)
```
dagster_ma_pipeline/
├── Dockerfile
├── requirements.txt
├── pipeline_code/
│   ├── __init__.py
│   └── repository.py      # Code de votre pipeline
```

### Fichiers Clés
- **Dockerfile** : Build de l'image avec serveur gRPC
- **requirements.txt** : Dépendances minimales (dagster + vos besoins)
- **repository.py** : Définition de vos assets/jobs

### GitHub Actions
- Configure multi-arch build (AMD64/ARM64)
- Pousse l'image vers GHCR

## 2. Création du Repo Déploiement (DEVOPS_DEPLOY_DAGSTER_PIPELINE_ma_pipeline)
```
DEVOPS_DEPLOY_DAGSTER_PIPELINE_ma_pipeline/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml    # Déploie le serveur gRPC
    └── service.yaml       # Expose le port 4000
```

## 3. Configuration Dagster Principal (DEVOPS_DEPLOY_dagster)
Mettre à jour workspace.yaml :
```yaml
load_from:
  - grpc_server:
      host: pipeline-ma-pipeline
      port: 4000
      location_name: "pipeline-ma-pipeline"
```

## 4. Configuration ArgoCD
Ajouter dans votre configuration ArgoCD :
```yaml
  - name: dagster-pipeline-ma-pipeline
    namespace: dagster
    project: quant-cm
    source:
      repoURL: https://github.com/MiletoCarmelo/DEVOPS_DEPLOY_DAGSTER_PIPELINE_ma_pipeline.git
      path: .
      helm:
        valueFiles: values.yaml
      targetRevision: main
```

## Ordre des Opérations
1. Créer et pousser le repo code
2. Attendre que l'image soit disponible sur GHCR
3. Créer et pousser le repo déploiement
4. Mettre à jour workspace.yaml dans DEVOPS_DEPLOY_dagster
5. Mettre à jour la config ArgoCD
6. Vérifier le déploiement dans l'UI Dagster

## Ports et Services
- Pipeline gRPC : Port 4000
- Dagster UI : Port 3000

## Points à Vérifier
- [ ] Image multi-arch sur GHCR
- [ ] Configuration PostgreSQL cohérente
- [ ] Workspace.yaml à jour
- [ ] Configuration ArgoCD correcte
- [ ] Pipeline visible dans l'UI Dagster
