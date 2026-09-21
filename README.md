# pip-video-player

Firsty's copy of the official Flutter [`video_player`](https://pub.dev/packages/video_player)
packages, with picture-in-picture (PiP) added for iOS.

The official plugin has no PiP on iOS ([flutter/flutter#60048](https://github.com/flutter/flutter/issues/60048)).
The Firsty app starts PiP from its troubleshooting and data-roaming screens, so the user can keep
watching the instructions while they are in iOS Settings.

## Why this repo exists

The app used a community fork (`vanlooverenkoen/flutter-packages`, branch
`feature/#60048-ios-picture-in-picture`). That fork stopped at January 2025 (`video_player` 2.10.0,
`video_player_avfoundation` 2.7.0) and breaks on iOS 27: it attaches a video composition to every
video, and iOS 27 fails those items with `-11800` / `-12784`, so no video plays at all. The official
plugin only adds a composition when a video needs rotating.

This repo starts from the official releases and adds only the PiP start and stop calls on top.

## Contents

| Package | Upstream release | Changed here |
| --- | --- | --- |
| `packages/video_player` | `video_player-v2.14.0` | `startPictureInPicture()` / `stopPictureInPicture()` on the controller |
| `packages/video_player_platform_interface` | `video_player_platform_interface-v6.9.0` | the two PiP methods on the platform interface |
| `packages/video_player_avfoundation` | `video_player_avfoundation-v2.12.0` | iOS implementation of the two PiP methods |

Android and web are not in this repo: the app takes `video_player_android` and `video_player_web`
from pub.dev. They inherit the platform interface defaults, which throw `UnimplementedError`, so PiP
must only be called on iOS.

## Using it

```yaml
dependencies:
  video_player:
    git:
      url: git@github.com:Bambooconnected/pip-video-player.git
      path: packages/video_player
      ref: <commit>

dependency_overrides:
  video_player_avfoundation:
    git:
      url: git@github.com:Bambooconnected/pip-video-player.git
      path: packages/video_player_avfoundation
      ref: <commit>
  video_player_platform_interface:
    git:
      url: git@github.com:Bambooconnected/pip-video-player.git
      path: packages/video_player_platform_interface
      ref: <commit>
```

Pin `ref` to a commit, not a branch, so a push here never changes an app build by itself.

## Upgrading to a new upstream release

1. Replace the package folders with the new release tags from
   [flutter/packages](https://github.com/flutter/packages) (without `example/` and `doc/`) and commit that
   as an unmodified import.
2. Reapply the PiP commit on top and resolve conflicts.
3. Update the release table above.

Keeping the upstream import and the PiP change in separate commits keeps step 2 a small, readable
diff.
