## pseudo for trigger CI/CD deployment

1. Setup variables
    - Store secrets in Gitlab Variables:
        - ENV_FILE
        - DB_CONNECTION
        - AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_ACCOUNT_ID, AWS_REGION
        - EKS_CLUSTER_NAME

2. Trigger pipeline
    - IF code is pushed to `main` or tagged `release/*`, THEN start pipeline
    - IF code is pushed to `feature/*`, THEN run tests only

3. Define stages
    - `stages`:
        - test
        - build
        - deploy-staging
        - deploy-production

4. Run Test (stage: `test`)
    - EXECUTE:
        - Unit tests
        - Integration tests
        - Security scans
    - IF tests fail, STOP pipeline

5. Build Application  (stage: `build`)
    - IF branch is `main` or tag `release/*`
    - Before build
        - Retrieve secrets from Gitlab Variables
        - Add/Replace env. file
        - Login to registry
    - Build application image
    - Push image to registry
    - IF build fails, STOP pipeline

6. Deploy to Staging  (stage: `deploy-staging`)
    - IF branch is `main` or tag `release/*`
    - Before deploy
        - Retrive secrets from Gitlab Variables
        - Connect to cluster
    - Pull image
    - Deploy to staging environment

7. Deploy to Production  (stage: `deploy-production`)
    - IF tag `release/*` and yes on manual approval
    - Before deploy
        - Retrive secrets from Gitlab Variables
        - Connect to cluster
        - Get previous version tag
    - Pull image
    - Deploy to production environment
    - Monitor deployment
    - IF errors, ROLLBACK to previous version

8. END