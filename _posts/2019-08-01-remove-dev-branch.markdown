---
layout: post
title:  "Env clean up after branch is removed"
date:   2019-08-01 10:23:16 +0200
categories:
---
Define process to be able to do env clean up after branch is removed

{% highlight yaml %}
deploy-dev:
 stage: deploy
 image: $KUBERNETES_DEPLOY_RUNNER
 ...
 environment:
   on_stop: stop_dev_env 
   name: dev

{% endhighlight %}

{% highlight yaml %}
stop_dev_env:
 stage: deploy
 image: $KUBERNETES_DEPLOY_RUNNER
 variables:
   GIT_STRATEGY: none
 before_script:
   - mkdir -p /etc/deploy
   - echo ${kube_config_dev} | base64 -d > ${KUBECONFIG}
   - kubectl config use-context dev.agspace.net
 script:
   - export DEPLOY_NAME=$(if [ "${CI_BUILD_REF_NAME}" == "master" ]; then echo "${CI_PROJECT_NAME}"; else echo "${CI_PROJECT_NAME}-${CI_COMMIT_REF_SLUG}"; fi)
   - kubectl -n dev delete deployment $DEPLOY_NAME
   - kubectl -n dev delete service    $DEPLOY_NAME
   - kubectl -n dev delete ingress    $DEPLOY_NAME
 when: manual
 environment:
   action: stop
   name:   dev
{% endhighlight %}