core_pipeline {

  on_commit { // Handling a non-merge, non-PR commit to a Feature Branch.
    if (branch ==~ branches.master) {
      // TODO: Lock this down in GitHub, by making master a protected branch
      println "WARNING: Direct commits to \"master\" branch go against the implemented branching strategy"
      phase.continuous_integration()
      phase.deploy_to development
      phase.deploy_to production
    }
    else if (branch ==~ branches.develop) {
      // TODO: Lock this down in GitHub, by making develop a protected branch
      println "WARNING: Direct commits to \"develop\" branch go against the implemented branching strategy"
      phase.continuous_integration()
      phase.deploy_to development
    }
    else {
      // NOTE: To see the all stages/steps of this phase, look at this same file on the sample/exploded branch
      phase.commit()
    }
  }

  on_pull_request {
    if (target_branch ==~ branches.develop) {
      // PR will preemptively merge the two branches of the PR into a temporary branch
      phase.continuous_integration()
    }
    else {
      println "WARNING: No actions to perform on a PR into the \"${target_branch}\" branch for the implemented branching strategy"
    }
  }

  on_merge {
    if (branch ==~ branches.develop) {
      if (source_branch ==~ branches.master) { // merging from a higher protected branch into a lower. Treat as a PR into develop
        phase.continuous_integration()
      }
      else { // deploy to Development env, and await approval to deploy to test env
        phase.deploy_to development
        phase.deploy_to app_env: testing,
                approval: [message: 'Are you ready for a new deployment to the Testing environment?']
      }
    }
    else if (branch ==~ branches.master) { // deploy to Production env
      // TODO: Look to add prod-only checks here
      phase.deploy_to production
    }
    else { // Otherwise, merging among lower branches is handled as a commit. Do commit checks
      phase.commit()
    }
  }
}
