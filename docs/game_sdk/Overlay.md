# Overlay

> info
> Need help with the SDK? Talk to us at [dis.gd/devsupport](https://dis.gd/devsupport)

Discord comes with an awesome built-in overlay, and you may want to make use of it for your game. This manager will help you do just that! It:

- Gives you the current state of the overlay for the user
  - Locked, enabled, unlocked, open, closed, etc.
- Allows you to change that state

## Data Models

###### ActivityActionType Enum

| name     | value |
| -------- | ----- |
| Join     | 1     |
| Spectate | 2     |

## IsEnabled

Check whether the user has the overlay enabled or disabled. If the overlay is disabled, all the functionality in this manager will still work. The calls will instead focus the Discord client and show the modal there instead.

Returns a `bool`.

###### Parameters

None

###### Example

```cs
if (!overlaymanager.IsEnabled())
{
  Console.WriteLine("Overlay is not enabled. Modals will be shown in the Discord client instead");
}
```

## IsLocked

Check if the overlay is currently locked or unlocked

###### Parameters

None

###### Example

```cs
if (overlayManager.IsLocked())
{
  overlayManager.SetLocked(true, (res) =>
  {
    Console.WriteLine("Input in the overlay is now accessible again");
  });
}
```

## SetLocked

Locks or unlocks input in the overlay. Calling `SetLocked(true);` will also close any modals in the overlay or in-app from things like IAP purchase flows and disallow input.

Returns `Discord.Result` via callback.

###### Parameters

| name   | type | description                |
| ------ | ---- | -------------------------- |
| locked | bool | lock or unlock the overlay |

###### Example

```cs
overlayManager.SetLocked(true, (res) =>
{
  Console.WriteLine("Overlay has been locked and modals have been closed");
});
```

## OpenActivityInvite

Opens the overlay modal for sending game invitations to users, channels, and servers. In order for this to function, you must have the proper activity fields set on the local client before attempting to use this feature. To know what you need for Spectate and Join invites, refer to [Activity Action Field Requirements](#DOCS_GAME_SDK_ACTIVITIES/activity-action-field-requirements).

Returns a `Discord.Result` via callback.

###### Parameters

| name | type               | description                 |
| ---- | ------------------ | --------------------------- |
| type | ActivityActionType | what type of invite to send |

###### Example

```cs
overlayManager.OpenActivityInvite(Discord.ActivityActionType.Join, (result) =>
{
  if (result == Discord.Result.Ok)
  {
    Console.WriteLine("User is now inviting others to play!");
  }
});
```

## OpenGuildInvite

Opens the overlay modal for joining a Discord guild, given its invite code. An invite code for a server may look something like `fortnite` for a verified server—the full invite being `discord.gg/fortnite`—or something like `rjEeUJq` for a non-verified server, the full invite being `discord.gg/rjEeUJq`.

Returns a `Discord.Result` via callback. Note that a successful `Discord.Result` response does not necessarily mean that the user has joined the guild. If you want more granular control over and knowledge about users joining your guild, you may want to look into implementing the [`guilds.join` OAuth2 scope in an authorization code grant](#DOCS_TOPICS_OAUTH2/authorization-code-grant) in conjunction with the [Add Guild Members](#DOCS_RESOURCES_GUILD/add-guild-member) endpoint.

###### Parameters

| name | type   | description                |
| ---- | ------ | -------------------------- |
| code | string | an invite code for a guild |

###### Example

```cs
overlayManager.OpenGuildInvite("rjEeUJq", (result) =>
{
  if (result == Discord.Result.Ok)
  {
    Console.WriteLine("Invite was valid.");
  }
});
```

## OpenVoiceSettings

Opens the overlay widget for voice settings for the currently connected application. These settings are unique to each user within the context of your application. That means that a user can have different favorite voice settings for each of their games!

![](https://user-images.githubusercontent.com/167844/48321255-f4a44080-e5d5-11e8-930c-044a1b76d76f.png)

Also, when connected to a lobby's voice channel, the overlay will show a widget that allows users to locally mute, deafen, and adjust the volume of others.

![](https://user-images.githubusercontent.com/167844/48321253-f2da7d00-e5d5-11e8-94f5-c6a5314b8a07.png)

Returns a `Discord.Result` via callback.

###### Parameters

None

###### Example

```cs
overlayManager.OpenVoiceSettings((result) =>
{
  if (result == Discord.Result.Ok)
  {
    Console.WriteLine("Overlay is open to the voice settings for your application/")
  }
})
```

## OnToggle

Fires when the overlay is locked or unlocked (a.k.a. opened or closed)

###### Parameters

| name   | type | description                           |
| ------ | ---- | ------------------------------------- |
| locked | bool | is the overlay now locked or unlocked |

## Example: Activate Overlay Invite Modal

```cs
var discord = new Discord.Discord(clientId, Discord.CreateFlags.Default);
var overlayManager = discord.GetOverlayManager();

// Invite users to join your game
overlayManager.OpenActivityInvite(ActivityActionType.Join, (result) =>
{
  Console.WriteLine("Overlay is now open!");
})
```

And that invite modal looks like this!

![](overlay-invite.gif)
