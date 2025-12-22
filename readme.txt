================================================================================
PHB 2024 - Bladesinger (Heroes of Faerun)
================================================================================

Version: 1.0.0
Author: Your Name
Category: Classes & Subclasses

================================================================================
DESCRIPTION
================================================================================

This mod extends "DnD PHB 2024 - All in One" to update the Bladesinger wizard
subclass to accurately match the 2024 Player's Handbook rules from Heroes of
Faerun.

================================================================================
REQUIREMENTS
================================================================================

* Baldur's Gate 3 (latest version)
* DnD PHB 2024 - All in One (REQUIRED DEPENDENCY)
* BG3 Mod Manager or Vortex Mod Manager

IMPORTANT: This mod MUST load AFTER "DnD PHB 2024 - All in One"

================================================================================
FEATURES
================================================================================

WEAPON RESTRICTIONS (2024 PHB Rules)
-------------------------------------
✓ Works with ALL one-handed weapons (daggers, shortswords, maces, handaxes, etc.)
✓ Works with versatile weapons (longswords, warhammers, battleaxes, quarterstaffs)
✓ Works automatically with modded one-handed weapons
✓ Mid-combat enforcement: Bladesong ends instantly if blocked weapon equipped
✗ Blocks two-handed weapons (greatswords, greataxes, mauls, pikes, etc.)
✗ Medium/Heavy armor blocks Bladesong
✗ Shields block Bladesong

BLADESONG (Level 3)
-------------------
* Uses per day: Equal to Intelligence modifier (minimum 1)
* AC Bonus: Full Intelligence modifier
* Movement: +10 feet while active
* Advantage on Acrobatics checks
* Use Intelligence for weapon attack rolls and damage
* Intelligence bonus to Concentration saves
* Bonuses persist when swapping weapons mid-combat

TRAINING IN WAR AND SONG (Level 3)
-----------------------------------
* Martial weapon proficiencies
* Weapons usable as spellcasting focus
* Performance skill proficiency

EXTRA ATTACK (Level 6)
----------------------
* Can replace one attack with a cantrip

SONG OF DEFENSE (Level 10)
--------------------------
* Reduce damage by 5× spell slot level as a reaction

SONG OF VICTORY (Level 12)
--------------------------
* Make bonus action weapon attack after casting a spell

================================================================================
INSTALLATION
================================================================================

BG3 MOD MANAGER:
1. Download the .pak file
2. Drag and drop into BG3 Mod Manager
3. Ensure it loads AFTER "DnD PHB 2024 - All in One"
4. Click "Save Load Order"
5. Launch the game

VORTEX MOD MANAGER:
1. Download the mod archive
2. Click "Install From File"
3. Enable the mod
4. Ensure it loads AFTER "DnD PHB 2024 - All in One"
5. Deploy mods
6. Launch the game

MANUAL INSTALLATION:
1. Download and extract the .pak file
2. Copy to: %LOCALAPPDATA%\Larian Studios\Baldur's Gate 3\Mods\
3. Edit modsettings.lsx to add this mod's UUID after PHB 2024 mod
4. Launch the game

================================================================================
WHAT'S CHANGED FROM PHB 2024 MOD
================================================================================

The base "DnD PHB 2024 - All in One" mod already implements most Bladesinger
features correctly. This extension makes these specific corrections:

1. Weapon restrictions: Changed from whitelist to blacklist approach
2. Modded weapon support: Automatically works with any one-handed weapon
3. Intelligence bonuses: Now persist through weapon swaps
4. Movement speed: Corrected to +10 feet (was +15)
5. Mid-combat enforcement: Bladesong ends immediately with invalid weapons
6. UI cleanup: Removed "Bladesong Impeded" condition indicators
7. Martial weapons: Explicitly grants proficiency at level 3

================================================================================
COMPATIBILITY
================================================================================

COMPATIBLE WITH:
* Most mods that don't modify Bladesinger
* Weapon mods (blacklist approach supports modded weapons)

MAY CONFLICT WITH:
* Other mods that modify the Bladesinger subclass

INCOMPATIBLE WITH:
* Mods that completely replace the Bladesinger subclass

================================================================================
KNOWN ISSUES
================================================================================

None currently. All features working as intended:
* Weapon restrictions work correctly
* INT bonuses persist through weapon swaps
* UI indicators properly hidden
* Mid-combat enforcement working

================================================================================
TROUBLESHOOTING
================================================================================

Q: Game crashes on startup
A: Check load order - this mod MUST load AFTER "DnD PHB 2024 - All in One"

Q: Bladesong won't activate
A: Ensure you're not wearing medium/heavy armor or shield, and wielding a
   one-handed or versatile weapon (not greatsword, greataxe, etc.)

Q: Intelligence bonuses not applying
A: Bladesong must be active. Try re-equipping weapon if bonuses don't appear
   immediately after swapping.

Q: Mod doesn't load
A: Verify load order in your mod manager. Must load after "DnD PHB 2024 - All
   in One"

================================================================================
CREDITS
================================================================================

* Larian Studios - Baldur's Gate 3
* Wizards of the Coast - D&D 5e and 2024 Player's Handbook
* DnD PHB 2024 mod team - Base implementation this mod extends

================================================================================
LICENSE
================================================================================

This mod is provided as-is for personal use.
All D&D content belongs to Wizards of the Coast.

================================================================================
CHANGELOG
================================================================================

Version 1.0.0 (Initial Release)
* Switched to blacklist weapon approach (allows modded weapons)
* Blocks two-handed non-versatile weapons only
* INT bonuses persist through weapon swaps
* Mid-combat weapon enforcement
* Movement speed corrected to +10 feet
* Hides "Bladesong Impeded" UI indicators
* Cleaned up ability tooltips
* Martial weapon proficiency at level 3

For detailed changelog, see: https://github.com/holypolarpanda7/PHB-2024---Bladesinger/releases

================================================================================