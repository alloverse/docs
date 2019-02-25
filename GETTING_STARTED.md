## Development environment

### Alloplace server

This is an Elixir process that acts very similarly to a dedicated game server.
You run it, and it acts as a container/world simulator for all users
and apps that connect to it.

See the [setup instructions in alloplace's README](https://github.com/alloverse/alloplace#setup)
for setup and run instructions.

Once it's running, you have a server on [alloplace://localhost:21337](alloplace://localhost:21337)!

### Allovisor client

This is the "game client" that connects to the alloplace server.

Allovisor should build in Unity out of the box. The bundled `liballonet.bundle`
is Mac-only, so if you want to develop on another platform, you have to compile
and copy in the appropriate `allonet` binary into the Assets folder.

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

Eventually I'll replace this with CI and a build-time curl job, but not now...
