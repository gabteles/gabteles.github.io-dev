+++
date = "2016-07-30T21:39:13-03:00"
draft = false
title = "Using Github Repository as Maven Repository"
+++

![Octocat Repository](/images/octocat-repository.jpg)

Last week I was working in a project that needed a private Maven Repository since we couldn't expose our artifacts in repositories like [JCenter](https://bintray.com/bintray/jcenter) or [Maven Central](http://search.maven.org/).

This situation led me to some possible solutions, which I learned from searching. I'm not sure if they're the best or if there are easier options, but I'll explain a bit about them and why Github appeared the best to me.

### Creating an private server running Artifactory

I've used [JFrog Artifactory](https://www.jfrog.com/open-source/) in the past, with other projects. Since it's a "native" solution to our problem, I thought first about using it.

The main obstacle to this option is that it has some chances of causing problems in the future. We would have to manage it, upgrade when necessary, control access (!), etc. It somehow runs out of our main devops assignments.

I think if we're a bigger company, this could have been an perfect solution, after all, it cleverly solves this problem.

### Using [jitpack.io](https://jitpack.io/)

Jitpack is a service that allow you to publish JVM/Android libraries in git (Github, Bitbucket, GitLab, etc) repositories and use them just adding `https://jitpack.io` as a maven repository in the project which will consume that artifacts. It has another great features, check it out!

Of course it is also an nice solution. It solves the problem which I've found on the first option: no more maven repository management tasks. But it let me a bit uncomfortable because to use it with one private repository you have to pay U$9. Yeah, it's a small amount, but between managing Artifactory and paying it, I'd rather take first one.

I don't know if you thought the same I did, but one repository is too few! Now I think it's sufficient. We could use one repository to publish all our artifacts and serve them using Jitpack.

### Github raw version

Because of the "anatomy" of how maven search for artifacts in given repositories, trying to read `http://your-repositorys-url.com/your/artifacts/group/id/pom.xml`, some reading made me try to use `https://raw.githubusercontent.com/username/repository/master/` as respository.

I've got partial success by using it: authentication knocked me down! With public Github repositories, it works like a charm. Private repositories do need HTTP's basic auth, if I'm not mistaken. I'm not sure if it was my or gradle's fault. Even using maven repository credentials, it hasn't worked.

As we needed private hosting, it has not solved our main problem, but gave me some clues!

### Gradle Plugin to rule them all

Finally I've found [layerhq/gradle-git-repo-plugin](https://github.com/layerhq/gradle-git-repo-plugin), _"this plugin allows you to add a git repository as a maven repo, even if the git repository is private, similar to how CocoaPods works."_

It allowed me to use Github private repositories as Maven repository just by adding `git("https://some/clone/url.git", "arbitrary.unique.name", "master", "releases")` and following the setup steps described in the readme.

More than just using git repositories, this plugin adds an `publishToGithub` gradle task to generate artifacts, commit and push them to the repository! This 100% solved my problem.

### The Happy End

Now I can publish and retrieve Maven artifacts from private git repositories, don't mind about managing maven repository server or even managing permissions, since only our devs and CI environment have access to our Github repository and they can/should access and generate this artifacts.
