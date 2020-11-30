# Identity

Identity hasn't been completely fleshed out yet, but here's the general gist:

* Alloverse is decentralized, so there is no central identity authority.
* When a Visor is started for the first time, it generates a private+public key pair.
  The user is also prompted to fill in "profile data" such as name, email, profile
  picture, choice of avatar, etc.
* Public key + profile data = "Alloverse Passport"
* Visors can transfer passports to other visors over Bluetooth or Wifi. E g, if the
  user starts out on a Mac and later get a Quest, when they start the Quest app
  they're prompted to either create a passport or transfer one over.
  If they choose to transfer, the Mac app and Quest app will communicate and show
  UI to confirm the transfer on both sides.
* Visors with the same Passport communicate to connect to the same place, and
  they control the same avatar, so that it's easy to switch between platforms and
  level of immersion.
* Agents can identify each other using Diffie Hellman key exchange.
  * This means that an app "logs in" the users that are in the room by exchanging
    keys with them, and there's no need to type in any usernames or passwords. Apps
    automatically know who is in the room and can customize its contents based on
    that.
  * Users also identify themselves to each other, so that you can know that the
    person you talked to yesterday is the same person you are talking to today.
    This also lets you build a contact list.
* Key exchange is done through Interactions that can be blocked on a Place level
  or at a Visor level, so that people who don't wish to be identified to other
  people or apps without authorizing it, won't be.
* To back up your Passport, you can link it to your my.alloverse.com account,
  or another web service.
