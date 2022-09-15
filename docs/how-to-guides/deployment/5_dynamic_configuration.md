# How to inject dynamic configuration

Parts 1) through 3) of this guide focused on the bare minimum of deploying a static application on our infrastructure.
Real word applications are not always serving static files though and might need some configuration that is not baked into the application container but only provided at runtime.
This guide walks through how we do that.

!!! important "Goal"

    Provide a environment variable to the containerized application via Kubernetes

!!! important "Prerequisites"

    You should already have a basic deployment set up.
    This means that you should have:
    
    - An application repository with CI/CD set up
    - A *-deploy* repository


## 1)
