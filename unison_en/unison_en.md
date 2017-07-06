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

   ![Installing OCaml & OPAM via apt-get][fig. 6]

   ![Initializing OPAM environment for future builds][fig. 7]

2. Download Unison source; check out the correct version;
   compile'n'install the binaries:

   ![Compiling Unison][fig. 8]

   ![Installing the binaries][fig. 9]

## Synchronization ##

Now, I get back to my Mac and we'll play with the actual files.

1. Create the remote directory and do the first, interactive, run to
   establish the replicas metadata

   ![Initializing the replicas][fig. 10]

   The output is eloquently desribing what's been done. Note the
   "ssh-url" where we specify the remote dir. Also, see that the local
   binary first starts the remote binary through SSH; then establishes
   ssh-tunnel between the too after which they negotiate and do the
   actual data transfer.

   Now, start the remote synchronization in "scan-sync-repeat" loop

   ![Starting Unison in watch/batch mode][fig. 11]

   Unison processes together with their fs-monitors are started on
   both ends

   ![Remote processes tree][fig. 12]

   Now lets edit the file and see how the changes are propagated

   ![Sync: remote -> local][fig. 13]

   The Unison output shows how the magic is done

   ![Sync log][fig. 14]

   Edit file "here", see the changes applied "there", and vice versa

   ![Sync: local -> remote][fig. 15]

   ![Sync log][fig. 16]

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
[fig. 6]: figures/fig6.png "Installing OCaml & OPAM via apt-get"
[fig. 7]: figures/fig7.png "Initializing OPAM environment for future builds"
[fig. 8]: figures/fig8.png "Compiling Unison"
[fig. 9]: figures/fig9.png "Installing the binaries"
[fig. 10]: figures/fig10.png "Initializing the replicas"
[fig. 11]: figures/fig11.png "Starting Unison in watch/batch mode"
[fig. 12]: figures/fig12.png "Remote processes tree"
[fig. 13]: figures/fig13.png "Sync: remote -> local"
[fig. 14]: figures/fig14.png "Sync log"
[fig. 15]: figures/fig15.png "Sync: local -> remote"
[fig. 16]: figures/fig16.png "Sync log"
