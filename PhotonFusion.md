# Photon Fusion

- Create an empty GameObject called "NetworkRunner"
- Add the "NetworkRunner" component to it
- Add a new "Spawner" script to it
- Have the Spawner implement INetworkRunnerCallbacks,
  and have the methods do nothing (for now)
  - Instead of implementing the Spawner through code,
    you can add a "NetworkEvents" component to the NetworkRunner object
    and hook up the callbacks via the Editor
- At some point, NetworkRunner.StartGame must be called
  - Can prompt user to choose game mode (e.g., hosting or joining a game),
    and calling StartGame after this choice since StartGame needs game mode
  - Before calling StartGame, set the ProvideInput field to true
    (at least for the host and client in "Hosted" mode, where one computer
    is both the server and client, and other computers are clients)
- Create a GameObject for the player, which will be turned into a prefab
- Add a "NetworkObject" component to it
- In the Spawner script, add code to ``OnPlayerJoined`` and ``OnPlayerLeft``
  to instantiate the player object:

      // OnPlayerJoined
      var networkObject = runner.Spawn(playerPrefab, null, null, player);

      // OnPlayerLeft
      runner.Despawn(networkObject);

  The ``networkObject`` should be saved in a dictionary when created
  in order to despawn it later.
- Create a ``struct`` to hold input-related data to pass through the network
- Still in the Spawner class, collect input from the user,
  either in ``Update`` or ``OnInput``, depending on the kind of input
  - Use ``Update`` for events that may be missed if not checked every frame
  - Use ``OnInput`` for events that is OK if not every one is caught
- In ``OnInput`` use the following to commit the data to send over the network:

      input.Set(data);

- Add a new "Player" or "Controller" script to the Player object,
  and have it derive ``NetworkBehaviour`` (instead of ``MonoBehaviour``).
- Override ``FixedUpdateNetwork()`` and use ``GetInput`` get the
  input previously saved in a ``struct``, and the apply the input values
  to the player.
- If an input instruction requires creating a networked object,
  use ``Runner.Spawn`` to instantiate it.
  - This object should have the ``NetworkObject`` component
    and something like ``NetworkRigidbody`` for physics
    or ``NetworkTransform`` for manually controlled motion
    (there are other similar components: see docs).
- Networked property changes are "useful for spawning local visual effects
  in response to state changes or perform other tasks that does not directly
  impact gameplay logic."
- Remote procedure calls (RPCs) are possible with Fusion but not recommended.
  Only use non-gameplay interaction (purchasing, initial player properties,
  voting on map or type).
