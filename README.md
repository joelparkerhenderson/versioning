# Versioning

This page has versioning notes about version numbers, deterministic builds, pinning, vendoring, and discussion from peers. Suggestions are welcome.

* [Introduction](#introduction)
  * [Do this first](#do-this-first)
  * [What is a version identifier?](#what-is-a-version-identifier)
* [Types of version changes](#types-of-version-changes)
  * [Bug fix change](#bug-fix-change)
  * [Non-breaking change](#non-breaking-change)
  * [Breaking change](#breaking-change)
* [Types of versioning](#types-of-versioning)
  * [Semantic versioning](#semantic-versioning)
  * [Increment versioning](#date-versioning)
  * [Keyword versioning](#uuid-versioning)
  * [Date versioning](#date-versioning)
  * [Hash versioning](#hash-versioning)
  * [UUID versioning](#uuid-versioning)
  * [Tuple versioning](#tuple-versioning)
* [Deterministic builds](#deterministic-builds)
  * [Version pinning](#version-pinning)
  * [Dependency resolution](dependency-resolution)
  * [Vendoring](vendoring)
* [Controversies](#controversies)
  * [Semantic vesioning doesn't work](#semantic-vesioning-doesnt-work)
  * [A package manager should provide no options for version pinning](a-package-manager-should-provide-no-options-for-version-pinning)
  * [Package reputation](package-reputation)
  * [Automated regression testing](#automated-regression-testing)
  * [Tuples](#tuples)

Credit:

  * https://news.ycombinator.com/item?id=15676691

See also:

  * ["The cargo cult of versioning" by Kartik Agaram](http://akkartik.name/post/versioning)
  * ["Volatile Software" by Steve Losh](http://stevelosh.com/blog/2012/04/volatile-software)
  * ["Dear package manager, dependency resolution results should be in version control" by havoc](https://blog.ometer.com/2017/01/10/dear-package-managers-dependency-resolution-results-should-be-in-version-control/)
  * ["Versioning RESTful web resources -- a survey" by Stu Charlton](http://web.archive.org/web/20160407080723/http://stucharlton.com/blog/archives/2010/03/versioning-restful-web-resources---a-survey.html)

## Introduction

Versioning software can be challenging. There's terminology to learn and many tradeoffs.


### Do this first

If you're new to versioning, or just want to know what to do first, we recommend these steps:

1. Use semantic versioning.

2. Use deterministic builds, including pinning, resolutions, lockfiles,  and vendoring.

3. Add the dependency resolution files, e.g. the lockfiles, to your version control.

4. Use automatic testing to verify your dependencies and their ugrades.


### What is a version identifier?

A version identifier is a way to help people understand these kinds of questions:

  * Is this version better/newer than this other version?

  * Will upgrading to this new versino break anything?

  * Do I need to upgrade this to be secure?

A version identifier is typically a complex data type made up of iterable data types. 

  * A version identifier isn't typically a simple string, nor a simple number.

  * A version identifier is also known as a version number, even though it's not really a number.

Examples:

  * A string of numbers and dots, such as "1.2.3", to indicate major version 1, minor version 2, micro version 3.

  * An incrementing number, such as "123", to indicate the 123rd release or build.

  * A keyword, such as "alpha" to indicate an alpha-testing release.

  * A date, such as "2016-12-31" to indicate the publication date of 2016, December 31st.

  * A hash, such as "a7ac99da44892ef15fe15d7ad2d22d292d9d2a85" to indicate a git commit.


## Types of version changes


### Bug fix change

A bug is any deviation in actual behaviour from what the documentation says.

  * If a program isn't documented, then its code implicitly fills the role of documentation. 

A bug fix is a program change that brings the actual behavior into line with the expected behavior. 

  * If a prgram isn't documented, and the bug isn't about some kind of externally documented area, then a bug fix might be considered a breaking change by some people.


### Non-breaking change

A non-breaking change:

  * If a program that uses the previous behavior is still correct, then it's a non-breaking change. 

  * For example, adding a new function usually fits here. But that is always verified from the point of view of documented behavior, never looking for actual behavior.


### Breaking change

A breaking change:

  * If the behavior is different from what the documentation previously said it was, then it's a breaking change.

  * If people are relying on undocumented behavior, they will have problems upgrading their libraries. They should be expecting it, if they are not, it's their problem not the library maintainer's. Wether to depend on undocumented behavior is a decision made by the library users at their discretion by weighting their opotions, it shouldn't impact upstream.


## Types of versioning


### Semantic versioning

Semantic versioning is a simple set of rules and requirements that dictate how version numbers are assigned and incremented.

Given a version number MAJOR.MINOR.PATCH, increment the:

  * MAJOR version when you make incompatible API changes, i.e. breaking changes.

  * MINOR version when you add functionality in a backwards-compatible manner, i.e. non-breaking changes.

  * PATCH version when you make backwards-compatible bug fixes.

  * Additional labels for pre-release and build metadata are available as extensions to the MAJOR.MINOR.PATCH format.

See http://semver.org/

Pros:

  * Easy to skim.

  * Widely supported by many projects.

Cons:

  * Some major projects (e.g. Rails) do not support semver, and are ok with having significant breakage even at minor versions.


### Increment versioning

Increment versioning is a simple idea to just use a number, and for each new version, just add one.

  * For example you would have version 1, version 2, version 3, etc.

  * This is very similar to the concept of a "build number", which is an incrementing number that adds one each time a software project is built.

Pros:

  * Easy to understand.

  * Sortable.

Cons:

  * Does not indicate a purpose of a version upgrade, such as a fix, or new feature, or breaking change.

  * Even a custom enumeration that holds values such as “alpha”, “beta”, "pre", “rc”, isnt just a string, but an enumerated type, meant to convey meaning. The fact that they are serialised into a format that happens to be readable and parsable, is besides the point. 


### Keyword verisioning

Keyword versioning is a general idea to use special words in the version string.

  * Such as "alpha" to indicate an alpha test release, "pre" to indicate a pre-release, "rc" to indicate a release candidate.

Pros:

  * Easy to skim.

  * Conveys more meaning than just a number.

Cons:

  * These strings may mean different things in different libraries, based on their release processes.

  * These strings may not mean exactly the same thing in different software packages, yet there are widely accepted conventions, such as "rc" meaning a release candidate, and "rc0" meaning the first release candidate. 


### Date versioning

Date versioning is a general idea to use a date and/or date-time as the version code.

  * For example you could use the project date-time-stamp.

  * This can be represented as a standard date-time string.

  * Such as "2017-12-31 12:00:00".

  * Amazon Web Services (AWS) uses a date version for APIs.

  * Ubuntu & JetBrains both use date fields in their versioning.

Pros:

  * Easy to see when the project was updated.

  * Easy to sort.

  * Easy to understand steps. For example the step from "2010-01-01" to "2017-01-01" is a much longer time duration that than "2016-01-01" to "2017-01-01".

Cons:

  * Hard to quickly understand if/why to migrate to a subsequent version.


### Hash versioning

Hash versioning is a general idea to hash the codebase, and use the output as the version code.

  * For example you could use a git commit hash.

  * This can be represented as a unique hexdecimal lowercase string of 40-characters.

  * Such as " a7ac99da44892ef15fe15d7ad2d22d292d9d2a85".

  * Notably, git is planning to phase in hashes that are more secure and longer.

Pros:

  * A sufficiently-powerful hash will always correspond exactly to a project's exact state, because that's what a sufficiently-powerful hash does.

Cons:

  * A git commit hash is essentially impossible for humans to comprehend in terms of a project's state. For example, there's no human-friendly way to quickly eyeball two git commit hashes, and know that one hash's state is a security bug fix for the other hash's state. This means that a project developer would need to use some other way to understand what each hash means, such as a change log, or history document.

  * A git commit hash is not relevant to other version control systems that may also be in use simultaneously. For example, we have projects that are simultaneously versioned by using Git, and Subversion, and Microsoft Visual Source Safe. Each version control system uses its own unique identifier. This means that a project that wants to fetch a dependency from different version control systems must have a developer that understands how a git commit hash relates to the other version control system identifiers. This could mean forcing each version control system to contain exactly the same files, or could mean using some kind of comparison chart, or change log, or history document, or lookup table.


### UUID versioning

UUID versioning is a general idea to create a universally unique identifier (UUID), and use that as the version code.

  * For example you could generate a secure random UUID-4.

  * This can be representated as a unique hexadecimal uppercase string of 36 characters.

  * Such as "90161E28-AEB7-5DD1-FCCB-0FDA3FEDAE99"

Pros:

  * A UUID could always specify a project's exact state, assuming there's some way to map the UUID to the project's files.

Cons:

  * A UUID is essentially impossible for humans to comprehend in terms of a project's state.

  * For example, there's no human-friendly way to quickly eyeball two UUIDs, and know that one UUID's state is a security bug fix for the other UUID's state. 

  * This means that a project developer would need to use some other way to understand what each UUID means, such as by parsing a change log, or reading a history document.

  * Also, there would need to be some way to connect the UUID to the project's state, such as by using a git tag, or change log, or history document.


### Tuple versioning

Instead of a string "1.2.3" we could use a tuple "(1, 2, 3)".

Pros:

  * Could be easier for machines to understand.

Cons:

  * Why bother? You’ll end up treating them the exact same way. 

Questions:

  * Or does the tuple have some structure that is different from the containment found in semver? 


## Deterministic builds


### Version pinning

Version pinning is when a developer specifies the usable version id, or range of ids.

Example using Ruby bundler file `Gemfile`:

    gem "alpha", "1.0.0"
    gem "bravo", "~> 1.2", ">= 1.2.3"

Example using Rust Cargo file `Cargo.toml`:

    [dependencies]
    alpha = "1.0.0"
    bravo = "^1.2.3"

Examples using other languages are welcome here-- please let us know.


### Dependency resolution

Many popular software frameworks provide ways to coordinate multiple dependencies:

  * Figure out which dependencies work with each other, or rely on each other, or conflict with each other, or require additional dependencies.

  * The process typically involves creating a dependency tree, or dependency graph, that searches all published versions to find the best-possible matches that all work together.

  * The result is typically a complete list of all the dependencies and their exact version numbers.

Example using Ruby bundler:

  * When you run `bundle install`, the tool looks at all the dependencies, typically by parsing the `Gemfile` to find gem entries) and any known pinning (e.g. A Gemfile gem entry that uses a version number).


### Lockfile

Many popular software frameworks provide ways to save the dependency resolution list, or tree, or graph, to a special file called a "lockfile".

  * The lockfile typically contains the entire list of dependencies.

  * The lockfile typically gets updated by the package management tool only, and never by a developer editing the file.

  * Many developers put the lockfile in version control along with the project, so that anyone who checks out the project has a complete list of dependencies and their exact version numbers.

Example using Ruby bundler:

  * When you run `bundle install`, the tool does dependency resolution then saves the gem names and version numbers in the special file `Gemfile.lock`.


### Vendoring

Vendoring is when your project includes all the dependency code.

Example using Ruby bundler:

  * When you run `bundle install` you can tell the tool to save all the dependency code into a project directory, such as `./vendor`, and then you can add the directory to version control. 

  * This means that anyone who clones your repository gets all the dependency code, and it's the exact same code you use.

  * This means that the project is "protected" from something changing upstream, such as a dependency's GitHub repository being deleted, or a dependency's upstream source code being changed out from under you, after you cloned it.

  * Vendoring is a general term, and has more sophisticated approaches than using a `vendor` directory. For example, a company may choose to use an artifact management system that can download a dependency, then save it permanently for many projects, and provide projects with proxy server, or local URL, or local git repository.

Example using git submodules:

  * Some developers consider it "vendoring" when a project uses git submodules, because the project does contain all the code for the submodules.


## Controversies


### Semantic vesioning doesn't work

Why "semantic versioning" doesn't really work:

* You can never be certain that the maintainer actually follows it

* One user's breaking change is one maintainer's bugfix

* While some packaging systems attempt to automate and enforce it (e.g. Elm's) even that only goes so far due to type system limitations (e.g. changing acceptable values from [0, 4] to [0, 4[ as a side-effect of some change deep in the bowels of the library's logic is pretty much guaranteed not to surface if you don't have a dependent type system, and may not do so even then.


### A package manager should provide no options for version pinning

TODO


### A package manager that followed such a proposal would foster an eco-system with greater care for introducing incompatibility

TODO


### Package reputation

Idea: Packages that wantonly broke their consumers would "gain a reputation" and get out-competed by packages that didn't, rather than gaining a "temporary" pinning that serves only to perpetuate them.


### Automated regression testing

Q. Could package registries implement some sort of required automated integration testing?

  * If a package owner wants to update their package with what they claim is a non-breaking change according to semver, then the new version must pass all integration test cases of the previous. 

  * This could also include crowdsourced testing of that package, that allows users of that package to add tests over time to that particular version, so as to mitigate owners who don't test their packages well enough.

A. Yes this could help.

  * You still need to verify and test package upgrades and pin versions; but it would make the semver contract at least specified to some degree.

  * Some verification would be better than no verification. 

  * Although, then you’re just defining “breaking change” as “change that breaks the tests.” Anyone with any programming experience knows that “but the tests passed” is not sufficient to catch all bugs.

  * See perl CPAN for an example of automated testing on a mass scale on many platforms.


## Comments


### Version number

* Version numbers signal the intent of the release, but don’t guarantee anything. 

  * Any change in a dependency might break your project so no update is safe, regardless of what the version number says. 

  * That’s why people pin versions (or, at least should be why.) It’s not a problem of version numbers at all. 

  * That doesn’t make version numbers useless, though. Understanding the intent of an update is important info. It lets you estimate the cost/risk of taking an update. 

  * What we actually want is for package managers to be conservative by default.

  * Some people suggest that the latest version with the same major version be taken. That would work as long as... (1) none of your dependencies ever introduces bugs or accidental compatibility changes in a non-major update; and (2) all contributors update dependencies in lockstep (since any new code contribution could be dependent on a feature or behavior of a newer version of a dependency). In other words, that doesn’t work at all.

  * What we want is for package managers to lock the dependencies by default and for package repositories to require a version update when committing a dependency update. In fact, better to take the version number out of it: lock dependencies to the commit hash and leave version numbers for what they are good for, to communicate intent.
	

### Semantic versioning

* Semantic versioning is a way for package maintainers to signal their expectations.

  * It's not something you can rely completely on - in these systems I'm describing, you still need automated tests to give you more confidence that upgrading is safe. 

  * But semantic versioning improves your confidence which helps avoid wasting work on an upgrade that will certainly fail, and helps identify problems in advance.

* I do not understand the popularity of semantic versioning. Why would I trust hundreds of people that their non-breaking changes are really non-breaking to me? And suddenly, the auto-scaling fails in production because something has updated to include a new bug.

* Semantic versioning is a heuristic for a largely stochastic process. You and only you are responsible for whether your code is broken or not. Semantic versioning is just this one standard most of us have agreed to follow as a community to make that responsibility a little easier to bear.

* Semver-based package managers don't just naively always upgrade to the very latest version like the article suggests. The default behavior is to limit upgrades to semver-compatible versions. There are tools like greenkeeper which use your project's unit test to try out packages before upgrading, adding extra level of assurance. While none of this is a perfect guarantee, it's IMHO working well enough.

* Semver-based package managers are most modern ones (e.g. npm, bundler, cargo) that have the concept of Semver embedded in the version constraints for dependencies.

  * E.g. the tilde (~) and caret (^) operators in npm[0] allow you to specify version constraints on dependencies, that allow them to be resolved/pinned to higher minor/patch versions of that package, but not to a higher major version since that will by the Semver definition contain breaking changes and those might impact your project.
[0]: https://docs.npmjs.com/misc/semver#tilde-ranges-123-12-1

* There are tools like greenkeeper which use your project's unit test to try out packages before upgrading, adding extra level of assurance.

  * Some communities have also started doing the reverse, where as a library you can get all your users's tests to run to check that you have not broken something they were using.

  * I know that that happens for the Rust compiler, possibly some libraries as well. I don't know that it's systematic but I do know that they regularly leverage Cargo to find out major dependencies/users and run those against pending changes.

* Semantic versioning has good intentions, but adherence and accuracy of the signalling is variable.

  * Absent external enforcement, that's just the way it is -- it's a self-asserted string that no one bothers to validate and everyone hopes they can rely on. 

  * If package repositories were more than just storage, perhaps this would be a different story.

* The reasons behind semver: communication between humans as to what types of changes a potential upgrade contains. 

  * The tooling supports this so that I can pull in changes that shouldn't break the build, test them to see if that actually is correct, and then deploy them automatically, with minimal risk. 

  * Between security fixes and bug patches, I have a need to know what types of updates I'm looking at, and to ensure that the updates that aren't breaking changes get applied diligently — and that's why we have the notion of semver: so that we can all communicate this the same way, and so that our tools can understand it, and act accordingly.

  * Now, just because semver says that 1.3 should be compatible w/ 1.2 doesn't necessarily mean it is. Mistakes happen. That's what tests are for, and it doesn't mean you need to blindly upgrade; this is why Cargo has a separate notions of the versions that the project should be compatible with, such as ^1.2 recorded in a Cargo.toml, and what versions the project is actually using, exactly, which are contained in the lock file, so that the project can be rebuilt exactly as a previous build was, but can also be trivially upgraded where and when possible.

* Semver tries to solve very real problems, and any alternative approach would need to explain how it would solve these real problems. 

* Semver is a social protocol, not a technical one; in other words, it's wishy-washy meatspace stuff (and yet it's still the best we've got at the moment). As consequence of being wishy-washy meatspace stuff, it lets any library that uses semver define "breaking change" in any way it wants to. This is literally the first rule of the semver specification (where, for the entirety of the document, the "public API" is how breakage is defined)/

* I write a bunch of Rust. The Cargo package manager has several layers of "defense":

  * 1. The "Cargo.lock" contains exact versions of all transitive dependencies. (And Cargo doesn't allow overwriting a version once published, and lock files even override normal package "yanks", so these version numbers are very stable identifiers.)

  * 2. The "Cargo.toml" file contains manually-specified semver information. I can run "cargo update" to get the latest, semver-compatible versions of all my dependencies.

  * 3. In the rare case that somebody messes up semver, I can just blacklist certain versions in "Cargo.toml", or add more specific constraints. I think I've done this maybe two or three times in thousands of dependency updates.

  * 4. My own projects have unit tests, and of course, Rust does extensive compile-time checks. So I'm likely to catch any breakage anyway.

  * So the absolute worse-case scenario here is that somebody messes up (which is very rare). At this point, I can just file a bug upstream, lock a specific version down in "Cargo.toml", and wait for things to get sorted out.

  * I have zero need to "be certain". I'm quite happy with "It works nicely 99.9% of time, and it has an easy manual fallback when the inevitable problems occur."

Cons:

* The problem with semver is that every change is either a new feature or a breaking change. A bug is encoded in the behavior of the program. "Fixing" a bug is changing the behavior of the program - breaking it if someone downstream was implicitly or explicitly relying on the bug.

  * Most people want the bug fixed, and would tag the change as 1.0.1 rather than 2.0.0; but in someone's dependency tree is version 1.0.0 of your project, and upgrading to 1.0.1 will break them, even though semver tells us that ..1 is a minor change for most consumers.

  * Incidentally, this is also why evolving the web platform is so hard. Among the uncountable sites on the internet, even if the current behavior seems totally broken, someone is shipping code that relies on it.


### Increment vesioning

* No comments yet.


### Date vesioning

* No comments yet.


### UUID versioning

* No comments yet.


### Hash versioning

* Commit hashes are incomprehensible to most humans and look like noise. It takes extraordinary mental effort to compare them, while the difference between "1.2.3" and "1.4.5" is instantly apparent to any human.

* If a package maintainer force pushes to a repository, this would makes it hell for your package manager: you would now have to manually hunt your dependency's repository for the closest commit hash to the one that was removed.


### Tuple versioning

* No comments yet.


### Pinning

* Pinning is absolutely crucial when building software from some sources out of your control, especially with larger teams or larger software. You cannot rely on the responsibility of others, be they 100s of upstream library authors, or 100s of other peer developers on your product. One among those people may cause a dependency to break your build, either by releasing a bad version, or foolishly adding a dependency without carefully checking its “reputation” for frequent breaking changes.

* Without pinning, your build is non-deterministic and sometimes starts failing it’s tests without any diff. You can’t bisect that. Your only remediation is manual debugging and analysis. Work stops for N engineers because everyone’s branches are broken.

* Pinning is absolutely crucial. The problem isn't with versioning, it's with non-deterministic builds. Once you have a large enough project that you can no longer keep manual track of version changes, you build tooling which checks for the latest version and opens a pull request (and subsequent CI build on the pre-merge commit) with the latest version. This keeps your project up to date and prevents the woes of non-deterministic building.

* I think no pinning, and just taking the latest package every time, is a really bad idea. 

  * Bugs can creep into software even with the best of intentions, and it is useful for consumers of packages to have a choice whether to upgrade immediately or maybe wait until they have done some risk analysis. Also, the ability to roll back is invaluable.

  * The package-3, package-4 thing seems odd to me. I use Nuget and have used NPM / Gem / Haskell Stack on spare time projects. I would find it annoying to use this scheme because you have to hunt around searching in a different way to look for major upgrades compared to what you do for minor ones.

  * I want my build to be reproducible, going back into history. Having the pinned version numbers committed to source control in ensures this (As you keep an onsite cache of the packages, or you trust the package repo to stay available and for published versions to be immutable).

* I use version pinning to be resilient to changes in the greater internet: builds don't use the internet, and instead use internally pinned and vendored sources for third party libraries.

  * If you rely upon the internet to build and deploy your code, then someone else's outage (like github's) can become your outage, or make yours worse. If you work on a product which has high uptime requirements, this is unacceptable.

  * Just as important, suppose any one of those dependencies breaks the social contract described in the posted article: they make a backwards-incompatible change, and your code no longer compiles or builds or (much worse!) subtly breaks. Now you're relying very strongly upon the good intentions and competency of all third party maintainers.

  * Worst of all, suppose any of your dependencies gets compromised and now has a security vulnerability. What do you do?

* Because I need network-free, reproducible builds of known-good code, I need to maintain a copy of third-party code somewhere. 

  * When I start doing that, I need to keep track of what version (which could be a commit, sure) I'm storing to know when I'm out of date. 

  * Then, when my cached copy is out of date, I need to know how safe it is to update to match the remote version: should I expect builds to break, or not?

* It's also helpful to have minor and patch versions so applications can concisely describe which features of third-party dependencies are required to build and run your code. This makes it easier to integrate new applications - you can clearly see whether your cached copy of the third-party code is up-to-date enough.


### Pinning and technical debt

* The trouble with version pinning is that, over time, technical debt builds up. 

  * Software is built using out of date versions. 

  * There are version clashes when A needs C version 1, and B needs C version 2, and A and B are needed by D. 

  * When you pin a version, you begin to accrue technical debt.

  * This has long been a curse of ROS, the Robot Operating System, which is a collection of vaguely related packages which speak the same interprocess protocol. Installing ROS tends to create package clashes in Ubuntu, even when using the recommended versions of everything. This gives us a sense of what version pinning does to you like after a decade.

  * Tools for computing the total technical debt from version pinning in a package would be useful. You should be able to get a list of packages being used which have later versions, with info about how far behind you are in time and number of updates. Then at least you can audit version pinning technical debt. You can assign someone to bringing the technical debt down, updating packages to the latest version and running regression tests.


### Vendoring

* Pinning, while more reliable, is not actually fully reliable. If you want reliable you should be checking in copies of your dependencies either directly or as sub modules, i.e. vendoring. Then if you want to upgrade you check in the latest. Pinning still allows the person in control of the 3rdparty repo to mess you up. Your reproducibility is still at their mercy.

* Pinning isn't enough. People push history changes, rename things and just delete stuff all the time. I've had this happen to be more than once and would now never consider a application production ready until all dependencies are either vendored or forked.

* You should either commit dependencies, or have your own repository, or caching proxy which will neither change nor drop old versions.

* I agree that the best approach today is to maintain a local cache of a specific version, and to maintain unit tests to warn you of any breakage. In which case: what's the point of a package manager, again?

* My current approach is to be extremely conservative in introducing dependencies, inline any dependencies I do introduce, and then treat them as my own code, polishing them alongside my own stuff, deleting code paths I don't need, submitting patches upstream when I discover issues from my hacking. I'm investigating better ways to write automated tests, so that when tests pass we can actually gain confidence that nothing has regressed: http://akkartik.name/about.

* There are plenty of strategies for making your dependencies deterministic. At Airbnb, we use caching proxies in front of all third-party language package managers, so the first time someone in the org pulls “foo 1.2.3”, the source tarball is frozen forever. The package can’t be yanked upstream, force-pushed over, etc. This is a kind of vendoring.


### Lockfile

* Package managers like npm5/yarn and Cargo have lock files that pin exact versions by default, for all dependencies, recursively. If you keep that file (commit it), your project will be immune to unexpected breaking updates.

* The fact that we pin versions in places like Gemfile.lock is totally fine. However, the fact that we pin versions in places like Gemfile is a smell. 

  * This discussion gets at the distinction: https://stackoverflow.com/questions/4151495/should-gemfile-l...

  * The use of left-pad as an example in OP was intended as an acknowledgement of the deployment issues you point out.

  * See my example of "exemplary library use" at http://arclanguage.org/item?id=20221
	
* Putting version numbers in Gemfile.lock does not accurately reflect the workflow where all upgrades happen with humans in the loop, who need to be aware of and initiate any change that happens in your dependencies' code.

* Version locking every dependency is identical to just inlining every dependency in the repo. And I actually like this approach a lot. See my example of "exemplary library use" at http://arclanguage.org/item?id=20221. 

* I don't love lockfiles, but you need some way deterministic dependency resolution.


### If you change behavior, then rename

* This is wrong. First, naming is hard. Finding good recognizable name for a package is hard enough, if each BC break would require a new one, we'd drown in names. Second, behavior breaks are not total. If RoR or ElasticSearch releases a new version with BC break, they do not stop being essentially the same package just with somewhat different behavior. Most of the knowledge you had about it is still relevant. Some pieces are broken, but not the whole concept. New name requires throwing out the whole concept and building a new one, essentially. It is not good for incremental gradual change.


### Using latest
	
* I work primarily in the enterprise Java world and I'd never just blindly compile in a dependency of LATEST and keep my fingers crossed. Any version bumps in my POM dependencies block are intentional. The transitive dependencies are hard enough to deal with already without having to worry if my dependent binary footprint changed significantly from the last build.
reply
	

### You can't trust semantic versioning

* Semantic versioning only "doesn't really work" because this is ascribing too much responsibility to it. 

  * Semver is a social protocol designed to assist in solving--but by no means completely solve--the technical problem of how to resolve dependency versioning. 

  * It only gives us the barest protocol to allow us to begin describing the problem, and permits us to attempt to build technical solutions on top of it. 

  * Nobody's pretending it's perfect, but if anyone has a better solution, feel free to propose it.

* "You can never be certain the maintainer follows semver". 

  * That's a bad argument. You can also never be certain any third-party code works at all, or did not change without changing versions, or does not have subtle bugs that surface only in your particular use case, etc. 

  * If you dismiss the whole concept of versioning on the basis that somebody could fail to follow it correctly, then you wouldn't be able to use any third-party dependencies at all.

* I agree with Rich Hickey that "semver" is an epic failure.

  * He even uses package managers as an example in a talk, saying, what if you had to worry about what "version" of Maven central you were using? 

  * Well, that's exactly what npm did (only in the client). How did I stumble onto this point? Because it broke the very first CI build I deployed to GitLab. Worked locally with Rollup 0.50.0, but Rollup 0.50.1 (which the CI used, because caret) introduced a regression that happened to break my package. So yeah, npm's default is not appropriate for CI. It assumes that patch updates are non-breaking, and we all know that they're not.


### Automated regression testing

* I think this is an excellent idea, particularly for typed languages. For a typed language package manager, you can run the following verifications:

  * for a patch version, all exported (name, type) pairs of A.B.C are identical to those of A.B.(C-1)

  * for a minor version, enforce first rule, but allow new types for new names.

  * I think the Elm package manager does this, based on other comments here in this thread.

  * Either way, that sort of automatic “exports” verification could be a base layer in a semver verification package manager, with integration tests layered on top.

* For C ABI, this exists since quite some time: https://abi-laboratory.pro/tracker/timeline/libevent/ (though, there is not really a way to enforce).

* There's complexity in practice. For example, in a language with function overloading and implicit conversions, adding an overload may silently change the meaning of existing code.
Sometimes, this can get very gnarly. In more complicated scenarios (e.g. involving inheritance), it is even possible to have code that silently changes its meaning in such circumstances, e.g. when overloading methods across several classes in a hierarchy. It'd be rather convoluted code, but not out of the realm of possibility.


### Debian versioning

* One approach is to convert names-and-version-numbers into identifiers used in package management systems. The convention is basically what Debian has always done for C libraries (putting the soname in the package name), so it's certainly a plausible way to manage things.

* If I stop using Debian some day, it will probably be because of that habit of pushing version information into part of a package's name.
It completely breaks upgrades. Newer versions aren't compatible with older versions, yet there's nothing telling apt that it should upgrade other stuff depending on the older version first. Also, who is to say wether foo5.3 is newer or older than foo5, and since foo5 depends on bar3.2, while foo5.3 depends on bar3.2a, what package do you have to upgrade first?

* libbar3 still has a package version of 3.2.1, libfoo5 still has a package version of 5.3.0. Debian putting soname’s into package names is actually quite handy as it allows side-by-side installation of multiple versions of a library with ease.

* It's very useful for applications that don't guarantee API or data format compatibility between major versions. If you were running PostgreSQL 8, then under no circumstances would you want a silent upgrade to PostgreSQL 9, because 9 would refuse to read the data storage files from 8 and not start up: PostgreSQL required the administrator to handle migration between storage versions.


### More comments, to be categorized

* "One user's breaking change is one maintainer's bugfix". This is a "grey zone" fallacy. From the fact that there might be disagreements on the margins, does not follow there is not a huge majority of clear-cut cases where everybody agrees that something is a huge or tiny change, and in this huge majority of cases it is useful. Even if there's no definition that would work in absolute 100% of cases, it works just fine in 99% of them.

* Semver is a wetware protocol, not a formal, mathematically certain concept. You also can never be certain that the car is not going to run through pedestrian crossing on red light when you step in - but it doesn't mean that traffic lights are useless. Semver is like traffic lights. For reproducible builds/increased certainty harden it with some kind of pinning or local copy.

* Elm enforces semantic versioning with its type system, and one can also print the diff of values/functions between separate package versions. In principle, this is doable.
reply
	
* API compatibility (showing that a package contains the same functions, which accept the same kinds of inputs and produce the same kinds of outputs) is only part of the problem-- it's harder to show that the functions actually do the same thing, but that's arguably more important than API compatibility. Typically, a compiler will catch a breaking API change; a behavior change will cause problems at runtime if a function suddenly starts doing something completely different.

* "It's harder to show that the functions actually do the same thing". Effectively impossible, with a Turing complete programming environment. (“Effectively,” because technically our physical computers have a bounded number of states and inputs, thus aren’t technically Turing complete, thus Rice’s theorem technically doesn’t apply.)

* There are tools like QuickCheck (https://hackage.haskell.org/package/QuickCheck) that can bombard the function with large amounts of random (but legal) input data and makes sure it's behaving.

* I've always felt versioning is more or less a form of marketing for your software. It's handy that people are kind of using convention to say things like "this is a HUGE change" vs "oops, bugfix". But it's really just conventions of communication, which, in a nutshell, comes with zero guarantee .

* Semantic versioning and automatic "transitive" dependencies sure are handy, especially in that "naive" phase of development. But once you start having to start tracking performance and security, it's time to turn off any magic update.

* When you've got people depending on you, you need to start putting your security hat on and assume that there can be a breach with every change, or your performance hat and assume the system will crash due to a performance problem. No way would I want any rebuild OF THE SAME SOURCE subject to change.

* Software versioning is a case of the XY Problem. We don't know what we want, but we know something that might help us get it (and we are wrong).

  * We don't care if you add behavior to your library (unless it fulfills a need we had or didn't know we had). 

  * What we really care about is if you take behavior away. And in some cases it's not even all the behavior. If I'm only using 20% of your library and you haven't changed it, I can keep upgrading whenever.

  * What semantic versioning is trying and failing to achieve is to make libraries adhere to the Liskov Substitution Principle (LSP). If you had a library that had sufficient tests, then any change you make that causes you to add tests but not change an existing test means that it should satisfy the LSP (tests that correct ambiguities in the spec notwithstanding). People can feel pretty safe upgrading if all of the tests they depend on are intact.

  * The difficult part of this 'solution' is that reusable code would have to have pretty exhaustive test coverage. I think we are already bifurcating into a world of library authors and library consumers, so I'm okay with this. We should feel comfortable demanding more than what we currently get from our library writers. In this world, what you version is your test harness, and not your code.

* Some projects do not allow changes (short of the repo itself failing). Some allow only withdrawing versions, but no changes.

* Some people confuse the (sometimes bad) behavior of package managers with version numbers.

* If a language affords module namespacing, the new library can be just a new require/import. 

  * Major versions aren't drop-in replacements. 

  * If the libraries are namespaced, you can conceivably use them side-by-side in the same codebase as you migrate. This is potentially very powerful. 

  * As a practical example, in one Java library I've been working on that has a long history, it's able to use various versions of JUnit in the same testing suite exactly because the different versions are namespaced. 

  * Another example at a different level is being able to install more than one version of, say, python or sqlite, on the same system. Some packages name the binaries with a suffix, e.g., python27 or python3, so you can include and have both of them on your path without conflict. When you install current versions of sqlite, the binary is explicitly sqlite3 so as to not be confused with older, incompatible versions.

* The major version number doesn't give you any additional information as to what has changed. You need to refer to the documentation, or the results of your own testing. 

* A major version number change is relatively actionable.

  * When someone says they want to migrate from Rails 4 to 5, the default response is “we should really consider doing that at some point and evaluate how much work it will take.” 

  * When someone says they want to migrate from Rails X to Django Y, the default response is “you’re going to have to justify a total rewrite”. 

  * This is even true for some brutal version changes: going from Python 2 to Python 3 is still typically less work than going from Python 2 to Go or Java or some other language. Angular might be an exception to this pattern.

* There are at least three levels where the names matter: the project (e.g., Rails), the artifact (e.g., a particular gem), and the module(s) (e.g., what gets referenced by require). They're each important in their own way. 

  * Artifacts are a way of referencing a collection of files, and often there's some way of embedding metadata about the artifact in the name of it, though that's accidental, and naming these artifacts is independent (or at least not necessarily dependent) of the collection it represents: the same file can be found in any number of artifact builds. I think the practical implication is that you're renaming what is being required and referenced in the code itself.

  * You have some understanding that a migration from Rails 1 to Rails 5 would be bigger than one from Rails 4 to Rails 5, and that would be a whole different kettle of fish than moving from Rails to Django (or even Sinatra).
	
* There's basically another part of the version-tuple that we compress into the same position as the "major" version: the number of complete codebase rewrites that the code has experienced. 

  * Often V1 and V2 of an application, especially, will have effectively nothing in common.

  * Often V1 and V2 will be developed by separate people who happened to inherit the name of the original, or "take up the reins" after the original became abandonware—but, either way, decided to just write a new one themselves rather than continuing on with the old. 

  * At that point, there is really no difference between calling it "libfoo (n + 1).0" and calling it "libbaz 1.0".

  * If you want to convey ancestry, you could add another preceding number, and that's often what people do—in the package name. For example, sqlite3 vs. sqlite4. 

  * If you wanted to make this "part of" the version tuple, you could: just have a super-major number before the major number.

* I've seen numerous times where a new version of a project is more similar to other projects than it's previous iteration. The reason is simple: the field has progressed. 

  * A great recent example is that DirectX 12 is more similar to Vulkan, than it is DirectX 11 or OpenGL 4. I would actually say it's quite common. 

  *  Now certainly there are new "major" releases that are simply breaking changes with no fundamental changes, but I think it depends on your field: native libraries rarely change because it's well worn, the web changes every few days it seems like. 

  * But my point is that if we should distinguish between: conceptual changes, breaking changes and the iteration number (e.g. non-breaking changes and patches), and we want to simplify it, then we should merge breaking changes and conceptual changes, as described by the article.
	
* Taking the latest package every time you're building for production deployment is a bad idea. 

  * And version pinning in and of itself doesn't really solve this problem either: if you're relying on an external source for reproducible builds, you've got a potential point of failure. 

  * To have a reproducible build you need to have access at the time of the build to the specific artifact you want to include in the build, which at the end of the day means having that library at hand locally as part of your build system. 

  * We don't have great stories yet about how to maintain these artifacts locally: setting up your own repos for many ecosystems isn't a walk in the park.

  * Specifying a version does allow you to know which artifact to include, but that's at the artifact level, which often is conflated with the particular library that artifact provides. A build number or other included in the artifact gives you the same selection properties, yet is independent of the code the artifact actually provides. Currently code and artifact are labelled similarly, but that's more a purposeful accident rather than a guarantee or deterministic property.

  * As for the package-3, package-4 thing you mention, I agree, it's kind of a mess. I think that's more a result of this lack of separation between the code and the artifact (if I'm understanding you correctly), and module (what is referenced in the code itself namespacing and aliasing. I don't think it's a solved problem.

* We need upgrades to be easy to perform so that we'll be more likely to perform them and so that we'll perform them more often, thus keeping our projects up-to-date on the latest vulnerabilities and bugs.

* Version pinning isn't mutually exclusive to running latest, head, or whatever you call now. You can still resolve now to some version. For example, Debian stable resolves to version 9. Stable points to the dependency tree called version 9, but you can also just reference it directly if you want. You can go back in time and reference dependency tree 8 if 9 was a doozy.

* Linux distributions have been including the major version in the name for a long time; when we started doing this at Red Hat I wrote down the rationale at http://ometer.com/parallel.html in order to convince upstream projects to do this upstream, as many now do. For non-system package managers like npm, always using a lockfile is the best current option IMO, for those package managers that support it. https://blog.ometer.com/2017/01/10/dear-package-managers-dep...
	
* Windows 8 went straight to Windows 10, without any Windows 9. 

  * The alleged reason for skipping windows 9: many software packages were checking for older Windows versions (namely 95/98) by checking if the Windows version string starts with "Windows 9"

  * That explanation ignores the fact that windows spoofs the windows version number for all applications by default (defaults to 8.1 i think), unless you have a special entry in your application manifest. so old applications will still work by default. not to mention that windows has compatibility shims to deal with this exact issue.

* NPM for Node.js defaults to version tagged "latest". The "default" in NPM is to use `npm install --save`, which adds the version of the package it installs to your `package.json` so future uses of `npm install` will automatically use a semver compatible version of that package.

* Barring total ecosystem overhauls (Python 2 vs. 3, AngularJS vs. Angular), people don't spend any effort wondering which version of a package they should use. In my experience, you install the latest. You may pin (or let yarn.lock do it for you) to avoid implicitly consuming a breaking change the next time you install from scratch, but that's to be able to explicitly test if the upgrades break your application. It's not likely because you presume the newer version is worse than the one you already have.

* I've seen even point releases break something. For proper ci/cd you always lock to exact version, and run upgrades purposefully (say on a schedule). Last thing one needs is to hunt for breakage when a dependency changes unexpectedly.

* You wouldn't let your teammates commit code without review, why trust a 3rd party... (Not saying need to look at diffs of third party libraries)

* It's similar for Bundler (the de facto official Ruby package manager). After the initial install, you'll only get new package versions if you run the update command.

  * The complaint seems to be that the update command is not sufficiently safe by default (though the `--conservative`, `--minor` and `--strict` flags help there), which is fair enough, but why not just fix the default behavior?

* Doing `npm update` will only update to the latest version that matches the selector in your `package.json`. So if you ran `npm install --save` and it wrote 'foo@^1.2.3', `npm update` will not update to release 2.0.0 which includes breaking changes, but will update to 1.2.5 which includes fixes. The ^ symbol is the default which will allow new features and fixes, but not breaking changes. You can optionally set '~' on a conditional basis or npm-wide default for fixes only, or pin packages only if that's your fancy. But the default seems pretty sensible in my opinion.
reply
	
  * The behaviour of package-lock.json was changed in npm 5.1.0 by means of pull request #16866. The behaviour that you observe is apparently intended by npm as of version 5.1.0.

  * That means that package.json can trump package-lock.json whenever a newer version is found for a dependency in package.json. 

  * If you want to pin your dependencies effectively, you now must specify the versions without prefix, that means you need to write them as 1.2.0 instead of ~1.2.0 or ^1.2.0. Then the combination of package.json and package-lock.json will yield reproducible builds. 

  * To be clear: package-lock.json alone does no longer lock the root level dependencies!

* Names for libraries serve a dual purpose: they must be resolvable by computers (i.e. package managers, linkers, etc) but also meaningful to humans -- the programmers who supply the name as user input in the first place. 

  * Version numbers allow one to trace the provenance of later artifacts back to earlier ones somewhat linearly, instead of having to hunt for comparable artifacts unconnected by graph edges.

  * If libraries upon major version changes were renamed instead, the programmer would have to maintain a mental mapping between the predecessor library and the successor, and all code and configuration references to the other artifact would have to be updated 'by hand', without assistance to be gained from the package manager.

  * Including multiple versions of a library in your project is unsupported in many environments, so if the library author ever envisions a situation where the two 'versions' will be used concurrently, renaming makes sense. In the Java ecosystem, this approach was used for Apache Commons Lang, Apache HTTP Client, and Jackson.

* It's strange to extoll the virtues of Go's dependency management, because by convention it binds the artifact identity to a resolvable external URL without any indirection. 

  * Meatspace maintenance changes like changing the hosting location of the artifact will cause Go to treat it as a different package, and inability to directly communicate externally will also break your build. 

  * Every other packaging solution has independently come to the conclusion that abstracting away artifact identity from artifact location is a good thing.
	
* Basically the perl/CPAN model is: Don’t break backwards compatibility unless you absolutely have to; as in cases of security or when the original functionality never worked in the first place.

  * See https://perldoc.perl.org/perlpolicy.html#BACKWARD-COMPATIBIL... that’s the policy for the language itself, but as it mentions there it’s considered generally a “community” virtue. I wasn’t programming back in 02 so I’m sure it was extremely different (and the source I’ve read from modules from that era is pretty frightening. Any major framework or generally used module will try to maintain backwards compatibility at least as far as documented behavior. I’ve never come across the kind of stuff I see all the time with node and go and python; package maintainers changing parameters and such because they weren’t happy with the original API.

* Version numbers are what you expose to the real world, something which semantic versioning tries to standardize. I shouldn't have to care if you use git, hg or copy-paste-versioning internally.

* I feel like this works reasonably well uusing Java: https://github.com/nebula-plugins/gradle-dependency-lock-plu...

  * You specify what version constraint you want for each dependency (latest.release, major.+, major.minor.+, a specific version number...).

  * The tool does the work of resolving those constraints to specific artifact references, which you commit to your repo in the form of a dependency.lock file that gives you reproducible builds. 

  * You can then resolve new artifact versions with `gradle generateLock`, or revert it to a previous commit if there is a breakage.

* The main reason for trying to jam everything into three numbers of "semver" is that otherwise you get total freeform chaos. 

  * Look at Debian/Ubuntu, where you get things like "30~pre9-5ubuntu2" and "5.1.1alpha+20110809-3" and "1.5.0-1~webupd8~precise".

* When a major change is made to something, as in a breaking change, where you as the developer are making the conscious decision that you will cause other people's shit to break if they keep pulling "latest" I absolutely think that the major version number should just be part of the naming scheme, not the versioning scheme.

  * We've actually implemented a similar system where I'm currently working. For versioning APIs, if you want to bump a major version then we force developers to start working with a whole new git repository, a whole new pipeline etc. If we have to patch some defect fix back to the old version, we can just cherry pick across forks, but most of the time we want to manage the life cycle of two bits of development that no longer do the same thing as being exactly that: bits of developed code that do different things.

* Rich Hickey points out that the convention of bumping the major version of a library to indicate incompatibility conveys no more actionable information than just changing the name of the library.

  * Roughly speaking, the name identifies the project.

  * Roughly speaking, the purpose of the software doesn't change with major versions.

  * Roughly speaking, the people working on the software don't change with major versions.

  * Both of which are, arguably, more important than raw compatibility.

* Steve Losh points out that if version numbers were any good, we'd almost always be looking to use the latest version number.

  * No, we wouldn't. When I build a piece of software, I build it with a particular version of its dependencies. Then, I test it with those versions. After I've tested it, I absolutely do not need changes in the underlying software changing how things work. Compatibility is never perfect.

* I'm really fond of how Google does versioning in their internal codebase (from the public information about it that I know).

  * They have a monorepo containing a single version of every library. 

  * When updating something, you're supposed to take into account all the places it's being used, and avoid breakages. 

  * Tests and tooling play an important role in making it possible.

  * I really want something like that for the open-source world, instead of having to deal with maintaining multiple versions, and dealing with the complexity of interactions between the all the permutations of different versions.

* At least in the Linux world, this is part of the value that Linux distributions can offer. 

  * Distributions like Fedora and openSUSE tend to lead the pack here by actively testing updates before they're merged into the distribution repositories, and developing tooling for supporting continuous verification of everything working together.

  * Cf. Koji+Bodhi for Fedora, Open Build Service for openSUSE, and the OpenQA instances for both distributions.

* I've examined the same issue between reproducible builds vs semantic versioning while relating it to Nix. This post also shows how to use Git submodules to achieve something similar. https://matrix.ai/2016/04/04/content-addressed-dependencies-...
reply

* The Dhall language has an interesting take on dependencies where each module can be a content addressed code supplied via ipfs https://github.com/dhall-lang/dhall-lang

* I once wrote a fairly extensive post on REST API versioning techniques with similar points (lost my backup so wayback machine): http://web.archive.org/web/20160407080723/http://stucharlton...

  * In short, we really seem to have forgotten the power of backward and forward compatibility because we assume a very short lifespan for our services (months, not decades).

* I just use major+SHA. 
  
  * Major changes mean you are breaking the API.

  * The SHA is what code built it-- not a git commit hash SHA, but a hash tree of the actual source.

* Semantic versioning is definitely more of an art than a science. I wrote an article about it a while ago https://hackernoon.com/its-not-a-bug-it-s-a-feature-the-prob...

* I'm not totally sold on the heavy BDSM type systems, but one of their clear advantages comes from trivially handling this problem. Elm's package manager simply won't let you publish an incompatible update without bumping the version, all handled by type check.

* People using semantic versioning when its totally unnecessary is a peeve of mine. Maybe if youre releasing an api or something it makes sense (I dunno, I dont do that) I make games and just number my releases / versions 1,2,3,4,5,6 etc.

  * You can also do this with semantic versioning; I have indeed seen people who, when forced to use semver, just do 1.0.0, 2.0.0, 3.0.0, 4.0.0... It's completely valid, because you, as the package author, are given ultimate freedom by semver in determining when major version bumps are appropriate.
	
* Semver is marketing, enumerating builds is engineering.

  * Sure, semver should be associated (pinned?) to build numbers. Invariant. Immutable. Never changing.

  * Semver is the public face. But internally, I only care what build number someone is talking about.

* The Rust ecosystem might be a demonstration of the inverse problem. 

  * Pinning versions is trivial in Rust because its default. You will never be pushed over a major version boundary without intentionally doing it (and "always get the latest" is just the "" version).

  * The problem then becomes that crates never reach 1.0. Flask is probably the oldest piece of software I use that similarly never reached 1.0 - albeit its been pretty close ever since the project was reorganized last year - but the entire Rust ecosystem is buried in Flasks. Software that is 99% of the way to what the creator wants 1.0 to be, but the last 1% is something nobody wants to do. When you are 99% of the way there, the software works in 99% of use cases, and that last 1% is... the last 1% anyone wanted to do. Which means they didn't want to do it.

  * Probably the most blatant example of this in Rust is regex, which is blessed crate that is still only at 0.2, among many others (just from my hobby Rocket project are base64, chrono (and time), cookie, dotenv, envy, error-chain, lazy_static, num-traits, rand, redis, and uuid all still in version 0.x. That being said, the impl period going on right now and this entire year of direction from the core team was explicit in bringing as much of these fundamentals to 1.0, so we will see how successful the effort is.

  * Probably the greatest problem with semantic versioning in that context becomes feature creep. It is scary to go to 1.0 - you feel free in 0.x land where you can make major breaking changes in a .x release whereas you need to increment that scary major version number to do it later on. And besides the fear there is a lot of logistical headache properly maintaining semantically versioned software - you should expect if you never release a 2.0 to have someone trying to contribute 1.x bugfixes down the road, with the need to release 1.x.y bugfixes for all time. Because 2.0 is, like the article says, a new library - its a different API, you changed the meaning. So you are now maintaining two libraries.

  * There is also one final fear with the way Rust setup its crates ecosystem - if you are bold and break things but don't end up delaying 1.0 for way too long, you might end up incrementing the major version a bit. And there is a cultural and subconscious aversion to anything on crates.io you see at version 3.0 or heavens forbid 4.0 or more. That software is unreliable, the developer is changing it all the time!. But then you go and use a 0.15 crate that is having the same problem anyway, just without saying "this probably does its job" like a 1.0 can.

  * In the end, versioning truly is almost meaningless, even in an enforced semantic versioning system the intent breaks down and meaning is lost just because different people release software differently. But that is a real deep almost - because its still more information than not having it, and in Rust right now at least it gives more helpful information than not. I'd call it a success at that point - way more than say Linux, where the major version is incremented whenever Linus wants to run a Google+ poll...

* As far as I understand, the situation with 0.x.y libs on crates.io is working-as-intended; a major version of 0 denotes libraries that the author does not yet want to commit to having a stable interface (and may not ever want to commit to such), which is crucial information for any potential users of that library. 

  * The fact that there are many such libraries can be attributed to the fact that having a stable inferface was impossible before Rust stabilized in May 2015, and so most libraries are exceedingly young (even disregarding those packages which see one aspirational release and are then forgotten, which make up the bulk of every package repository for every language regardless of versioning scheme :P ).

  * If the situation were to never improve that would indeed be concerning, but the Rust devs acknowledge the concern have been striving towards 1.0 for all of their "blessed" libs (e.g. regex, which you mention, is having its 1.0 release imminently) in order to help encourage people who might be on the edge to take the plunge.

* I get the goal of attempting to increase accountability via tweaking expectations of package managers, but that ignores the deeper problems limiting software quality that cause this whole situation in the first place. Consider this quote: "Packages that wantonly broke their consumers would "gain a reputation" and get out-competed by packages that didn't [...]"

  * 1. That there's a large enough competitive field for any given package that one that publishes a "bad" release can be "punished" by being overtaken by competitor(s). This is weird on so many levels. The costs to switch to a competing library can often be quite high, more than just fixing the current one. Related, there's often only one viable library choice in a given domain, which implies both zero competitors and a high cost of using an alternative (whole-cloth rewrite).

  * 2. QA is expensive: time to maintain automated tests, time to run any needed ad-hoc testing, time and experience to develop the library in a really robust way. But this proposal seeks to put in a competitive incentive that would fragment efforts to solve the same problem. So there's this assumption that the publisher is only ever just incompetent, not starved for resources to implement great software quality. A major success of open source packages is that they enable consolidation of effort (i.e. a community of contributors).

  * 3. Sometimes a library is used in contexts that weren't foreseen by its authors, so a "breaking" change for some consumer is accidentally introduced. This may have happened before a package consumer strongly adopted it. This dashes up against an implication in the article that breakage is only ever introduced, vs. already existing. Packages are never in some pristine state of function and concept. Except for some kinds of simple packages, they're often ongoing, living exercises in understanding the real-world problem domain and applying software techniques to the solution.

  * Overall, I think saying that modern package managers "default" to unpinned is a straw-man: no sane package manager is intended to be used this way except for a few minutes after setting up a new project. "bundle install" that first time and now you've got a Gemfile.lock. The next time, you get the same packages as the prior install. Likewise, "bundle update" is an operation that should be dirtying your source tree, requiring the usual passes of building, testing, and other software quality process before it lands. AFAICT, nothing in this proposal would ever change these steps: initial package adoption, and software change evaluation. If there are problems with upstream package quality, those root at deeper issues of "why software quality is hard" rather than being the fault of current package managers. It's difficult to see how our package managers could influence this real-world social problem at all.

* Rich Hickey points out the convention of bumping the major version of a library to indicate incompatibility conveys no more actionable information than just changing the name of the library.

  * Unless, you know, you care about the purpose or authorship of the library.

  * I don't think pinning dependencies at package levels is a bad idea - ubuntu does this for a lot of packages but this relies on properly followed semantic versioning to work.
	
* How do you encode development versions? Alphas/betas/rcs? Versions are structurally more complex than a simple tuple, and they're strings b/c that's a convenient and easy representation.

  * You can't just tell people to not have alphas, etc. 

  * Sometimes I need to test stuff on branches, so it can't have a normal, mainline version number; I need to signal that this is a proposed, test version, etc.

* How to get a package manager to provide the sane default of "give me the latest compatible version, excluding breaking changes."

  * You have to actually notate enough information for the package manager to determine "latest compatible version, excluding breaking changes". E.g., in Cargo, you'd need to say something like ^1.2.) But being able to pull down the absolute latest is useful in a new project; I'll often use "dep" to start, get comfortable, and then restrict that to dep = "^3.2" or whatever.

* The whole situation with language package managers like Ruby and Node make them completely inappropriate for end users and deployment.

  * Many apps merrily pull in hundreds of dependencies. 

  * Some dependencies need a complete build environment, others may require specific versions that quickly lead to fragility, dependency hell and thousands of wasted hours.

  * These kind of package managers only make sense and see maximum use in SAAS type apps or dev centric environments explaining perhaps why some devs do not realise the issue. 

* What we need is good, language independent, tooling to automatically select which versions to use, how risky the update is going to be, what problems are being fixed.

  * Tracking and correlating successful and failed updates centrally - call it "distributed CI".

  * Tentatively upgrading dependencies, running tests and rolling back on error.

  * Don't forget that even minor, bugfix releases introduce bugs or break code by actually fixing bugs that people were inadvertently relying upon.

  * Lack of automated tools only encourages the "vendorize, ship and forget" model that is so popular.

  * The NixOS community is building towards this with hydra, ipfs, nixpkgs.

* It's true that using version ranges is incredibly dangerous. My take on it: https://www.lucidchart.com/techblog/2017/03/15/package-manag...



