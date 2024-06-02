These are the data files for [thaliak.com](https://thaliak.com). All data files are composed in YAML, to make them both easy to read and write.

## Directory structure

| Directory       | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| `/enums`        | Contains various definitions that can be referenced in timelines. |
| `/schemas`      | Contains the JSON schemas that can be used to validate every YAML file in this repository. If you use Visual Studio Code with the RedHat YAML extension, Code will automatically highlight errors in your YAML files without any additional configuration. |
| `/extremes`     | Contains the timelines for extreme fights.                        |
| `/raids`        | Contains the timelines for savage fights.                         |
| `/ultimates`    | Contains the timelines for ultimate fights.                       |
| `calendar.yaml` | Contains a list of events to be shown on https://thaliak.com/calendar. |
| `metadata.yaml` | Required metadata injected into server-side code at runtime. Do not modify this. |
| `recent.yaml`   | A list of recent fights that will be shown at the top of Thaliak's home page. |

## Timelines

The fight timelines are the main component of thaliak.com, and as a result also make up the majority of files in this repository. There's one file per fight and it requires at least the following properties:

| Property       | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| `id`            | The ID of the duty finder instance in the game files. Can be found via [`ironworks-cli`](https://github.com/thaliakcom/ironworks-cli). |
| `patch`         | The patch in which this fight was released. |
| `date`          | The date the timeline was first created.                        |
| `modified`      | The date the timeline was last modified.                         |
| `by`            | An array of contributors. The `id` refers to their Lodestone ID.                       |
| `boss`          | The name of the boss to be fought in this fight. If multiple bosses are fought, this should be the final boss' name. Can later be referred to using `[boss]`. |
| `HP`            | The total HP of the boss. In ultimates, set this to `0`. |
| `average_hp`    | The average HP of non-tank party members when this fight was on content. |
| `tier`          | For savage fights, the full name of the tier the fight takes place in. |
| `suffix`        | Can be used to add a suffix to the name of the fight. Useful for fights that have a door boss, since those fights have the same name otherwise. |
| `autos`         | An object composed of a `mechanic` and `type` property that specify the type of auto-attacks in this fight. |
| `description`   | A short appetizer for the fight that is displayed at the top of the timeline. |
| `status`        | A dictionary of status effects to be found in the fight. |
| `actions`       | A dictionary of (enemy) actions to be found in the fight. |
| `timeline`      | A list of timestamps along with the action that takes place at that point in time. |
| `graphing`      | Graphing data (used to define interactive diagrams). |

For more information on the properties that each status, action, etc. can have, check some of the existing timelines or review the JSON schema.
Full documentation on every available property may come later.

## Timeline text

All descriptions within the timeline (including action descriptions, status descriptions, strategy descriptions, etc.) are post-processed. This allows you to use a limited form of Markdown (supporting **bold**, *italics*, and asterisk-prefixed lists) as well as certain _placeholders_ that automatically replace themselves with various variables and buttons.

The following placeholders are valid:

| Placeholder                | Example                                    | Description |
| -------------------------- | ------------------------------------------ | ----------- |
| `[fight]`                  | `[fight]`                                  | Replaced with the name of the fight. |
| `[boss]`                   | `[boss]`                                   | Replaced with the name of the boss. |
| `[a:ACTION_ID]`            | `[a:blaster]`                              | Replaced with a button that opens a dialog to explain the action in more detail. `ACTION_ID` must be one of the keys in `actions`. |
| `[a:ACTION_ID:c]`          | `[a:blaster:c]`                            | Like `[a:ACTION_ID]`, but the name of the action is capitalized. |
| `[a:ACTION_ID:d]`          | `[a:blaster:d]`                            | Replaced with the full description of the action. |
| `[TEXT](a:ACTION_ID)`      | `[Blaster (spread)](a:blaster)`            | Like `[a:ACTION_ID]`, but instead of using the name of the action for the button, uses `TEXT` instead. |
| `[s:STATUS_ID]`            | `[s:doom]`                                 | Replaced with a button that opens a dialog to explain the status effect in more detail. `STATUS_ID` must be one of the keys in `status`. |
| `[s:STATUS_ID:c]`          | `[s:doom:c]`                               | Like `[s:STATUS_ID]`, but the name of the status effect is capitalized. |
| `[s:STATUS_ID:d]`          | `[s:doom:d]`                               | Replaced with the full description of the status effect. |
| `[s:STATUS_ID:N]`          | `[s:vuln-up:4]`                            | Like `[s:STATUS_ID]`, but for status effects that can have multiple stacks, uses the icon for `N` stacks. |
| `[TEXT](s:STATUS_ID)`      | `[Doom (5s)](s:doom)`                      | Like `[s:STATUS_ID]`, but instead of using the name of the status effect for the button, uses `TEXT` instead. |
| `[t:TERM(:d)(:c)]`         | `[t:AoE]` `[t:AoE:d]` `[t:wall:c]`         | Replaced with a button that opens a dialog to explain the term in more detail. `TERM` must be a key from `enums/terms.yaml`. |
| `[TEXT](t:TERM)`           | `[walling](t:wall)`                        | Like `[t:TERM]`, but instead of using the name of the term for the button, uses `TEXT` instead. |
| `[m:MECHANIC(:d)(:c)]`     | `[m:spread]` `[m:spread:d]` `[m:spread:c]` | Replaced with a button that opens a dialog to explain the mechanic type in more detail. `MECHANIC` must be a key from `enums/mechanic-types.yaml`. |
| `[TEXT](m:MECHANIC)`       | `[walling](t:wall)`                        | Like `[m:MECHANIC]`, but instead of using the name of the mechanic type for the button, uses `TEXT` instead. |
| `[ms:SHAPE(:d)(:c)]`       | `[ms:cone]` `[ms:cone:d]` `[ms:cone:c]`    | Replaced with a button that opens a dialog to explain the mechanic shape in more detail. `SHAPE` must be a key from `enums/mechanic-shapes.yaml`. |
| `[TEXT](ms:SHAPE)`         | `[conal](ms:cone)`                         | Like `[ms:SHAPE]`, but instead of using the name of the mechanic shape for the button, uses `TEXT` instead. |
| `[st:STATUS_TYPE(:d)(:c)]` | `[st:dot]` `[st:dot:d]` `[st:dot:c]`       | Replaced with a button that opens a dialog to explain the status effect type in more detail. `STATUS_TYPE` must be a key from `enums/status-types.yaml`. |
| `[TEXT](st:STATUS_TYPE)`   | `[damage over time](st:dot)`               | Like `[st:STATUS_TYPE]`, but instead of using the name of the status effect type for the button, uses `TEXT` instead. |
| `[i:ICON]`                 | `[i:caster]`                               | Replaced with the specified icon. See below for a list of valid icons. |
| `:unverified(TEXT)`        | `:unverified(always hits twice)`           | Highlights a given piece of text with the disclaimer that the information is unverified and may be false. |

In addition, all HTML is valid in descriptions but should be avoided if possible.

## Valid icons

The following values are valid for the icon (`[i:ICON]`) placeholder:

* <img src="https://thaliak.com/images/sub-icons/tank.png" width="24" height="24" /> `[i:tank]`
* <img src="https://thaliak.com/images/sub-icons/healer.png" width="24" height="24" /> `[i:healer]`
* <img src="https://thaliak.com/images/sub-icons/dps.png" width="24" height="24" /> `[i:dps]`
* <img src="https://thaliak.com/images/sub-icons/melee.png" width="24" height="24" /> `[i:melee]`
* <img src="https://thaliak.com/images/sub-icons/ranged.png" width="24" height="24" /> `[i:ranged]`
* <img src="https://thaliak.com/images/sub-icons/pranged.png" width="24" height="24" /> `[i:pranged]`
* <img src="https://thaliak.com/images/sub-icons/caster.png" width="24" height="24" /> `[i:caster]`
* <img src="https://thaliak.com/images/sub-icons/circle.svg" width="24" height="24" /> `[i:circle]`
* <img src="https://thaliak.com/images/sub-icons/cross.svg" width="24" height="24" /> `[i:cross]`
* <img src="https://thaliak.com/images/sub-icons/square.svg" width="24" height="24" /> `[i:square]`
* <img src="https://thaliak.com/images/sub-icons/triangle.svg" width="24" height="24" /> `[i:triangle]`
