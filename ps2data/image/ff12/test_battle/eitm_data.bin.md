# eitm_data.bin file structure and information
eitm_data.bin holds information on weapons, armor, and accessories. The file is in __little endian__ format.

## Important Note
Editing this file directly will have no effect on the game. This file is combined with other binaries into the `/test_battle/(locale)/binaryfile/battle_pack.bin`. Searching for the header inside of that file will give you the starting point of the file up until the next header (`73 74 32 65`).

## Header
The header is 32 byte in size with the following structure
```
char[4] file_type
uint total_items
uint item_byte_size
uint item_start_byte // Pointer to where the items list starts
uchar[4] padding // ? Assumed
uchar[4] padding // ? Assumed
uint attribute_start_byte // Pointer to where the attributes list starts
uchar[4] padding // ? Assumed, to get struct to even 32 bytes.
```

__Note:__ It's odd that the header contains information about the item total and item structure size, but is 0'd out for what could be information about attributes. Maybe someone can shine a light on this based on the header information in other files.

## Items
Items are organized in 52 byte structures

| Byte Reference | Data Type | Data Description | Notes |
| -------------- | --------- | ---------------- | ----- |
| `0x00-0x03` | uint | Weapon ID | Add 2048 to get the value in the save editor XML. Some values may not line up correctly due to zodiac items being added |
| `0x04-0x05` | ushort | Item Type | |
| `0x06` | uchar | **??** | Follows the grouping of item type **Needs testing** |
| `0x07` | uchar | **??** | A ENUM of some sort. Follows the power of 4 **Needs Testing** |
| `0x08` | uchar | Order A | The order in which the item will appear within its item type |
| `0x09` | uchar | Order B | The order in which the item will appear relative to all other items |
| `0x0a-0x0f` | uchar[6] | `0x00` Padding | |
| `0x10` | uchar | Metal Vdalue | The metal value of the item (`0x00, 0x01, 0x02, 0x03, 0x05`) |
| `0x11` | uchar | **??** | Values are `0x12, 0x17, 0x18, 0x19, 0x12, 0x1a, 0xff` **Needs Testing** |
| `0x12-0x13` | ushort | Gil Value | |
| `0x14-0x17` | uchar[4] | `0x00` Padding |  Bytes `0x14-0x15` might be related to Gil Value for uint data type |
| `0x18` | uchar | Shield Evasion / Armor Defense | |
| `0x19` | uchar | Weapon Damage Formula / Shield M.Eva / Armor M.Resist | See [weapon damage formula section](#weapon-dmg-formula) for specific values |
| `0x1a` | uchar | Weapon ATK / **??** | Unknown what this does for armor **Needs Testing** |
| `0x1b` | uchar | Weapon Knockback / **??** | Uknown what this does for armor **Needs Testing** |
| `0x1c` | uchar | Critical % / Combo Rate | |
| `0x1d` | uchar | Weapon Evasion | |
| `0x1e` | uchar | Elemental Effect | See [elemental effects](#elemental-values) for specific values |
| `0x1f` | uchar | On-Hit Chance % | |
| `0x20` | uchar | Bad Effect 1 | See [bad status effects group 1](#group-1-1) for specific values |
| `0x21` | uchar | Bad Effect 2 | See [bad status effects group 2](#group-2-1) for specific values |
| `0x22` | uchar | Good Effect 1 | See [good status effects group 1](#group-1) for specific values |
| `0x23` | uchar | Good Effect 2 | See [good status effects group 2](#group-2) for specific values |
| `0x24` | uchar | **??** | Unknown. Bytes `0x24-0x26` may be related to icon and model **Needs Testing** |
| `0x25` | uchar | **??** | Unknown, see byte `0x24` |
| `0x26` | uchar | **??** | Unknown, see byte `0x24` |
| `0x27` | uchar | CT | |
| `0x28-0x2b` | uint | Additional Attributes | See [attributes](#attributes) section for more information |
| `0x2c` | uchar | **??** | Unknown |
| `0x2d` | uchar | **??** | Values are 0x00 or 0x80 |
| `0x2e-0x2f` | uchar | `0x00` Padding | See notes for byte `0x30` |
| `0x30` | uchar | **??** | Bytes `0x2e-0x31` may all be related as a uint **Needs testing** |
| `0x31` | uchar | **??** | See note above. Values here are either `0x00` or `0x01` |
| `0x32` | uchar | Render Model | Flag values `0x00` (Invisible) or `0x77` (Visible) |
| `0x33` | uchar | `0x00` Padding | |

<details>
    <summary>
    Table as code block
    </summary>

    0x00-0x03 uint weapon_id // If you add 2048 to this number it will match up in the XMLs
    0x04-0x05 ushort item_type // Item Type or Category
    0x06 uchar // ?? Follows grouping of item type
    0x07 uchar // ENUM (4 ^ 1, 2, 3, 4 ...)
    0x08 uchar order_byte_a
    0x09 uchar order_byte_b
    0x0a-0x0f uchar[6] // Padding
    0x10 uchar metal_value // 0x00, 0x01, 0x02, 0x03, 0x05
    0x11 uchar ?? // Values 0x12, 0x17, 0x18, 0x19, 0x12, 0x1a, 0xff
    0x12-0x13 ushort gil_value
    0x14-0x17 uchar[4] // Padding
    0x18 uchar shield_eva, armor_def // Something different for weapons
    0x19 uchar weap_dmg_formula, shield_meva, armor_mresist
    0x1a uchar weap_atk // Something different for armor
    0x1b uchar weap_knockback // Something different for armor
    0x1c uchar crit_combo_rate
    0x1d uchar weap_eva
    0x1e uchar elm_effect
    0x1f uchar on_hit_chance
    0x20 uchar bad_effect_1
    0x21 uchar bad_effect_2
    9x22 uchar good_effect_1
    0x23 uchar good_effect_2
    0x24 uchar ?? // 
    0x25 uchar ?? // THese three bytes may be related to icons and models
    0x26 uchar ?? //
    0x27 uchar ct
    0x28-0x2b uint attributes // Offset to attribute (attribute_start_byte + this value)
    0x2c uchar ??
    0x2d uchar ?? // 0x00 or 0x80
    0x2e-0x2f uchar[2] // Padding
    0x30 uchar // ??
    0x31 uchar // ?? Flag (0x00, 0x01) or part of uchar before to make a ushort value
    0x32 uchar render_model // 0x00 or 0x77
    0x33 uchar // Padding for 52 byte

</details>

## Attributes
Item attributes are a 24 byte structure

| Byte Reference | Data Type | Description | Notes |
| -------------- | --------- | ----------- | ----- |
| `0x01-0x02` | ushort | HP | |
| `0x03-0x04` | ushort | MP | |
| `0x05` | uchar | STR | |
| `0x06` | uchar | MAG | |
| `0x07` | uchar | VIT | |
| `0x08` | uchar | SPD | |
| `0x09` | uchar | Equip Bad Statuses Group 1 | See [bad status effects group 1](#group-1-1) |
| `0x0a` | uchar | Equip Bad Statuses Group 2 | see [bad status effects group 2](#group-2-1) | 
| `0x0b-0x0c` | uchar | Equip Good Statuses Group 1 | See [good status effects group 1](#group-1) |
| `0x0b-0x0c` | uchar | Equip Good Statuses Group 2 | See [good status effects group 2](#group-2) |
| `0x0d` | uchar | Immunities Group 1 | See [bad status effects group 1](#group-1-1) |
| `0x0e` | uchar | Immunities Group 2 | See [bad status effects group 2](#group-2-1) |
| `0x0f` | uchar | **??** | Ribbon Flag | |
| `0x10` | uchar | `0x00` Padding | |
| `0x11` | uchar | Elemntal Absorb | |
| `0x12` | uchar | Elemental Immune | |
| `0x13` | uchar | Elemental Half | |
| `0x14` | uchar | Elemental Weak | |
| `0x15` | uchar | Elemental Potency | |
| `0x16-0x18` | uchar[3] | `0x00` Padding | |

<details>
    <summary>
    Table as code block
    </summary>

    0x01-0x02 ushort HP
    0x03-0x04 ushort MP
    0x05 uchar STR
    0x06 uchar MAG
    0x07 uchar VIT
    0x08 uchar SPD
    0x09 uchar bad_status_1
    0x0a uchar bad_status_2
    0x0b uchar good_status_1
    0x0c uchar good_status_2
    0x0d uchar immunity_1
    0x0e uchar immunity_2
    0x0f uchar ?? // Potentially EOF flag or Ribbon flag
    0x10 uchar // Padding
    0x11 uchar elm_absorb
    0x12 uchar elm_immune
    0x13 uchar elm_half
    0x14 uchar elm_weak
    0x15 uchar elm_potency
    0x16-0x18 uchar[3] // Padding

</details>

## Weapon DMG Formula
```
14 Strength (Sword, Spear, Rod)
15 Speed (Ninja Sword, Dagger)
16 Magick (Katana, Staff)
17 Vitality (Axe, Hammer, Hand-bomb)
18 Pole
19 Bow
1A Cross-bow
1B Pierce (Gun, Measure, Healing Rods)
68 Mace
```

## Elemental Values
Elements can be grouped by adding their values together
```
01 Fire
02 Lightning
04 Ice
08 Earth
10 Water
20 Wind
24 Wind + Ice
40 Holy
80 Dark
```

## Statuses
### "Good" Statuses
#### Group 1
```
01 Disease
02 Lure
04 Protect
08 Shell
10 Haste
20 Bravery
40 Faith
80 Reflect
```
#### Group 2
```
01 Vanish
02 Regen
04 Float
08 Berserk
10 Bubble
20 Critical HP
40 Libra
80 X-Zone
```

### "Bad" Statuses
#### Group 1
```
01 Death/Instant KO
02 Stone
04 Petrify
08 Stop
10 Sleep
20 Confuse
40 Doom
80 Blind
```

#### Group 2
```
01 Poison
02 Silence
04 Sap
08 Oil
10 Reverse
20 Disable
40 Immobilize
80 Slow
```

## Additional Work Needed
* We're still missing how several values work for the item structure, please see ?? values above

## Credits
* SwarnaD
* shinseikuevangerion
* phoenik
* watafuzz

