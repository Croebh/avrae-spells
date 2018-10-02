# Avrae Spells
This repository contains the automation for all spells in Avrae.
Feel free to improve the automation of any spell!

## Basic Spell Structure
A spell is made up of a list of *effects*.
See below for what each effect does.
```json
{
    "type": string,
    "meta?": [Effect]
}
```
All effects in an effect's `meta` will be executed before the
rest of the effect, if there is a meta.

### Target
```json
{
    "type": "target",
    "target": "all"|"each"|int|"self",
    "effects": [Effect]
}
```
A Target effect should only show up as a top-level effect.
It designates what creatures to affect.
- `target`
    - `"all"`: Affects all targets (usually save spells)
    - `"each"`: Affects each target (usually attack spells)
    - `int`: Affects the nth target (1-indexed)
    - `"self"`: Affects the caster
- `effects`: A list of effects that each targeted creature will be subject to.

### Attack
```json
{
    "type": "attack",
    "hit": [Effect],
    "miss": [Effect]
}
```
An Attack effect makes an attack roll against a targeted creature.
It must be inside a Target effect.
- `hit`, `miss`: A list of effects to execute on a hit or miss.

### Save
```json
{
    "type": "save",
    "stat": "str"|"dex"|"con"|"int"|"wis"|"cha",
    "fail": [Effect],
    "success": [Effect]
}
```
A Save effect forces a targeted creature to make a saving throw.
It must be inside a Target effect.
- `stat`: What type of saving throw it is.
- `fail`, `success`: A list of effects to execute on a failed or successful save.

### Damage
```json
{
    "type": "damage",
    "damage": AnnotatedString,
    "higher?": {int: string}
}
```
Deals damage to a targeted creature. It must be inside a Target effect.
- `damage`: How much damage to deal. Can use variables defined in a Meta tag.
- `higher` (Optional): How much to add to the damage when a spell is cast at a certain level.

### IEffect
```json
{
    "type": "ieffect",
    "name": string,
    "duration": int or AnnotatedString,
    "effects": AnnotatedString
}
```
Adds an InitTracker Effect to a targeted creature, if the spell is cast in init.
It must be inside a Target effect.
- `name`: The name of the effect to add.
- `duration`: The duration of the effect. Can use variables defined in a Meta tag.
- `effects`: The effects to add (see `add_effect()` in [scripting](https://avrae.io/cheatsheets/aliasing) docs). Can use variables defined in a Meta tag.

### Roll
```json
{
    "type": "roll",
    "dice": "2d6[cold]",
    "name": "damage",
    "higher": {
      "2": "1d6[cold]"
    }
}
```
Rolls some dice and saves the result. Should be in a Meta tag.
- `dice`: What dice to roll.
- `name`: What to save the result as.
- `higher` (Optional): How much to add to the roll when a spell is cast at a certain level.

### Text
```json
{
    "type": "text",
    "text": string
}
```
Outputs a short amount of text in the resulting embed.
- `text`: The text to display.
