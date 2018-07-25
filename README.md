# gitlab-ci-push
How to push to a gitlab repo through Gitlab ci runner

GitLab CI uses a YAML file (.gitlab-ci.yml) for the project configurationand it is placed in the root of your repository and contains definitions of how your project should be built.

Place the following before-script to register the key, git user and email.

before_script:
```  
  ##
  ## Create the SSH directory and give it the right permissions
  ##
  - mkdir -p ~/.ssh
  - chmod 700 ~/.ssh

  ##
  ## Use ssh-keyscan to scan the keys of your private server. Replace gitlab.com
  ## with your own domain name. You can copy and repeat that command if you have
  ## more than one server to connect to.
  ##
  - ssh-keyscan -H gitlab.com >> ~/.ssh/known_hosts
  - chmod 644 ~/.ssh/known_hosts
  - git config --global user.name "${GITLAB_USER_NAME}"
  - echo 'git user name done' ${GITLAB_USER_NAME}
  - git config --global user.email "${GITLAB_USER_EMAIL}"
  - echo 'git user email done' ${GITLAB_USER_EMAIL}
  - git remote set-url origin https://gitlab-ci-token:"${ACCESS_TOKEN}"@code.siemens.com/username/repo.git
```

Let us say we have a gittask task that has to be run by the runner and assume that a java class is generated inside the folder generatedCode that has to be committed and pushed into the repo

```
gittask:
  - cd generatedCode
  #Instead of cd, we can aso use the move command (mv)
  - cp -r . ../
  - cd ..
  - rm -r generatedCode
  - git add --all
  - echo 'git add done'
  #We need to use the [skip ci] in the commit message to prevent/skip subsequent triggers
  - git commit -m "[skip ci] Runner pushing new code"
  - echo 'git commit done'
  #This created a detached head in most cases and so we need to push on top of the HEAD:branch
  - git push origin HEAD:master
```
