# PHB 2024 - Bladesinger (Heroes of Faerun)

Extension mod for "DnD PHB 2024 All in One" that updates the Bladesinger wizard subclass to match the **Heroes of Faerun** (2024 PHB) rules.

## Features

This mod overrides the Bladesinger subclass to accurately reflect the 2024 D&D Player's Handbook version:

### Weapon Restrictions Updated (NEW)
- ✅ **Allows all one-handed weapons** (daggers, shortswords, maces, handaxes, etc.)
- ✅ **Allows versatile weapons** (longswords, warhammers, battleaxes, quarterstaffs, etc.)
- ✅ **Allows modded weapons** (blacklist approach means any weapon without two-handed tag works)
- ✅ **Mid-combat enforcement** (Bladesong ends immediately if you equip a blacklisted weapon)
- ❌ **Blocks true two-handed weapons** (greatswords, greataxes, mauls, pikes, etc.)
- ❌ **Medium/Heavy armor blocks Bladesong**
- ❌ **Shields block Bladesong**

### Bladesong (Level 3)
- **Uses:** Equal to your Intelligence modifier (minimum of 1) ✓
- **AC Bonus:** Full Intelligence modifier (instead of scaling every 2 points) ✓
- **Movement:** +10 feet (+2 in BG3) instead of +15 feet ✓
- **Advantage on Acrobatics** ✓
- **INT for weapon attacks:** Use Intelligence instead of STR/DEX (persists through weapon swaps) ✓
- **INT bonus to Concentration saves** ✓
- **Works with all one-handed and versatile weapons** ✓

### Training in War and Song (Level 3)
- Martial weapon proficiencies ✓
- Weapon as spellcasting focus ✓
- Skill proficiency choice (Performance) ✓

### Extra Attack (Level 6)
- Can replace one attack with a cantrip ✓

### Song of Defense (Level 10)
- Reduce damage by 5× spell slot level as a reaction ✓

### Song of Victory (Level 12)
- Bonus action weapon attack after casting a spell ✓
- (Level 14 in tabletop, moved to 12 for BG3's level cap)

## How This Mod Works

This mod extends "DnD PHB 2024 All in One" by selectively overriding files to adjust Bladesinger mechanics.

### Technical Implementation

#### 1. Condition Script Override (Weapon Restrictions)
**File**: `Scripts/thoth/helpers/BladesongAvailable.khn`

Replaces PHB 2024's weapon whitelist with a **blacklist approach** that blocks two-handed non-versatile weapons. This allows **ALL** one-handed weapons (including modded weapons), versatile weapons, and ranged weapons.

```lua
function BladesongAvaliable()
    local hasTwohandedMelee = WieldingWeapon('Twohanded', false, false, context.Source) 
                            & WieldingWeapon('Melee', false, false, context.Source)
    local hasVersatile = WieldingWeapon('Versatile', false, false, context.Source)
    
    -- Block two-handed non-versatile melee weapons
    local invalidWeapon = hasTwohandedMelee & ~hasVersatile
    
    -- Keep armor/shield checks from original
    local hasHeavyArmor = HasHeavyArmor(context.Source)
    local hasMediumArmor = HasMediumArmor(context.Source)
    local hasShield = HasShieldEquipped(context.Source)
    local isImpeded = HasStatus('BLADESONG_IMPEDED')
    
    return ~(invalidWeapon | hasHeavyArmor | hasMediumArmor | hasShield | isImpeded)
end
```

**Key Features:**
- Uses `WieldingWeapon()` to check weapon flags
- Checks for both 'Twohanded' AND 'Melee' flags together
- Allows versatile weapons even though they have 'Twohanded' flag
- Preserves original armor and shield restrictions
- Runs on every Bladesong activation attempt

#### 2. INT-Based Weapon Bonuses (Equipment Status System)
**Files**: `Status_BOOST.txt` and `Passive.txt`

PHB 2024 base mod grants Intelligence-based weapon attack/damage bonuses through **equipment statuses**, which are applied to specific weapon items. This system requires special handling to persist through weapon swaps.

**BLADESONG Status** - Applies equipment statuses when activated:
```
data "OnApplyFunctors" "ApplyEquipmentStatus(MeleeMainHand,BLADEWORK_CUSTOM,100,-1);ApplyEquipmentStatus(MeleeOffHand,BLADEWORK_CUSTOM,100,-1);ApplyEquipmentStatus(RangedMainHand,BLADEWORK_CUSTOM,100,-1)"
data "Passives" "Bladesong_Equipment_Monitor"
```

**BLADEWORK_CUSTOM** - Equipment status with INT override:
```
data "Boosts" "WeaponAttackRollAbilityOverride(Intelligence);WeaponDamage(1d4,Intelligence)"
```

**Bladesong_Equipment_Monitor** - Passive that reapplies equipment status on weapon changes:
```
data "StatsFunctorContext" "OnTurn;OnEquip;OnCombatStarted;OnStatusApplied"
data "StatsFunctors" "ApplyEquipmentStatus(MeleeMainHand,BLADEWORK_CUSTOM,100,10);ApplyEquipmentStatus(MeleeOffHand,BLADEWORK_CUSTOM,100,10);ApplyEquipmentStatus(RangedMainHand,BLADEWORK_CUSTOM,100,10)"
```

**Why This Approach:**
- `WeaponAttackRollAbilityOverride(Intelligence)` **only works on equipment statuses**, not character passives
- Equipment statuses apply to specific item entities, not equipment slots
- When you swap weapons, the old weapon keeps its equipment status but the new weapon doesn't have it
- Monitoring passive reapplies bonuses automatically when weapons are swapped
- Triggers on: turn start, equipment change, combat start, and status application

#### 3. Mid-Combat Weapon Enforcement
**File**: `Status_BOOST.txt`

**BLADESONG_WEAPON_WATCHER** - Auto-removes Bladesong if blacklisted weapon equipped:
```
new entry "BLADESONG_WEAPON_WATCHER"
type "StatusData"
data "StatusType" "BOOST"
data "RemoveEvents" "OnTurn;OnEquip"
data "RemoveConditions" "WieldingWeapon('Twohanded',false,false,context.Source) & WieldingWeapon('Melee',false,false,context.Source) & ~WieldingWeapon('Versatile',false,false,context.Source) & HasStatus('BLADESONG')"
data "OnRemoveFunctors" "IF(HasStatus('BLADESONG')):RemoveStatus(BLADESONG)"
```

**Key Features:**
- Applied automatically when Bladesong activates
- Checks weapon validity on turn start AND equipment change (**instant enforcement**)
- Automatically ends Bladesong when equipping two-handed non-versatile melee weapons mid-combat
- Conditional removal (`IF(HasStatus('BLADESONG'))`) prevents interference with Bladesong reactivation
- Removes itself when Bladesong ends

#### 4. Movement Speed Correction
**File**: `Passive.txt`

Changed movement bonus from `ActionResource(Movement,3,0)` to `ActionResource(Movement,2,0)` to match PHB 2024 rules (+10 feet instead of +15 feet).

#### 5. Bladesong Tooltip Cleanup
**File**: `Spell_Shout.txt`

Clears tooltip warning fields to remove weapon restriction text from the Bladesong ability tooltip:
```
data "ExtraDescription" ""
data "ExtraDescriptionParams" ""
data "TooltipStatusApply" ""
data "TooltipPermanentWarnings" ""
data "TooltipOnMiss" ""
data "TooltipOnSave" ""
```

#### 6. Status Visibility Suppression
**Files**: `Status_BOOST.txt` and Localization

Completely hides all weapon/armor/shield restriction status indicators that the base game applies:

**Status Overrides** - Makes all MESSAGE statuses invisible:
```
new entry "BLADESONG_WEAPON_MESSAGE"
type "StatusData"
using "BLADESONG_WEAPON_MESSAGE"
data "StatusPropertyFlags" "DisableOverhead;DisableCombatlog;DisablePortraitIndicator;ApplyToDead;IgnoreResting"
data "Icon" ""
data "DisplayName" ""
data "Description" ""
data "StatusType" "INVISIBLE"
```

Applied to: BLADESONG_IMPEDED, BLADESONG_WEAPON, BLADESONG_SHIELD, BLADESONG_ARMOR, BLADESONG_UNARMED, BLADESONG_WEAPON_MESSAGE, BLADESONG_SHIELD_MESSAGE, BLADESONG_ARMOR_MESSAGE, BLADESONG_UNARMED_MESSAGE

**Localization Blanking** - Removes text from status displays by blanking localization handles for all DisplayName and Description fields.

**Why This is Needed:**
- Base game applies BLADESONG_WEAPON_MESSAGE status when wielding non-approved weapons
- Without these overrides, "Bladesong Impeded" condition appears in character sheet and above portrait
- Overriding to INVISIBLE status type plus blanking localization completely hides these cosmetic statuses

#### 7. Martial Weapons Proficiency
**File**: `Progressions.lsx`

Ensures martial weapon proficiency is granted at level 3:
```xml
<attribute id="Boosts" type="LSString" value="Proficiency(MartialWeapons)"/>
```

This progression override also removes the passives that apply the "Bladesong Impeded" status indicators from the base game/PHB 2024 mod.

### Why Script Override is Required

The weapon restrictions in PHB 2024 are implemented via condition scripts that check specific weapon types (dagger, longsword, rapier, etc.). Simply removing passives or changing progressions doesn't work because:

1. PHB 2024's condition script still runs and blocks non-approved weapons
2. Khonsu scripts can override by matching the exact function name and path
3. Our `BladesongAvailable.khn` completely replaces PHB 2024's implementation

### Development Notes

**Critical Functions Used:**
- `WieldingWeapon(weaponFlag, offHand, checkBothSets, entity)` - checks weapon properties with STRING flags
- `HasHeavyArmor(entity)` - checks for heavy armor
- `HasMediumArmor(entity)` - checks for medium armor
- `HasShieldEquipped(entity)` - checks for equipped shield
- `HasStatus(statusName)` - checks active status

**Why Equipment Statuses for INT Bonuses:**
- Character-level passives can add bonuses but cannot change the base attack ability
- `WeaponAttackRollAbilityOverride()` only works on equipment statuses
- Equipment statuses apply to item entities, not players
- Weapon swaps require monitoring passive to reapply equipment status to new weapon

**Development References:**
- [BG3 Condition Functions](https://gist.github.com/Norbyte/5628f9787b39741bfbe0918be4c823c2) - Official function list
- [BG3 Khn Library](https://github.com/NellyDevo/Bg3-Khn-Libs) - Type definitions and examples

## Installation

### Requirements
- **Baldur's Gate 3** (latest version)
- **DnD PHB 2024 All in One** mod (REQUIRED DEPENDENCY)
- **BG3 Mod Manager** or **Vortex Mod Manager**

### Steps

1. Download the latest release
2. Install using your mod manager:
   - **BG3 Mod Manager:** Drag and drop the `.pak` file
   - **Vortex:** Install from archive
3. Ensure load order: This mod must load **AFTER** "DnD PHB 2024 All in One"
4. Enable the mod and launch the game

### Manual Installation
1. Extract the mod
2. Copy the `.pak` file to: `%LOCALAPPDATA%\Larian Studios\Baldur's Gate 3\Mods\`
3. Edit `modsettings.lsx` to include this mod's UUID with the proper load order

## What's Changed from PHB 2024 Mod

The "DnD PHB 2024 All in One" mod already implements most of the Bladesinger correctly. This extension makes these specific adjustments:

1. **Weapon Restrictions Changed**: Switched from whitelist (daggers, longswords, rapiers) to blacklist (blocks two-handed non-versatile)
2. **Modded Weapon Support**: Blacklist approach means any weapon without two-handed tag works
3. **Mid-Combat Enforcement**: Bladesong now ends immediately when equipping blacklisted weapons
4. **INT Bonus Persistence**: INT-based weapon bonuses now persist through weapon swaps
5. **Movement Speed**: Corrected from +15 feet (+3) to **+10 feet (+2)** while Bladesinging
6. **UI Cleanup**: Removed "Bladesong Impeded" condition indicators and cleaned up ability tooltips
7. **Martial Weapons**: Explicitly grants martial weapon proficiency at level 3

These changes ensure the subclass matches the official 2024 Player's Handbook exactly while supporting modded weapons.

## Compatibility

- ✅ **Compatible with:** Most mods that don't modify Bladesinger
- ✅ **Compatible with:** Weapon mods (blacklist approach allows modded one-handed weapons)
- ⚠️ **May conflict with:** Other mods that modify the Bladesinger subclass
- ❌ **Incompatible with:** Mods that completely replace the Bladesinger subclass

## Known Issues

None currently known. All weapon restrictions work correctly, INT bonuses persist through weapon swaps, and UI indicators are properly hidden.

## Troubleshooting

### Game Crashes on Startup
- **Cause**: Mod conflicts or load order issues
- **Fix**: Ensure this mod loads AFTER "DnD PHB 2024 All in One"

### Bladesong Doesn't Activate
- Check that you're not wearing medium/heavy armor or a shield
- Check that you're wielding a one-handed or versatile weapon (not greatsword, greataxe, maul, pike, etc.)

### INT Bonuses Not Applying
- Bladesong must be active for INT bonuses to apply
- Try re-equipping your weapon if bonuses don't appear immediately

### Mod Doesn't Load
- Verify load order: This mod MUST load after "DnD PHB 2024 All in One"
- Check modsettings.lsx includes this mod's UUID
- Ensure pak file is properly deployed to mod folder

## Building from Source

If you want to pack the mod yourself using LSLib's Divine tool:

```bash
divine.exe -g bg3 -a create-package \
  -s "d:/BG3Modding/Projects/PHB-2024---Bladesinger" \
  -d "d:/vortex/BG3/mods/PHB2024-Bladesinger_2eb0f48a-2f6d-05dc-8236-95b59e998637/PHB2024-Bladesinger_2eb0f48a-2f6d-05dc-8236-95b59e998637.pak"
```

**Important**: When using Vortex Mod Manager, the pak file must be created at the path with the UUID in the folder name that Vortex's symlink points to.

## Credits

- **Larian Studios** - Baldur's Gate 3
- **Wizards of the Coast** - D&D 5e and the 2024 Player's Handbook
- **DnD PHB 2024 mod team** - Base implementation this mod extends

## License

This mod is provided as-is for personal use. All D&D content belongs to Wizards of the Coast.

## Changelog

### Version 1.0.0 (Current)
- Initial release
- Switched from whitelist to blacklist weapon approach (allows modded weapons)
- Blocks true two-handed non-versatile weapons (greatswords, greataxes, mauls, pikes)
- Implemented equipment status monitoring for INT bonuses that persist through weapon swaps
- Added mid-combat weapon enforcement (instant removal when equipping blacklisted weapon)
- Corrects movement speed from +15 feet (+3) to +10 feet (+2)
- Completely hides "Bladesong Impeded" condition indicators from UI
- Cleaned up Bladesong ability tooltips
- Maintains armor/shield restrictions from PHB 2024 base
- Compatible with PHB 2024's INT-based scaling features
- Explicitly grants martial weapon proficiency at level 3
- Implemented equipment status monitoring for INT bonuses that persist through weapon swaps
- Added mid-combat weapon enforcement (instant removal when equipping blacklisted weapon)
- Corrects movement speed from +15 feet (+3) to +10 feet (+2)
- Maintains armor/shield restrictions from PHB 2024 base
- Compatible with PHB 2024's INT-based scaling features
