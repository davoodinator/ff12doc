# eitm_data.bin file structure and information
eitm_data.bin holds information on weapons, armor, and accessories. The file is in __little endian__ format.

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
```
uint weapon_id // If you add 2048 to this number it will match up in the XMLs
ushort item_type // Item Type or Category
uchar dmg_formula // Damage Formula?
uchar // ??
uchar order_byte_a
uchar order_byte_b
uchar[6] // Padding
uchar ?? // Values 0x00, 0x01, 0x02
uchar ?? // Values 0x12, 0x17, 0x18, 0x19, 0x12, 0x1a, 0xff
ushort gil_value
uchar[4] // Padding
uchar armor_eva // Something different for weapons
uchar armor_meva // Something different for weapons
uchar weap_atk // Something different for armor
uchar weap_knockback // Something different for armor
uchar crit_combo_rate
uchar weap_eva
uchar elm_effect
uchar on_hit_chance
uchar bad_effect_1
uchar bad_effect_2
uchar good_effect_1
uchar good_effect_2
uchar ?? // 
uchar ?? // THese three bytes may be related to icons and models
uchar ?? //
uchar ct
uint attributes // Offset to attribute (attribute_start_byte + this value)
uchar ??
uchar ?? // 0x00 or 0x80
uchar[2] // Padding
uchar // ??
uchar // ?? Flag (0x00, 0x01) or part of uchar before to make a ushort value
uchar render_model // 0x00 or 0x77
uchar // Padding for 52 byte
```

## Attributes
Item attributes are a 24 byte structure
```
ushort HP
ushort MP
uchar STR
uchar MAG
uchar VIT
uchar SPD
ushort bad_status
ushort good_status
ushort immunity
uchar ?? // Potentially EOF flag or Ribbon flag
uchar // Padding
uchar elm_absorb
uchar elm_immune
uchar elm_half
uchar elm_weak
uchar elm_potency
uchar[3] // Padding
```

## Elemental and Status Values
### Elemental
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

### On-Equip (Good) Statuses
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

### (Bad) Status Immunities
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

## Credits
* SwarnaD
* shinseikuevangerion
* phoenik
* watafuzz

--- Original information

Weapons start at test_battle/in/binaryfile/eitm_data.bin from at most offset 0x6C (Broadsword).
NOTE: Changing this file will not directly modify your game depending on your region setting.
A copy of all the data is located in text_battle/us/binaryfile/battle_pack.bin
Most people will want to modify this file instead. Re-locate the Broadsword sequence here.

Not sure about start and end-points per weapon but each is definitely 52 bytes in length:

Known Weapon Fields per Byte (Starting from offset 0x6C and repeating every 52 bytes):
Weapon Type? (0x0A for melee, 0x64 for ranged, 0x14 for Kumbha, 0xC8 for Seitengrat)
Damage Formula
ATK
??? (0x00 or 0x0A depending on weapon type, e.g. all Spears are 0A and all Ninja Swords are 00)
Combo Rate
Evasion
Elemental Effect
On-Hit Status Effect Inflict Chance
On-Hit "Bad" Status Effect Group 1
On-Hit "Bad" Status Effect Group 2
On-Hit "Good" Status Effect Group 1 (e.g. Faith=0x20)
On-Hit "Good" Status Effect Group 2 (e.g. Berserk=0x80, Regen=0x02)
4 Bytes -> Weapon Classifications/Icons? Haven't explored in-depth (For example, Spears are equal to eachother here, but 2H and 1H swords get differentiated)
Special Property Group(s) (I've looked at stat and elemental potency boosts so far)
^ Probably spans more than 1 byte
Not sure about the rest or where weapons are separated.

Status and elemental effects seem to share the same values/arithmetic system as in armor and accessories.
Multiple effects correspond to an addition of their respective values.
http://steamcommunity.com/app/595520/discussions/0/1700541698690214622/?ctp=9#c1693788384127697292
http://steamcommunity.com/app/595520/discussions/0/1700541698690214622/?ctp=9#c1693788384143259058

Damage Formula Values:
0x14 Strength(Sword, Spear, Rod)
0x15 Speed (Ninja Sword, Dagger)
0x16 Magick (Katana, Staff)
0x17 Vitality (Axe, Hammer, Hand-bomb)
0x18 Pole
0x19 Bow
0x1A Cross-bow
0x1B Pierce (Gun, Measures, Healing Rods)
0x68 Mace

Some observed Special Properties in the first byte (unverified but probably correct):
18: Potency: Water (Fumarole, Six-fluted Pole)
30: Potency: Fire (Burning Bow)
60: +2 MAG (most Rods, Oak Staff)
C0: +3 MAG and Wind Boost (Cherry Staff)
I don't know if these use the same kind of arithmetic as other item properties.

