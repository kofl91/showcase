# Showcase

Hi, I am Kim Oliver Fehrs. My passion is coding and in recent years I focus on infrastructure. This repository will showcase typical platforms that I have seen through my career and a few tricks on how to improve upon them. Let's dive right in


## Container Plattforms


## Application deployment

### ArgoCD with Helm

ArgoCD is an OpenSource Project that is helping deploying to Kubernetes with a GitOps Mindset. The idea is simple, an application on the Kubernetes Cluster, checks out your git repository and compares the manifests in there with the ones on your Kubernetes Cluster. ArgoCD can then notify you of changed or failed applications and (if you let it) update the cluster to reflect your git repository. Additionally it can provide metrics and badges for your repository that represent and applications health on a higher abstraction level than pods or containers. The UI is another very helpful aspect of ArgoCD. By letting you see exactly what resources are created (or deleted) and with which other resources they interact, it adds a certain level of trust to a tool that takes over your entire deployment process. Its quite impresive to see ArgoCD visualize how an Ingress Resource forces the creation of a certificate resource that in turn creates a secret which is also mounted by the application itself (not saying thats good pattern). 

However it might not be that easy, to restructure your repositories to fit in with the ideas that ArgoCD has. A common problem that will see a solution in the next Version 2.5, is that Helm and ArgoCD do not have an ideal structure to work with one another. 

Helm builds on the idea that you can seperate interesting configuration values from the actual (sometimes overblown) kubernetes manifests by templating them. The next step is to seperate the template from the values to simplify the deployment of a complex construct like a kafka cluster or an entire logging stack to a single file of configurations. No matter the effort, values.yaml files will always have a certain size to themselves. Image repositories, pullsecrets, credential-secrets, additional labels or volumes and entire application config files need to be stored somewhere. While the charts want to be immutable and stored ideally in a chartmuseum or docker registry, the values want to be changeable and adapt to new knowledge. 

ArgoCD applications allow to specify exactly one git repository. The expectation is, that this repository holds either plain kubernetes manifests, kustomize resoruces or a helm chart. We usually do not want to store our helm chart in git, because it is supposed to be immutable after being developed and versioned. However this is the only way for us to specify to deploy that particular helm chart with a values file stored in git.

Another option for argocd is to directly specify not a git repository but a helm chartmusuem to deploy from. This works well, but leaves one question open. Where do I store the values, I want to configure. ArgoCD is entrirely based on Kubernetes resources. So the configured application in ArgoCD is an application of its own. This yaml allows to sepcify values in its body in all the manners that helm allows you to. The problem is just the size, indentation and multiple tool interpreting the file. While you can locally test out a values file with a chart, that same values file has to be modified ever so slightly to fit into the ArgoCD application yaml. Some users get scared away from this idea, leaving them with only one other choice.

Helm dependencies allow you to reference other charts that need to be deployed alongside the templates you wrote yourself. However it is not necessary to add your own templates. You can simply write a chart by creating a Chart.yaml file. You add a version, name and dependencies to the chart you actually want to deploy. The values file gets stored in the same git repository as the Chart.yaml and now you can you the initial approach with the git repository. Your chart will stay immutable and your values are stored in a sperate file. However you will need to be aware of how helm works with subcharts. Values for a subchart are put in a different hierarchy level than the ones for the parent chart. Child chart 'pgadmin' will need to have his values stored under a root value of 'pgadmin:' in order to be assigned correctly.

As you can see no real satisfying solution is available for helm and argocd, but help is on the way with the upcomming release of 2.5. Since the exact details fo the implementation are unclear until release, I will postpone writing about this.

You can see examples of all three approaches in argocd/examples.

## Update Mechanisms


## Breathing in airgapped environments


## Knowing whats going on
