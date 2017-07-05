_For hype.codes_

# Playing Unison #

Every software developer at any time may need to work with the same
files despite of where they originally were. For example, you have to
move your precious backup the farther the better. Another case, you
are developing a Rails application while running it on a remote
machine: the remote runtime has to have all the file changes being
applied at the correct time. Mybe you even want to create some
dropbox-alike for you and your friends totally aside the popular cloud
services which you for some reason don't trust anymore. So, you need a
tool to sync your files with some remote box.

There are quite a multiple solutions for file synchronization; each of
different complexity. Here I will cover the one both flexible and,
say, allowing doing the simple things with the simple
commands. It's [Unison][Unison]. It's written in [OCaml][OCaml]
(hello, practical FP-ers). It's compact (portable native binary with
moderate memory footprint), fast (compresses files on-the-fly if
needed while sending them over a slow link) and reliable (files
metadata are stored on both ends; the transmission restarts if
needed). Last but not least, Unison is well-documented: there's an
online [doc][Unison online documentation]. Also, the comprehensive
help is embedded within the binary (available via `-help`
key). Stacking that up, you can have _all_ documentation on _any_ of
multiple options Unison has. Btw, Unison is made for bidirectional
file synchronization; all of its nuances are fine-tuneable. Also, you
can sync two locations on the same machine: very useful if we need to
sync a backup to a removable media.

Now let's install and run something useful. Our example is quite simple
yet common: "here" and "there" we sync two source trees while editing
the files on both sides of the link. "Here" is my Mac laptop; "There"
is some remote Ubuntu instance (I use the one from my homelab).

## Installation ##

### On the Mac ###

1. Install Unison via Homebrew.

   ![Installing Unison via Homebrew][fig. 1]

2. Using `pip` and `curl`, install the helper for filesystem events
   monitoring.

   ![Installing fsevents helper][fig. 2]

For more information look at the
`docker-sync` [documentation][docker-sync]. `docker-sync` is a tool
used to sync local source tree with the remote Rails-dev docker
container.

Let's see local synchronization:

![Local sync part 1][fig. 3]

![Local sync part 2][fig. 4]

![Local sync part 3][fig. 5]

So, Unison is run with `unison [options] <here> <there>`. "There" can
be on either local or remote machine. We will experiment with the
remote "there" in the next paragraph; now let's notice that being
called without options Unison runs

a) interactively;

b) in one run: it analyses the replicas once, syncs them and exits.

Different CLI options are there to adjust this basic behaviour if yow
want something smarter, including the fully daemonized
client-server solutions.

### On the Ubuntu box ###

In order to use the working fs-monitoring python helper we need to
install Unison from the source. It's not a problem because all we need
is available as Opensource.

1. Install OCaml

   [fig. 6]

   [fig. 7]

   [fig. 8]

   [fig. 9]

   [fig. 10]

2. Download Unison source; check out the correct version;
   compile'n'install the binaries:

   [fig. 11]

   [fig. 12]

## Synchronization ##

Now, I get back to my Mac and we'll play with the actual files.

1. Create the remote directory and do the first, interactive, run to
   establish the replicas metadata

   [fig. 13]

   The output is eloquently desribing what's been done. Note the
   "ssh-url" where we specify the remote dir. Also, see that the local
   binary first starts the remote binary through SSH; then establishes
   ssh-tunnel between the too after which they negotiate and do the
   actual data transfer.

   Now, start the remote synchronization in "scan-syn-repeat" loop

   [fig. 14]

   Unison processes together with their fs-monitors are started on
   both ends

   [fig. 15]

   Now lets edit the file and see how the changes are propagated

   [fig. 16]

   The Unison output shows how the magis is done

   [fig. 17]

   Edit file "here", see the changes applied "there", and vice versa

   [fig. 18]

   [fig. 19]

   So it works: the changes are propagated in both directions. Our
   example was quite basic: one dir with one files. In my everyday
   development I sync the whole Rails project tree together with qute
   large number of upload files, etc. Also, I use Emacs which monitors
   the files itself and shows the changes on either end almost
   immediately.

## Some caveats ##

1. Unison isn't a networked file system so the locks aren't propagated
   and all the changes are being sent with some lag according to the
   network characteristics. You should take this into account and
   avoid the conflicts while editing a file on both ends
   simultaneously.

2. Unison assumes the synced filesystems to be case-sensitive so
   beware and avoid syncing the files with case-only differing names
   if your filesystems are different in this sense. For instance, if
   you sync Mac fs vs. Linux ext4, etc.

[Unison]: https://www.cis.upenn.edu/~bcpierce/unison/
[Unison online documentation]: https://www.cis.upenn.edu/~bcpierce/unison/
[OCaml]: http://ocaml.org
[docker-sync]: https://github.com/EugenMayer/docker-sync

[fig. 1]: figures/fig1.png "Installing Unison via Homebrew"
[fig. 2]: figures/fig2.png "Installing fsevents helper"
[fig. 3]: figures/fig3.png "Local sync part 1"
[fig. 4]: figures/fig4.png "Local sync part 2"
[fig. 5]: figures/fig5.png "Local sync part 3"
