# Postmortem

![Flogging a dead horse](i-dont-like-efkf3y.jpg)

## Issue Summary

We had just released a new feature to our recently launched Raw Berries site that we had our first intake of users complaining about the site. 10 minutes after we performed a feature update, we started receiving emails from our users talking about "they are unable sign in or sign up to our platform". It was quite surprising to us because we knew it worked on our machines and it worked before. About 138 of such emails came to our inbox. It was an storm of emails. Knowing how hard it can be to attract and keep users, we couldn't afford to lose 138 of our users in that way and decided to take a closer look at the problem. We cloned our site's repository from GitBug, followed the installation instructions on the README and to our surprise the site couldn't startup. It wasn't long before we realized that the cause of the problem was failing to update the requirements for our project. The site was malfunctioning from 10:50 AM PST to 12:20 PM PST.

## Timeline

+ 05-02-2022 10:50 AM PST - A customer complained that they couldn't sign in to the site.
+ 05-02-2022 11:20 AM PST - Razafia, one of our backend developers, experienced the same issues our customers reported.
+ 05-02-2022 11:35 AM PST - We investigated the controllers and the views for inconsistencies.
+ 05-02-2022 11:40 AM PST - We assumed the bcrypt (one of our site's dependencies) gem being used was either at fault or used incorrectly because the error message on the site showed that the bcrypt gem was raising an error over an invalid hash.
+ 05-02-2022 11:42 AM PST - We checked that the views might not be binding the form fields to the right model fields, which later turned out to be false.
+ 05-02-2022 11:45 AM PST - We were misled by thinking that our controllers might be creating a different hash for a valid password of the site's admin.
+ 05-02-2022 11:50 AM PST - Razafia thought the issue might have been that the password was not properly hashed.
+ 05-02-2022 12:00 PM PST - The incident was escalated to the backend development team.
+ 05-02-2022 12:20 PM PST - The incident was resolved by updating the requirements (the bcrypt gem version) for the backend server.

## Root Cause And Resolution

The version of the bcrypt gem we used was outdated. It was raising an error over a hash that was clearly valid and matched what was stored in the database. It could be that the hash we were creating was not supported by the version of bcrypt we had installed. Razafia fixed the issue by manually updating the version of bcrypt in the Gemfile.lock file to a more recent version and reinstalling the required gems, and it worked like a charm.

## Corrective And Preventative Measures

+ Setup a continuous integration pipeline to run a build on each pull request branch. This would ensure that builds are passing in the pull request branch before it is merged with the deployment branch.
+ Setup a monitoring system for the database and application servers to keep track of any issue that may occur.
+ Develop tests that need to be passed for each new feature and those tests should be passing before they are merged with the deployment branch.
