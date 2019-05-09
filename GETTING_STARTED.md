## Development environment

### Alloplace server

This is an Elixir process that acts very similarly to a dedicated game server.
You run it, and it acts as a container/world simulator for all users
and apps that connect to it.

There is an always-on development server at [alloplace://nevyn.places.alloverse.com](alloplace://nevyn.places.alloverse.com).
You can use it to try out your code so you don't have to run a server locally if all
you're interested in is client development.

If you DO want to do server development, see the 
[setup instructions in alloplace's README](https://github.com/alloverse/alloplace#setup)
for setup and run instructions.

Once it's running, you have a server on [alloplace://localhost](alloplace://localhost)!

### Allovisor client

This is the "game client" that connects to the alloplace server.

[See its build instructions in its README](https://github.com/alloverse/allovisor#compiling-and-developing-allovisor)

It uses CI integration to get the latest version of "allonet", the network library used to
talk to the Alloverse. It's all in the README how you set that up.

### Appliances

See the [appliance dev guide](writing-apps).

### Allonet

Allonet is the network layer. Unless you're doing deep protocol hacking,
you shouldn't need to mess with it. It's built into allovisor and alloplace,
and should work as-is.

If you want to hack on it...

Allonet is written in C and CMake. I recommend using Visual Studio Code,
and installing the "CMake-Tools" package to build and run from the GUI.

You can then either run `./build/allodummyclient` to try things out.
If you want to try it in the real Allovisor, copy `liballonet.dylib` into
the Unity project's assets, something like this:

    cp build/liballonet.dylib ../allovisor/Assets/liballonet.bundle
