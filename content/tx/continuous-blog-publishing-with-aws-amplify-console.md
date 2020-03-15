---
title: "Continuous Blog Publishing with the AWS Amplify Console"
date: 2020-03-14T00:00:00-04:00
type: post
dek: "\"Yeah, but it's easier just to FTP files to my server!\" Using the AWS
Amplify Console, I setup a pipeline for this blog in a couple of minutes,
awkwardly leaving me with no excuse but to do the right thing."
---

**On my growing TODO list** there's a section I call [The Great Big Beautiful
Tomorrow][1]. It's the subset of tasks that would undoubtedly
save time or be valuable to do, but continually get pushed down the stack due to
priority starvation. Sometimes I check in on them briefly,
and then guiltily scroll away before I make eye contact. They are prisoners of
my good intentions and I keep them in my [Katamari][2] of a TODO list,
collecting dust. Toward the bottom, there's a gaggle of blog-related items
including one that reads "write blog posts." Yeah. Shut up, TODO. I'll get to it.
Somewhere below that another reads: "setup a publishing pipeline for the blog."

A [deployment pipeline][3] is an automated manifestion of the process for
getting software (or, in my case, rubbishy content) from a version control
system into the hands of customers. I'm a big fan of [AWS CodePipeline][6],
[AWS CodeBuild][5], and friends: AWS services which help developers to quickly
create [Continuous Delivery][4] pipelines without managing servers or
[Jenkins][7] or whatever.  I had the rough outline of what I would do mapped out
in my head. I'd create CodeBuild project with a `build.yaml` that executes
`hugo`, create a pipeline in CodePipeline to monitor my GitHub repository, build
the site, and then copy it to Amazon S3. It would take an hour to setup
perhaps, and save me from clumsily trying to remember the AWS CLI incantation to
synchronize my repo with the S3 bucket. It would be auto-magic! The future is
bright and wonderful.

But, ehhh. I guess I'll need to put it in some kind of [AWS CloudFormation][8]
template. I'll probably need to look up the syntax. That sounds boring. And
also. The Internet! It has so many fun things to read and do and watch. What I'm
saying is: if the Internet wasn't fun, I probably would have gotten this done
but it is, so I didn't. Instead, after my infrequent blogging, I did the CLI
futzing and often got the files copied. It is a task that supports inertia by
being annoying but not painful or time intensive enough to fix properly.

\
**Last week I joined** the [AWS Amplify][10] team working on the [Amplify for
Android framework][10]. Part of Amplify's suite of products is the [AWS Amplify
Console][11] which provides hosting for fullstack serverless applications. It
connects to repos on GitHub, Bitbucket, or GitLab, runs a build, and deploys the
result to Amazon S3 behind Amazon Cloudfront for content delivery. Plugging in
my blog repo in the console, I was blown away to see it create a full publishing
pipeline for my blog. Not only did the console detect that the blog used Hugo as
a static site generator, but when I hit *Save and Deploy*, it published the
content of the site in less than a couple of minutes. A few more mouse clicks,
and I had a free SSL certificate from [AWS Certificate Manager][13], and rewrite
rules in place.  Since I host my domain in [Amazon Route 53][12], the Amplify
Console was even able to automatically create the CNAME and ALIAS records to
wire the site up to production. Now everytime I pushed to my master branch, my
blog would be immediately deployed without any additional steps.

Without intending to, I had unwittingly crossed an item off my TODO list. That's
lifehacking, friends.

![](/images/continuous-blog-publishing-with-aws-amplify-console/app.png)

The service is built around the Git workflow many developers use. A simple `git
push` results in a production deployment without any fuss. In our
slightly-kind-of-dystopian world of [Kubernetes clusters and masturbatory
complexity][16], it's a glass of ice water in hell. I don't have to think
about servers, clusters, `scp`'ing something somewhere, command line options to
arcane CLIs, or anything beyond `git push`. More of this please.

So what's the catch? [AWS Amplify Console is priced on a
pay-as-you-go-basis][14] along the dimensions of build minutes, storage, and
data served. The free tier provides 1,000 build minutes, 5GB of storage, and
15GB of data served. Not only did I accidentally fall into a more efficient
deployment process, I also saved some cash as now this blog is hosted absolutely
free.

But wait! There's more! AWS Amplify Console meets developers where they are by
helping to enable the code review component of their Git workflow. Through a
feature called [Web Previews][17], pull requests are built and deployed to their
own URL for people to inspect for QA of the actual rendered content versus
reviewing Markdown files. Through an integration with GitHub, pull requests have
an AWS Amplify Console Web Preview check that builds and deploys content when
the PR is updated.

![](/images/continuous-blog-publishing-with-aws-amplify-console/pull.png)

Within *Details*, the link to the fully rendered site can be found:

![](/images/continuous-blog-publishing-with-aws-amplify-console/browser.png)

This allows reviewers to see the final product and provide more meaningful
change reviews. Preview sites can even be optionally password protected to
prevent prying eyes or issues with duplicate content.

\
**AWS Amplify Console supports other** static-site generators besides Hugo such
as Jekyll, VuePress, Gatsby, Eleventy, and any kind of static sites using
frontend frameworks like React, Angular, etc. I can't say enough good things
about it, but since I'm on the payroll please don't take my word for it.
[Getting Started][15] is easy and free. Just connect your repo and you'll
be continuously publishing.


[1]: https://en.wikipedia.org/wiki/There%27s_a_Great_Big_Beautiful_Tomorrow
[2]: https://en.wikipedia.org/wiki/Katamari_Damacy
[3]: https://martinfowler.com/bliki/DeploymentPipeline.html
[4]: https://martinfowler.com/bliki/ContinuousDelivery.html
[5]: https://aws.amazon.com/codebuild/
[6]: https://aws.amazon.com/codepipeline/
[7]: https://jenkins.io/
[8]: https://aws.amazon.com/cloudformation/
[9]: https://aws.amazon.com/amplify/
[10]: https://github.com/aws-amplify/amplify-android
[11]: https://aws.amazon.com/amplify/console/getting-started/
[12]: https://aws.amazon.com/route53/
[13]: https://aws.amazon.com/certificate-manager/
[14]: https://aws.amazon.com/amplify/console/pricing
[15]: https://console.aws.amazon.com/amplify/home?#/create
[16]: https://circleci.com/blog/getting-your-manager-to-say-yes-to-devops-tools/
[17]: https://docs.aws.amazon.com/amplify/latest/userguide/pr-previews.html
