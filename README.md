# app-of-apps-example

### How to restore the health assessment of argoproj.io/Application CRD

# Note:
The health assessment of argoproj.io/Application CRD has been removed in argocd 1.8 (see #3781 for more information). You might need to restore it if you are using app-of-apps pattern and orchestrating synchronization using sync waves.
https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#argocd-app

1 In OpenShift GitOps **argocd-cm** ConfigMap is managed by ArgoCD object named **openshift-gitops**.
1.1 Run the following command to edit the object
'''
$: oc -n openshift-gitops edit  argocd openshift-gitops

'''
1.2 Add the following lines into the ArgocCD object based on the info from the Note above:
'''
...
spec:
**extraConfig:**
    resource.customizations: |
      argoproj.io/Application:
        health.lua: |
          hs = {}
          hs.status = "Progressing"
          hs.message = ""
          if obj.status ~= nil then
            if obj.status.health ~= nil then
              hs.status = obj.status.health.status
              if obj.status.health.message ~= nil then
                hs.message = obj.status.health.message
              end
            end
          end
          return hs
...
'''
### How to test the behavior
1 Run the following command:
'''
$: oc apply -f app-of-apps.yaml
'''
2 Expected:
  2.1 

