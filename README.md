# Alloverse

_By Nevyn Bengtsson (nevyn@alloverse.com), started 2018-08-25._

Alloverse is an open platform for collaborative workspaces in 3D.
It’s Gibson style Cyberspace, but for your day-to-day work and play, with your friends and colleagues. It’s a VR and AR platform for creating spaces, and for running real applications within those spaces, together with other people. 

[Please see the public website for more information](https://alloverse.com).

In nerd terms, it's a VR/AR/3D "window manager" and collaborative workspace.

* Your "place" is where you decorate, run apps, invite people, and
  hang out. It's like a collaborative X11 server: It runs a world simulation
  server, a voip gateway, and all the backing data for 3d UIs for
  the running apps. The reference Elixir implementation is in 
  [`allo-placeserv`](https://github.com/alloverse/allo-placeserv).
* A "visor" is the GUI application you use to visit places and interact
  with your apps. [`allovisor`](https://github.com/alloverse/allovisor)
  implements such a visor for VR in Unity.
* An "appliance" is a process running on your computer, or on a computer
  on the Internet. Like opening a web page (or launching a remote X11
  process), this app can then show its interface and be interacted with
  inside your place. 
* The network and "UI protocol" is abstracted into the 
  [`allonet`](https://github.com/alloverse/allovisor) library,
  written in C and used by all the above projects.

I'm writing all this from scratch in my barely-available spare time because
it's MY hobby project and ME is an idiot.

## Continued reading

* See [CONTRIBUTING](CONTRIBUTING.md) for development getting-started,
  and guidelines for contribution.
* See the [public website](https://alloverse.com) for news, installation
  instructions, etc.
* See [architecture](architecture) for overview of how things fit together.
* See [specifications](specifications) for protocol references.
* See [ux](ux) for user experience design principles and interaction patterns
  in the Alloverse.