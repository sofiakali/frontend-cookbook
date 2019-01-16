# Code versioning

Every developer needs some [version control](https://en.wikipedia.org/wiki/Version_control) and we decided to use Git for that purpose. Here you can find some description of some best bractices or worflows we are trying keep when using it.

## Topics

 - [Branching model](#branching-model)
 
## Branching model

There is several standard [git workflows](https://cs.atlassian.com/git/tutorials/comparing-workflows), and we use two of them: [Git flow](#git-flow-workflow) and [feature branch](#feature-branch-workflow) workflow. Which one we use depends on what we are developing.

### Git flow workflow

That is a worflow we use when developing **applications**. There are always at least two persistent branches `master` and `develop` (`development`) as shown on the picture. Most time there is also `stage` branch that represents middle phase between `master` and `development`.

All mentioned branches (`master`, `development`, `stage`) represents environment for deploying. 

* `development` - we use it when developing application, every new feature or bug fix (not urgent) is first merged into development branch which is then deployed to development environment and tested if it works.
* `stage` - when project manager decides that developed features, fixed bugs are ready to test by customer, they are usually first merged to `stage` branch and test at its environemnt before deploying to production
* `master` - when new features, bug fixes are approved by testers (`development`) and customer (`stage`) they are ready to merge to `master` branch and develop to production environemnt.  
*Be aware that for `master` branch unlike for `development`, deploy is usually not started automatically and you need to start it manually.*

![Git flow wofklow](https://buddy.works/blog/images/gitflow.png)  
*Image taken from https://buddy.works/blog/5-types-of-git-workflows*

You can find more about the workflow at external resources below:

* https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
* https://nvie.com/posts/a-successful-git-branching-model/

### Feature branch workflow

That is a worflow we use when developing **libraries** (see [tools we cook](./ToolsWeCook.md)). We found it more effecient than [Git flow](#git-flow-workflow) as there is usually no development version hold for some time and when new feature is finished, it's required to release immediately.

In short there is only one persistent branch called `master`, all other branches (feature, release, hotfix, etc.) are temporary, exists only for a neccesary time and are supossed to delete afterwards.

![Feature branch workflow](https://buddy.works/blog/images/feature-branch.png)  
*Image taken from https://buddy.works/blog/5-types-of-git-workflows*

You can find more about the workflow at external resources below:

* https://guides.github.com/introduction/flow/
* https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow

