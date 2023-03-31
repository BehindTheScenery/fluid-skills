# Fluid Skills

A library mod that exposes KubeJS methods to restrict how players see and interact with fluids. Built around
[Player Skills](https://github.com/impleri/player-skills). This interacts with Block Skills and Item Skills to provide
some of its functionality: Block Skills to replace fluids in the world, Item Skills to interact with recipes using
fluids.

## KubeJS API

When creating restrictions, you have two avenues: `replace` the fluid with a different fluid or `modify` what a player
can do with the actual fluid. Replacements will trump other modifications. One example where you may want to use both is
in cascading progress: when the player starts, the fluid is replaced with something basic (e.g. replace oil with lava)

### Register

We use the `FluidSkillEvents.register` event to register fluid restrictions. If the player ***matches*** the criteria,
the following restrictions are applied. This can cascade with other restrictions, so any restrictions which replaces a
fluid will trump any which only add restrictions to the fluid. Also, any restrictions which deny the ability
will trump any which allow it. We also expose these methods to indicate what restrictions are in place for when a player
meets that condition. By default, no restrictions are set, so be sure to set actual restrictions.

As an extension to PlayerSkills, all
the [common restriction facets](https://github.com/impleri/player-skills#kubejs-restrictions-api) are usable here.

#### Replacement methods

- `replaceWithFluid`: ResourceLocation/string referencing a fluid. Flowing states will be matched if possible when
  replacing in-world fluids.
- `replaceWithAir`: Replaces the fluid with air (it's completely hidden!)

#### Allow Restriction Methods

- `nothing`: shorthand to apply all "allow" restrictions
- `bucketable`: Player can pick up a source block with a bucket

#### Deny Restriction Methods

- `everything`: shorthand to apply the below "deny" abilities
- `unbucketable`: Player cannot pick up a source block with a bucket

### Other methods

These methods do not use player conditions, but dimension and biome conditions will apply

- `infinite`: Allows fluid to create new source blocks (default behavior for water, only works on supported fluids)
- `finite`: Disallows creating new source blocks (default behavior for lava, only works on supported fluids)

#### Supported fluids

- `minecraft:water`
- `minecraft:lava`

### Examples

```js
FluidSkillEvents.register(event => {
  // Replace lava with water
  event.restrict(
    'minecraft:lava',
    r => r.replaceWithFluid('water').if(player => player.cannot('skills:stage', 2))
  );
});
```

## Developers

Add the following to your `build.gradle`. I depend
on [Architectury API](https://github.com/architectury/architectury-api), [KubeJS](https://github.com/KubeJS-Mods/KubeJS),
and [PlayerSkills](https://github.com/impleri/player-skills), so you'll need those as well.

```groovy
dependencies {
    // Common should always be included 
    modImplementation "net.impleri:fluid-skills-${minecraft_version}:${blockskills_version}"
    // Plus forge
    modApi "net.impleri:fluid-skills-${minecraft_version}-forge:${blockskills_version}"
    // Or fabric
    modApi "net.impleri:fluid-skills-${minecraft_version}-fabric:${blockskills_version}"
}
repositories {
    maven {
        url = "https://maven.impleri.org/minecraft"
        name = "Impleri Mods"
        content {
            includeGroup "net.impleri"
        }
    }
}
```

## Modpacks

Want to use this in a modpack? Great! This was designed with modpack developers in mind. No need to ask.
