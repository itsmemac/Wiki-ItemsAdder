# Elytra

{% hint style="warning" %}
Custom Elytras require **Minecraft 1.21.2+** and **ItemsAdder 4.0.18+**
{% endhint %}

A custom Elytra has three visual parts:

1. The normal inventory and hand texture.
2. An optional broken inventory and hand texture.
3. The wings texture rendered on the player's body.

ItemsAdder automatically creates the required chest equipment, enables the vanilla glider component, and generates the `layers.wings` equipment model.
You do not need to create equipment JSON or item-model JSON files manually.

## Creating the content pack

Create a new content folder:

``` text
plugins/ItemsAdder/contents/my_elytras/
├── configs/
│   └── items.yml
└── textures/
    ├── item/
    │   ├── sky_elytra.png
    │   └── sky_elytra_broken.png
    └── entity/
        └── equipment/
            └── wings/
                └── sky_elytra.png
```

## Creating the textures

### Inventory texture

Create the normal item texture:

``` text
contents/my_elytras/textures/item/sky_elytra.png
```

A standard `16x16` item texture is recommended.

### Broken inventory texture (optional)

Create the broken texture:

``` text
contents/my_elytras/textures/item/sky_elytra_broken.png
```

When `broken_item_texture` is not specified, ItemsAdder automatically looks for a texture named:

``` text
<normal_item_texture>_broken.png
```

For example:

``` text
item/sky_elytra.png
item/sky_elytra_broken.png
```

{% hint style="info" %}
The broken texture changes only the inventory and hand model. It never changes the wings rendered on the player.
{% endhint %}

### Wings texture

Create the texture used while the Elytra is equipped:

``` text
contents/my_elytras/textures/entity/equipment/wings/sky_elytra.png
```

The recommended vanilla resolution is `64x32`. Higher resolutions should preserve the same `2:1` proportions, for example `128x64` or `256x128`.

ItemsAdder copies this texture into the correct version-specific resource-pack overlay. It does not modify the image pixels.

## Minimal configuration

Create `contents/my_elytras/configs/items.yml`:

``` yaml
info:
  namespace: my_elytras

items:
  sky_elytra:
    name: Sky Elytra
    material: ELYTRA
    graphics:
      texture: item/sky_elytra
    elytra:
      wings_texture: entity/equipment/wings/sky_elytra
```

## Configuring the broken texture

### Automatic selection

If your files are named:

``` text
sky_elytra.png
sky_elytra_broken.png
```

You can omit `broken_item_texture`:

``` yaml
graphics:
  texture: item/sky_elytra
elytra:
  wings_texture: entity/equipment/wings/sky_elytra
```

### Explicit selection

You can also select a different texture explicitly:

``` yaml
elytra:
  wings_texture: entity/equipment/wings/sky_elytra
  broken_item_texture: item/damaged_sky_elytra
```

Resource paths can include the namespace:

``` yaml
broken_item_texture: my_elytras:item/damaged_sky_elytra
```

The `.png` extension is optional, but omitting it is recommended.

ItemsAdder automatically generates the correct broken item-model state for each supported Minecraft version.

## Elytra properties

``` yaml
elytra:
  wings_texture: entity/equipment/wings/sky_elytra
  broken_item_texture: item/sky_elytra_broken
  max_flight_ticks: 0
  firework:
    enabled: true
    consume: true
    cooldown_ticks: 0
  durability_damage_multiplier: 1.0
  collision_damage_multiplier: 1.0
```

| Property | Default | Description |
| --- | --- | --- |
| `wings_texture` | Required | Texture rendered on the player's body while the Elytra is equipped. |
| `broken_item_texture` | Automatic | Inventory and hand texture used when the item is broken. |
| `max_flight_ticks` | `0` | Maximum continuous gliding time. `0` means unlimited. |
| `firework.enabled` | `true` | Whether fireworks can boost the player while using this Elytra. |
| `firework.consume` | `true` | Whether an accepted boost consumes the firework rocket. |
| `firework.cooldown_ticks` | `0` | Required delay between accepted boosts. |
| `durability_damage_multiplier` | `1.0` | Multiplier applied to durability damage. |
| `collision_damage_multiplier` | `1.0` | Multiplier applied to `FLY_INTO_WALL` damage. |

There are 20 ticks in one second. For example, this limits continuous flight to 30 seconds:

``` yaml
max_flight_ticks: 600
```

All tick values must be non-negative integers. Damage multipliers must be non-negative finite numbers.

## Firework examples

### Disable firework boosting

``` yaml
firework:
  enabled: false
```

The restriction applies only while the player is gliding with this exact custom Elytra.

Vanilla Elytras and other custom Elytras are not affected.

### Allow boosts without consuming rockets

``` yaml
firework:
  enabled: true
  consume: false
  cooldown_ticks: 0
```

### Add a two-second cooldown

``` yaml
firework:
  enabled: true
  consume: true
  cooldown_ticks: 40
```

The cooldown is cleared when the tracked glide ends.

## Damage multipliers

### Half durability damage

``` yaml
durability_damage_multiplier: 0.5
```

### Disable durability damage

``` yaml
durability_damage_multiplier: 0
```

### Double wall collision damage

``` yaml
collision_damage_multiplier: 2.0
```

### Disable wall collision damage

``` yaml
collision_damage_multiplier: 0
```

These multipliers affect only the exact custom Elytra being worn. Vanilla Elytras and other custom Elytras keep their own behavior.

ItemsAdder does not replace the vanilla enchantment system. Applicable enchantments such as Unbreaking and Mending continue to work normally.

## Events and actions

Custom Elytras provide four additional item events:

| Event | Trigger |
| --- | --- |
| `glide_start` | The player starts gliding with this custom Elytra. |
| `glide_stop` | The tracked glide ends, times out, or the Elytra is unequipped. |
| `elytra_boost` | A permitted firework boost is used. |
| `elytra_collision` | The player receives `FLY_INTO_WALL` collision damage. |

They use the normal ItemsAdder action system:

``` yaml
events:
  glide_start:
    play_sound:
      name: entity.ender_dragon.flap
      volume: 1
      pitch: 1.2

  glide_stop:
    play_sound:
      name: block.note_block.bass
      volume: 1
      pitch: 0.8

  elytra_boost:
    play_sound:
      name: entity.firework_rocket.launch
      volume: 1
      pitch: 1.3

  elytra_collision:
    play_sound:
      name: entity.player.hurt
      volume: 1
      pitch: 0.7
```

Any existing item action can be used inside these events.

## Full example

``` yaml
info:
  namespace: my_elytras

items:
  sky_elytra:
    name: Sky Elytra
    material: ELYTRA

    graphics:
      texture: item/sky_elytra

    elytra:
      wings_texture: entity/equipment/wings/sky_elytra
      # Optional. If omitted, item/sky_elytra_broken.png is detected automatically.
      broken_item_texture: item/sky_elytra_broken

      # 0 means unlimited flight.
      max_flight_ticks: 0

      firework:
        enabled: true
        consume: true
        cooldown_ticks: 0

      durability_damage_multiplier: 1.0
      collision_damage_multiplier: 1.0

    events:
      glide_start:
        play_sound:
          name: entity.ender_dragon.flap
          volume: 1
          pitch: 1.2

      glide_stop:
        play_sound:
          name: block.note_block.bass
          volume: 1
          pitch: 0.8

      elytra_boost:
        play_sound:
          name: entity.firework_rocket.launch
          volume: 1
          pitch: 1.3

      elytra_collision:
        play_sound:
          name: entity.player.hurt
          volume: 1
          pitch: 0.7
```

## Using explicit equipment settings

The `elytra` section automatically creates the required equipment configuration. You can still add compatible equipment properties when needed:

``` yaml
equipment:
  sound: item.armor.equip_elytra
  dispensable: true
  swappable: true
  damage_on_hurt: true
```

If you explicitly configure the following properties, they must remain compatible with the Elytra:

``` yaml
equipment:
  id: my_elytras:sky_elytra
  slot: CHEST
  glider: true
  allowed_entities:
    - PLAYER
```

{% hint style="warning" %}
ItemsAdder rejects incompatible values instead of silently generating an invalid item.

The equipment ID must match the item ID, the slot must be `CHEST`, `glider` must be `true`, and `allowed_entities` must include `PLAYER`.
{% endhint %}

## Generating and testing the pack

1. Save the configuration and PNG files.
2. Run your normal ItemsAdder reload and resource-pack generation workflow, such as `/iazip`.
3. Make sure the updated resource pack is applied to the client.
4. Give yourself the item:

``` text
/iaget my_elytras:sky_elytra
```

5. Equip it in the chest slot.
6. Start gliding and check the wings texture.
7. Use a firework to test its boost rules.
8. Reduce its durability to verify the broken inventory texture.

## Troubleshooting

### The wings texture is missing

Check that:

- The client uses Minecraft 1.21.2 or newer.
- `wings_texture` points to an existing PNG.
- The namespace is correct.
- The updated resource pack was generated and accepted.
- The resource path is relative to the `textures` folder.

Example:

``` yaml
wings_texture: my_elytras:entity/equipment/wings/sky_elytra
```

Corresponding file:

``` text
contents/my_elytras/textures/entity/equipment/wings/sky_elytra.png
```

### The inventory texture changes, but the worn wings do not

`graphics.texture` and `broken_item_texture` control only the item shown in inventories and hands.

The worn model always uses `wings_texture`.

### The broken texture is not shown

If automatic selection is used, verify that the filename ends with `_broken.png`:

``` text
sky_elytra.png
sky_elytra_broken.png
```

Otherwise, configure it explicitly:

``` yaml
broken_item_texture: item/sky_elytra_broken
```

### Equipment settings are rejected

Remove unnecessary explicit equipment properties first. The `elytra` section already configures the chest slot, equipment ID, player compatibility, and glider component.

## Ready-to-use test examples

The following items can be used to verify the main Elytra properties. Create the referenced textures inside your namespace's `textures/item` folder.

``` yaml
info:
  namespace: my_elytras

items:
  baseline_elytra:
    name: Baseline Elytra
    material: ELYTRA
    graphics:
      texture: item/baseline_elytra
    durability:
      max_durability: 12
    elytra:
      wings_texture: item/baseline_elytra_wings
      max_flight_ticks: 0
      firework:
        enabled: true
        consume: true
        cooldown_ticks: 0
      durability_damage_multiplier: 1.0
      collision_damage_multiplier: 1.0

  timed_safe_elytra:
    name: Timed Safe Elytra
    material: ELYTRA
    graphics:
      texture: item/timed_safe_elytra
    durability:
      max_durability: 12
    elytra:
      wings_texture: item/timed_safe_elytra_wings
      max_flight_ticks: 100
      firework:
        enabled: false
        consume: true
        cooldown_ticks: 0
      durability_damage_multiplier: 0.5
      collision_damage_multiplier: 0.0

  boost_heavy_elytra:
    name: Boost Heavy Elytra
    material: ELYTRA
    graphics:
      texture: item/boost_heavy_elytra
    durability:
      max_durability: 12
    elytra:
      wings_texture: item/boost_heavy_elytra_wings
      broken_item_texture: item/boost_heavy_explicit_broken
      max_flight_ticks: 0
      firework:
        enabled: true
        consume: false
        cooldown_ticks: 60
      durability_damage_multiplier: 2.0
      collision_damage_multiplier: 2.0
```

- **Baseline Elytra:** preserves normal vanilla-like flight, firework consumption and damage.
- **Timed Safe Elytra:** stops gliding after 5 seconds, blocks firework boosts, halves durability damage and prevents wall-collision damage.
- **Boost Heavy Elytra:** provides free firework boosts with a 3-second cooldown and doubles durability and collision damage.

For the first two items, add `<normal_texture>_broken.png` to test automatic broken-texture detection. The third item demonstrates an explicit `broken_item_texture`.

## Example Elytras pack

{% content-ref url="https://www.spigotmc.org/resources/simplyelytras-8-custom-elytras.138297/" %}
[Example pack](https://www.spigotmc.org/resources/simplyelytras-8-custom-elytras.138297/)
{% endcontent-ref %}
