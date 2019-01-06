# Avrae Spells
This repository contains the automation for all spells in Avrae.
Feel free to improve the automation of any spell!

## Basic Spell Structure
A spell is made up of a list of *effects*.
See below for what each effect does.
```ts
{
    type: string;
    meta?: Effect[];
}
```
All effects in an effect's `meta` will be executed before the
rest of the effect, if there is a meta.

### Target
```ts
{
    type: "target";
    target: "all"|"each"|int|"self";
    effects: Effect[];
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
```ts
{
    type: "attack";
    hit: Effect[];
    miss: Effect[];
    attackBonus?: AnnotatedString;
}
```
An Attack effect makes an attack roll against a targeted creature.
It must be inside a Target effect.
- `hit`, `miss`: A list of effects to execute on a hit or miss.
- `attackBonus` (Optional): An AnnotatedString that details what AB to use (defaults to caster AB).

### Save
```ts
{
    type: "save";
    stat: "str"|"dex"|"con"|"int"|"wis"|"cha";
    fail: Effect[];
    success: Effect[];
    dc?: AnnotatedString;
}
```
A Save effect forces a targeted creature to make a saving throw.
It must be inside a Target effect.
- `stat`: What type of saving throw it is.
- `fail`, `success`: A list of effects to execute on a failed or successful save.
- `dc` (Optional): An AnnotatedString that details what DC to use (defaults to caster DC).

### Damage
```ts
{
    type: "damage";
    damage: AnnotatedString;
    higher?: {int: string};
    cantripScale?: boolean;
}
```
Deals damage to a targeted creature. It must be inside a Target effect.
- `damage`: How much damage to deal. Can use variables defined in a Meta tag.
- `higher` (Optional): How much to add to the damage when a spell is cast at a certain level.
- `cantripScale` (Optional): Whether this roll should scale like a cantrip.

### IEffect
```ts
{
    type: "ieffect";
    name: string;
    duration: int | AnnotatedString;
    effects: AnnotatedString;
}
```
Adds an InitTracker Effect to a targeted creature, if the spell is cast in init.
It must be inside a Target effect.
- `name`: The name of the effect to add.
- `duration`: The duration of the effect. Can use variables defined in a Meta tag.
- `effects`: The effects to add (see `add_effect()` in [scripting](https://avrae.io/cheatsheets/aliasing) docs). Can use variables defined in a Meta tag.

### Roll
```ts
{
    type: "roll";
    dice: string;
    name: string;
    higher?: {int: string};
    cantripScale?: boolean;
    hidden?: boolean;
}
```
Rolls some dice and saves the result. Should be in a Meta tag.
- `dice`: What dice to roll.
- `name`: What to save the result as.
- `higher` (Optional): How much to add to the roll when a spell is cast at a certain level.
- `cantripScale` (Optional): Whether this roll should scale like a cantrip.
- `hidden` (Optional): If true, won't display the roll in the Meta field.

### Text
```ts
{
    type: "text";
    text: string;
}
```
Outputs a short amount of text in the resulting embed.
- `text`: The text to display.

## AnnotatedString
An AnnotatedString is a string that can access saved variables from a meta effect.
To access a variable, surround the name in brackets (e.g. `{damage}`). Available variables are any defined in Meta effects and character variables.

This will replace the bracketed portion with the value of the meta variable (usually a roll).

To perform math inside an AnnotatedString, surround the formula with two curly braces (e.g. `{{floor(dexterityMod+spell)}}`).