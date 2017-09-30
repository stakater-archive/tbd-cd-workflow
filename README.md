# tbd-cd-workflow
trunk based development (tbd) continuous delivery (cd) workflow

> Continuous Integration (CI) is a development practice that requires developers to integrate code into a shared repository several times a day. Each check-in is then verified by an automated build, allowing teams to detect problems early.

> Continuous Delivery (CD) is the natural extension of Continuous Integration: an approach in which teams ensure that every change to the system is releasable, and that we can release any version at the push of a button. Continuous Delivery aims to make releases boring, so we can deliver frequently and get fast feedback on what users care about.

> Continuous Delivery (CD) is a software strategy that enables organizations to deliver new features to users as fast and efficiently as possible. The core idea of CD is to create a repeatable, reliable and incrementally improving process for taking software from concept to customer. The goal of Continuous Delivery is to enable a constant flow of changes into production via an automated software production line. The Continuous Delivery pipeline is what makes it all happen.

# Common anti-patterns with branch based workflows?

Before we talk about TBD, let us look at some of the anti-patterns with branch-based workflows.

## Anti-pattern #1 - Long-lived feature branches

The core principle of Continuous Integration is that of integrating code frequently. So, if you are doing development on long-lived feature branches then you are no longer integrating code with everybody else frequently enough. This means delayed feedback, big bang merges that can be risky and time consuming to resolve, reduced visibility and reduced confidence.

But working on long-lived branches adds significant risk that cannot be fixed with tooling:

**Firstly**, the change-set in long-lived branches tends to be large. If someone else pushes a big change then it is going to be extremely difficult and risky to merge the code. We have all seen this at some point where we had to painstakingly merge lot of conflicts.

**Secondly**, this pain creates a tendency to hold back on refactoring on feature branches. Developers become wary of moving code between classes or even renaming methods because of the looming fear of having to resolve merge conflicts. Inevitably, this leads to piling up of technical debt.

**Thirdly**, working out of long lived branches does not give a chance for other developers on the team to pick up your changes quickly enough. This affects code reuse and can also result in duplicated efforts. For example, multiple developers upgrading to a new version of a library they need. Also, there is likely no visibility on changes happening in feature branches which eliminates any feedback loop from other developers in the project until the work is pushed to master, by which time it is too late to make changes to the design.

**Fourthly**, notice that the feedback loop with integration pipelines is long. If you have merge conflicts leading up-to master or test failures, it can be slow and painful to resolve.

**Finally**, feature branches promote the tendency of big bang feature releases — “Let me add one more thing before I merge to master” — which means not only delayed integration but also delay in delivering customer value and receiving early feedback. Also, the large change-set makes it difficult to troubleshoot issues or even rollback changes in Production if things go wrong.

In short, working out of long-lived branches adds risks and friction to the process of software delivery.

## Anti-pattern #2 - Branch per environment

One of other most common anti-patterns is to have supporting branches like Dev, QA, Staging and Production in addition to features branches. Developers work out of feature branches and merge to Dev branch when they are ready, and merge to upstream branches all the way to the Production branch. Each branch would then be configured on the CI server to run builds and deploy to a specific environment on which the feature is validated.

Apart from inheriting the problems associated with long lived feature branches, this workflow gives a false sense of security that the mainline or Production branch is pristine whereas if you look closely, it in fact introduces many new unknowns and risks.

For instance, even if you test your code exhaustively in a staging or a QA environment, there is no guarantee that the same revision is what is deployed to production. Because other changes can be made directly on any of the branches (e.g. Hot fixes for Production!) it means that what you verified on QA is not valid anymore. Merge conflicts while promoting to any of the upstream branches further reduce confidence.

In addition, because you rebuild the artifacts for every branch, there is even more risk because even small environment configuration changes can lead to differences in the binaries. For e.g. a package update or compiler version difference is enough to make the binary different from what was built previously.

# What is TBD?

In trunk-based development (TBD), developers always check into one branch, typically the master branch also called the “mainline” or “trunk”. You almost never create long-lived branches and as developer, check in as frequently as possible to the master — at least few times a day.

With everyone working out of the same branch, TBD increases visibility into what everyone is doing, increases collaboration and reduces duplicate effort.

The practice of checking in often means that merge conflicts if any are small and there is no reason to hold back on refactoring. Troubleshooting test failures or defects becomes easier when the change-set is small.

TBD also implies deploying code from the mainline to production – which means that your mainline must always be in a state that it can be released to production.

## Isn’t it too risky to develop on the mainline?

This sounds like a scary idea and many people initially balk at the idea of checking in directly to the mainline in favor of using branches which in comparison feel safe and cozy.

But don’t worry. Even the browser (chrome) that you are most likely reading this on, was built using this practice.

> While it is true that TBD expects a certain discipline from the developer, with the right practices, it can dramatically make your software development and deployment process much more reliable and lightweight.

The Continuous Delivery book mentions several best practices (below) to adopt TBD around the principle of keeping the mainline version releasable at all times:

**1. Small, incremental changes over big bang changes**

Frequent small changes are less risky, easier to integrate with and easier to rollback. Use branch by abstraction(not to be confused with version control branching!) to make even large-scale changes incrementally.

**2. Hide unfinished functionality with feature toggles**

Hide features that aren’t finished yet from users. Feature toggles are an effective way to hide new features before you are confident about releasing them to users.

**3. Comprehensive automated tests to give confidence**

With a comprehensive automated test suite designed to give fast feedback, you have high confidence about the changes you are making. If you are always checking in small incremental changes, test failures are easy to fix.

# What is Deployment Pipeline?

The deployment pipeline enables a trunk-based CD workflow. By modeling your application delivery as a pipeline of stages from the trunk all the way to Production, you get the visibility and reliability required to deploy continuously to Production.

> The deployment pipeline represents the path to production

The deployment pipeline is nothing but a concept that models your build and deployment workflow as a path to production. You model the pipeline as a series of stages. Each stage can be configured to run automatically based on the success of the previous stage OR with a manual trigger. Artifacts built in one stage can be consumed in subsequent stages – giving you the confidence that the same artifact that was tested is what is being deployed.

Continuous Delivery tools that implement the Deployment pipeline as first class concept can enable highly effective workflows with Trunk-based development.

TODO - add picture

You can see the list of commits and each commit has triggered a series of stages that lead all the way to Production. With each passing stage, you get higher confidence with that revision of the code. If something fails, the pipeline stops and you have to fix the build OR revert the commit that caused the failure. (Yes, this is inspired from Lean principles). Thus, the deployment pipeline thus affords a high degree of visibility into the build and the deployment process.

The pipeline can have any number of custom stages — including manually triggered stages — to deploy to intermediate environments like a QA or a Pre-Production (staging) environment. This way, a QA (for example) can decide to manually promote a revision to a QA environment and after verification, promote it to a production or staging environment as is the process.

This eliminates the need to have a branch per environment and enables a highly visualized and reliable workflow with trunk-based development.

Also, because there is no friction that comes with intermediate branches, this workflow promotes the practice of pushing small incremental changes that the customer benefits from continuously – which is the whole point.


# Continuous Code Review

## Pull Requests in Trunk Based Development

In trunk based development it is different. Since we want to merge our commits into the master branch as quickly as possible, we cannot wait until the complete feature is finished. Unlike in the original trunk based development approach we still use feature branches but we have much less divergence from the master branch than in Git Flow. We create a pull request as soon as the first commit is pushed into the feature branch. Of course that requires that no commit breaks anything or causes tests to fail. Remember that unfinished features can always be disabled with feature toggles.

Now, with part of the new feature committed and the pull request created, another developer from the team can review it. In most cases that doesn’t happen immediately because the developers don’t want to interrupt their work every time a team member pushes a commit. Instead, the code reviews are done when another developer is open for it. Meanwhile, the pull request might grow by a few commits.

The code is not always reviewed immediately after the commit but in most cases it reaches the master branch much quicker than in Git Flow.

# Feature Toggles

# Conclusion

> TBD is awesome, but it is not a silver bullet. Trying to implement it before having a reliable test suite and continuous integration in place (at least) will lead to serious quality issues in production.

> While it is true that TBD expects a certain discipline from the developer, with the right practices, it can dramatically make your software development and deployment process much more reliable and lightweight.

> One of the key technical practices that underpin this method of development, is working in "Small, incremental changes over big bang changes". Once your team embraces this practice, and the mindset behind it, you will find that your code reviews are rarely delayed more than a couple of hours. Because pull requests are very small, code reviews are very easy, so they happen more often.

> You can also work with your team to prioritise continuous flow throughput. That means, if a pull request is up for review, it is effectively a blocker for someone else's progress; so everyone prioritises dropping what they are doing to do the code review as fast as they can to unblock any work clogging up.

> Continuous deployment is a similar example: If you release a small amount of code to production, you have less to test, less to break and less to go wrong. And if something does go wrong, you are way more likely to understand what caused it because the changes to that environment were smaller and happened closer to the time of release, so they're easier to remember and resolve.

> no branches, runtime switches, tons of automated testing, relentless refactoring, and staying very close to HEAD of our dependencies.

# References

* https://blogs.technet.microsoft.com/devops/2016/06/21/a-git-workflow-for-continuous-delivery/
* https://www.thoughtworks.com/insights/blog/enabling-trunk-based-development-deployment-pipelines
* https://trunkbaseddevelopment.com
* https://medium.com/@aboodman/in-march-2011-i-drafted-an-article-explaining-how-the-team-responsible-for-google-chrome-ships-c479ba623a1b
* https://martinfowler.com/articles/feature-toggles.html
