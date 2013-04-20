hg-helpers
==========

Purpose
=======
Managing Mercurial-based repositories used by multiple client hosts,
without overbloated GUI's and frameworks.

Currently tested on Ubuntu 12.04 LTS only,
but can be easily adopted to any Linux/Unix distribution.

Conventions
===========
1) Central repository host (repo server) should have "repo.<domain>" DNS-name.
   Repo server and client hosts should have the same domain!
2) One client host = one user account on repo server, with homedir in /home/repousers
3) One repository = one group account on repo server, with datadir in /home/repos
4) On client hosts, access to repo server is performed under local root account.
5) Server authorizes clients using SSH keys.

Usage roadmap
=============
1) Setup server:
    - on DNS server  : create "repo" A-record
    - on repo server : hgrepo-setupserver

2a) Setup client hosts:
    - on client host : hgrepo-setupclient         (user must be root!)
    - on repo server : hgrepo-registerclient ...

2b) Setup client users:
    - on user side   : hgrepo-setupclient         (user must be non-root!)
    - on repo server : hgrepo-registerclient ...

3) Setup projects:
    - on repo server : hgrepo-setuprepo <repo> [<owner>]   (owner is account on server, root by default)

4) Add clients to projects:
    - on repo server : hgrepo-client2repos <client> <repo...>
    - on repo server : hgrepo-repo4clients <repo> <clients...>
    - on clients:    : hg clone ssh://repo/<repo> [<destdir>]

5) Create new project on client and push to repo server (includes steps 2,3,4):
   On client side (install/configure Mercurial and generate SSH keys/config):
    - hgrepo-setupclient
   On server side (register client account and create repo directory+group):
    - hgrepo-registerclient ...
    - hgrepo-setuprepo proj1 client1
    - hgrepo-repo4clients proj1 client1
   Back to client side (do ordinal hg steps):
    - cd /path/to/proj1/
    - hg init && hg add && hg ci -m 'Initial commit.'
    - echo -e "\n[paths]\ndefault = ssh://repo/$(basename $(pwd))" >> .hg/hgrc
    - hg push
   Back to server side (fixup project permissions after first commit):
    - hgrepo-setuprepo proj1 client1

Todo
====
Users with restricted read-only access? (at now, everybody can push)

Disclaimer
==========
Provided "as is" under terms of public domain.
Written at 20-Apr-2013 by ilya.evseev@gmail.com

>> END <<
