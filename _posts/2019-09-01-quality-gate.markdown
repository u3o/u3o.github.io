---
layout: post
title:  "Checking quality gate via Gitlab"
date:   2019-09-01 10:03:16 +0200
categories:
---
Working with sonarcloud throw gitlab pipeline

{% highlight yaml %}
quality-project:
  image: $MAVEN_IMAGE
  stage: quality-gate
  variables:
    SONAR_TOKEN: "${SONAR_LOGIN}:"
    SONAR_API_URL: ${SONAR_HOST_URL}/api/project_branches/list?project=${SONAR_PROJECT_NAME}
    SONAR_PARSE_EXPRESSION: "*\"ERROR\"*"
    CURRENT_BRANCH: ${CI_COMMIT_REF_SLUG}
  before_script:
    - apt-get update -qq && apt-get install -y jq
    - mvn sonar:sonar -s settings.xml -B -Dsonar.branch.name=${CI_COMMIT_REF_SLUG} -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn
  script:
    - export GATE_RESULT="$(curl -u $SONAR_TOKEN $SONAR_API_URL | jq --arg branch $CURRENT_BRANCH '.branches[] | select(.name==$branch) | .status.qualityGateStatus')"
    - echo "Quality gate result - " $GATE_RESULT
    - if [[ $GATE_RESULT == $SONAR_PARSE_EXPRESSION ]]; then exit 1; fi
  only:
    - branches
{% endhighlight %}