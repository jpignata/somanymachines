---
title: "Finger: The First Social Software"
date: 2019-05-18T12:00:00-04:00
type: post
dek: "One of the first examples of social software in an internetworked
environment still ships with your operating system. Let's connect 1972 to 2019."
---

In the early seventies at the [Stanford Artificial Intelligence Lab][0], [Les
Earnest][1] built a small utility to help people with disparate schedules find
each other for both professional and social purposes. Prior to his program
`finger`[^0], users would run a version of `who` to figure out what users were
logged in and what physical terminal they were using by mentally mapping cryptic
user IDs to people and line numbers to physical terminals in the lab. Building
on `who`, Les wrote finger to provide a richer display of this information by
mapping user IDs and ttys into peoples' names and physical places.[^1]

Somewhere down the road, `finger` became a [service][3] which allowed foreign
systems to query user status across a network allowing users to peek in on one
another to ascertain whether or not somebody was available. The program took on
more features as software is wont to do. One feature allowed users to know if a
remote user had unread mail, providing a primoridal read receipt. Another
allowed users to publish free-text by writing to a `.plan` or `.project` file in
their home directory to indicate their upcoming availabiity or project status
respectively, a la out-of-office messages and microblogging.

Presaging features that would become de rigueur in both productivity
applications and social software, `finger` existed in a world of early
networking where exposing the full names and email addresses of users was both
convenient and the privacy risk introduced thought acceptable. By the late
1990's, the risk profile had changed with a growing Internet user base and
several [security incidents][11], causing `finger` to lose momentum. Today
services no longer expose a `finger` server, but in the pre-commercial, shell
account era, `finger` was widely used to connect with colleagues and friends. If
you attended college during this time `finger` was the social network. Amongst
his inventions Les Earnest lists:

>  Social networking and blogging service (FINGER, 1972), which got ripped off
>  by the corrupt Facebook, which I aim to destroy.[^2]

The Finger User Information Protocol was last outlined in [RFC 1288][6]
published in 1991. It's dirt simple: a TCP server listens on port 79 and accepts
one-line queries of ASCII text which typically passes a username and a CRLF. The
server then returns whatever information it has about a given user. If no
username is provided, the server should return summary information of all
online users.

If you use macOS or Linux, you have `finger` installed today. Open a new
terminal and type `finger [username]`:

{{< highlight console >}}
$ finger jp
Login: jp                               Name: John Pignata
Directory: /Users/jp                    Shell: /bin/bash
On since Sun Apr 28 09:54 (EDT) on console, idle 20 days 0:46 (messages off)
On since Sat May 18 09:52 (EDT) on ttys016
No Mail.
Mail forwarded to: john@pignata.com
No Plan.
{{< /highlight >}}

Though most `finger` servers have been long retired or hidden behind firewalls,
a few continue to exist. What's up, `cs.cmu.edu`:

{{< highlight console >}}
$ finger @cs.cmu.edu
[cs.cmu.edu]
Login     Name       Tty      Idle  Login Time   Office     Office Phone   Host
root      root       pts/0      8d  Mar 21 13:56                           (curiosity.fac.cs.cmu.edu)
{{< /highlight >}}

Let's connect the past to the present. Using the `finger` protocol outlined in
RFC1288 and the [Twitter API][7], we can create a retro interface to Twitter
and dip our toes into writing TCP servers in [Go][8] in the process.

First we'll bind to port 79 and listen for new connections:

{{< highlight golang >}}
ln, err := net.Listen("tcp", ":79")

if err != nil {
  fmt.Printf("Could not bind to port: %s\n", err)
  os.Exit(1)
}

for {
  c, err := ln.Accept()

  if err != nil {
    fmt.Printf("Couldn't accept connection: %s\n", err)
    continue
  }

  go handle(c)
}
{{< /highlight >}}

Each time our server receives a new connection on port 79, it'll spawn a
[goroutine][9] by executing a handler function and passing it the connection. In
this function we'll query Twitter's API using a Go client library called
[Anaconda][10], format the results in `finger`-ish output, and return it to the
caller:

{{< highlight golang >}}
func handle(c net.Conn) {
  defer c.Close()

  reader := bufio.NewReader(c)
  userName, _, _ := reader.ReadLine()
  user, err := twitter.GetUsersShow(string(userName), nil)

  if err != nil {
    c.Write([]byte(err.Error()))
    return
  }

  fmt.Fprintf(c, "Login: %-33s Name: %-34s\n", user.ScreenName, user.Name)
  fmt.Fprintf(c, "Location: %-30s URL: %-35s\n", user.Location, user.URL)
  fmt.Fprintf(c, "Description: %s\n", user.Description)
  fmt.Fprintf(c, "Last tweet %s from %s\n", user.Status.CreatedAt, user.Status.Source)
  fmt.Fprintf(c, "Plan: %s\n", user.Status.Text)
}
{{< /highlight >}}

Run the daemon locally with [Twitter API][7] credentials. You can now use
your local `finger` client to look up people on Twitter:

{{< highlight console >}}
$ finger washingtonpost@localhost
[localhost]
Trying 127.0.0.1...
Login: washingtonpost                    Name: The Washington Post
Location: Washington, DC                 URL: http://t.co/Hq7hTYkOPg
Description: Breaking news, analysis, and opinion. Founded in 1877. Our staff on Twitter: https://t.co/VV0UBAMHg8
Last tweet Fri May 17 17:45:36 +0000 2019 from SocialFlow
Plan: Florida lawmakers rail against FBI for secrecy on voter breaches https://t.co/EgWZFFXmyu
{{< /highlight >}}

This exercise is just for laughs. The `finger` client is a pretty poor interface
to Twitter[^3], but in using it with contemporary content you can see a spark
of how an earlier generation of Internet users would have found this first
introduction to social software compelling.

Want to tinker? Download and play with the code example above in my [twinger][12] repo.

[0]: https://ai.stanford.edu/
[1]: https://web.stanford.edu/~learnest/
[2]: https://groups.google.com/forum/#!msg/alt.folklore.computers/IdFAN6HPw3k/Ci5BfN8i26AJ
[3]: https://tools.ietf.org/html/rfc742
[4]: https://www.youtube.com/watch?v=SYpJ1IgGoc0&feature=youtu.be&t=4
[5]: http://web.stanford.edu/~learnest/bucket/
[6]: https://tools.ietf.org/html/rfc1288
[7]: https://developer.twitter.com/
[8]: https://www.golang.org
[9]: https://tour.golang.org/concurrency/1
[10]: https://github.com/ChimeraCoder/anaconda
[11]: http://www.cs.unc.edu/~jeffay/courses/nidsS05/attacks/seely-RTMworm-89.html#p4.5.2
[12]: https://github.com/jpignata/twinger

[^0]: As in, [let your fingers do the walking][4] or pointing.
[^1]: Summarized from [an alt.forklore.computers post from 1990.](https://groups.google.com/forum/#!msg/alt.folklore.computers/IdFAN6HPw3k/Ci5BfN8i26AJ)
[^2]: He appears to be quite a character. To wit, he maintains an exhaustive [bucket list][5] of what he intends to do before he dies in 2043, which he plans to be at the circumstance of a gunshot wound by a jealous husband.
[^3]: Regrettably, most implementions don't even support multi-byte characters, so don't expect to see any non-ASCII characters or tears-of-joy emoji and the like. See **BUGS** in FINGER(1) on BSD-like systems such as Darwin.
