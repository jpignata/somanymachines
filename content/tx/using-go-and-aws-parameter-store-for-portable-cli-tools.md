---
title: "Using Go and AWS Parameter Store to Build Portable CLI Tools"
date: 2019-05-22T09:00:00-04:00
type: post
dek: "How do you keep all of the random scripts you use working across systems?
Store and share your configuration securely on AWS's dime."
---

**In January I made** a small list of productivity-related New Year's
resolutions to tackle in 2019. This list included random items such as switching
to right-handed mousing (as peripherals manufacturers continue to ignore [my
cohort in the market][0]), moving from Ruby to Python for general purpose
scripting (which is for another post), and consolidating the various scripts I
use across different systems to portable tools that can be used on any system
without any setup. This last resolution was motivated by the pain I felt
switching laptops at work. All of the little scripts I had littering my home
directory were gone, and moving them over revealed an invisible substrate of
dependencies and assumptions.

In my effort to automate repetitive tasks, my system incidentally accrued
libraries, configuration, and random settings that silently made all these
scripts work. Writing these scripts with expediency in mind, I didn't spend any
time jotting down documentation so their configuration was only repeatable if I
happened to recall the incantations I ran or if the error messages were
mercifully obvious. Whatever laptop I was using ended up becoming a graveyard of
state. I wanted someway to clone a repo, run a build command, and have all of
the tools I need without any runtime installation, gem bundling, or other yak
shaving.

Enter [Go][1]. Go shines for this task. As long as you have the Go toolchain
installed, you can repeatably build little tools across different architectures
without worrying about runtime versions or gems or npm packages or the like. [Go
modules][2] introduced in 1.11 adds versioned packages to the build process
which allows you to refer to specific dependencies that Go will fetch prior to
build. This addressed the software prerequisites component of my local state
problem. All of the tools I've put together share a single repo called
[toolbox][10] which has a `Makefile` that builds each executable and installs
them in my home bin directory.

The next step was to eliminate or centralize individual configuration data for
each tool. The requirement I had in mind was to able to use the tools
immediately without setting up multiple configuration files or copying things.
At the same time I didn't want to store things like access tokens in source
control. For example, I often [Gist][3] files to share them with others so I
have a small script that will accept a filename as an argument or contents from
STDIN and return a URL. This requires a GitHub personal access token with the
ability to create new gists. Previously, my approach was to have a dotfile
called `.github` where the token would be the payload. Each time I wanted to use
the script on a new system, I'd either have to create a new personal access
token or copy the contents of the file. Friction.

\
**[AWS Systems Manager][4] is a hodge-podge of** services built to help users
administer AWS resources such as Amazon EC2 instances. One particularly useful
feature in Systems Manager is called [Parameter Store][5]; it provides storage
for configuration items that can be optionally encrypted via AWS Key Management
Service (KMS). These configuration items can then be retrieved via API calls
using AWS IAM authentication. As a bonus, all calls to Parameter Store are
logged in [AWS CloudTrail][12] which captures an audit log of every call to your
configuration items with information about who requested it, when, and with what
credentials.

The best part? It's totally free.[^6]

While not intended for this purpose, Parameter Store is an ideal place to keep
configuration for local programs that are run on multiple Internet connected
computers. The [AWS SDK for Go][9] makes it dead simple to use Parameter Store:

{{< highlight golang >}}
package config

import (
	"github.com/aws/aws-sdk-go/aws"
	"github.com/aws/aws-sdk-go/aws/session"
	"github.com/aws/aws-sdk-go/service/ssm"
)

// Get returns a decrypted string value from AWS Systems Manager Parameter Store.
func Get(name string) (string, error) {
	sess := session.New()
	svc := ssm.New(sess)

	output, err := svc.GetParameter(
		&ssm.GetParameterInput{
			Name:           aws.String(name),
			WithDecryption: aws.Bool(true),
		},
	)

	if err != nil {
		return "", err
	}

	return aws.StringValue(output.Parameter.Value), nil
}
{{< /highlight >}}

This will use AWS CLI's configured credentials which reduces the state problem
down to a single piece of configuration to put in place across all systems.
Since the SDK can use any credentials the AWS CLI can use, these credentials can
be temporary, based on an IAM role, and require multi-factor authentication for
additional security. This package can be shared across all of the CLI tools and
once configuration parameters are added to Parameter Store, they will be
accessible from any computer I use. While this has the downside of requiring
Internet access, practically speaking, the tools that are require this kind of
configuration also interoperate with Internet services.

Parameters and be managed via the AWS Management Console or the [AWS CLI][12]:

{{< highlight golang >}}
read -s value && aws ssm put-parameter --name keyName --value "$value" --type SecureString --overwrite
{{< /highlight >}}

Now in tools where I need this piece of external configuration, I can call
`config.Get("keyName")` and retrieve the data instead of reading it from a local
file or environment variable.

Some examples of tools I use which use Parameter Store to share some kind of
authentication token:

**gist**

Create a Gist from files or STDIN and return a URL.
 
{{< highlight console >}}
$ gist -f 404.html -f 500.html -d 'some HTML files'
https://gist.github.com/jpignata/76d48c3caed4066fcf68e433f4a45861

$ nc djxmmx.net 17 | gist -n fortune.txt -d 'output of `nc djxmxx.net 17`'
https://gist.github.com/jpignata/76a982548dfc033e3e885d441353970c
{{< /highlight >}}

**bitly**

Shorten the given URL and return a bit.ly link.

{{< highlight console >}}
$ bitly www.google.com
http://bit.ly/2WHdVg5{{< /highlight >}}

**aoc**

This winter I binged the last couple of years of the [Advent of Code][11]
puzzles and found copying and pasting puzzle input to be tedious. This little
CLI grabs my user-specific input using a session token stored in Parameter
Store and writes it to STDOUT.

{{< highlight console >}}
$ aoc 12 2018 | tee input.txt
initial state: ##.#....#..#......#..######..#.####.....#......##.##.##...#..#....#.#.##..##.##.#.#..#.#....#.#..#.#

#.#.. => .
..##. => .
...#. => .
[...]
{{< /highlight >}}

\
**Parameter Store is a handy** service for non-critical configuration items that
you want to store and centrally access. It provides an audit log, encryption,
and is very straightforward to use. Squirreling shared configuration here, I can
continue to write small utilities in my toolbox repo as I come across repetitive
tasks and know that the next time I migrate to a new system everything should
just work.

[0]: https://simpsons.fandom.com/wiki/The_Leftorium
[1]: https://www.golang.org
[2]: https://github.com/golang/go/wiki/Modules
[3]: https://gist.github.com
[4]: https://aws.amazon.com/systems-manager/
[5]: https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html
[^6]: Last month, AWS curiously introduced [advanced parameters][7] which cost a nickel a month and provide more generous limits for size and history. Standard parameters are still totally free as in beer. This isn't to be confused with [AWS Secrets Manager][8] which provides some overlapping functionality with added features around secret rotation, AWS service integration, and cross-account access, but costs around $0.40 a secret and five cents per 10K API calls.
[7]: https://aws.amazon.com/about-aws/whats-new/2019/04/aws_systems_manager_parameter_store_introduces_advanced_parameters/
[8]: https://aws.amazon.com/secrets-manager
[9]: https://github.com/aws/aws-sdk-go
[10]: https://github.com/jpignata/toolbox
[11]: https://adventofcode.com
[12]: https://docs.aws.amazon.com/cli/latest/reference/ssm/index.html
[13]: https://aws.amazon.com/cloudtrail/
