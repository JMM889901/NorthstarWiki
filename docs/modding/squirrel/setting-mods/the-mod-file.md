Mod time
========
lets actually get started then on making a setting mod, this will involve the making of 3 things for a simple one. a mod.json, a language file and the mod itself

lets get started with the mod itself

The mod
=======
to begin with we need to answer the simple question of "what are we making" for our example lets make a simple randomiser than randomises your weapon on each spawn.

Because this is a setting mod it will only need to be installed on the serverside but it also wont appear in the browser unless the host puts it in the name.

so lets get started with our **initial function**

The initial function
---------
The initial function is the function that is called on server startup and contains 2 important things.
the **callbacks** and the **setting buttons** to add the settings to the private match settings we need to use a new function:

`AddPrivateMatchModeSettingEnum("string", "string", ["#SETTING_ENABLED", "#SETTING_ENABLED"], "0")`

this might look complicated, but really its just (Category, settingname, [setting options], default value) however we use terms like "#MODE_SETTING_CATEGORY_RANDOMISER" in place of the category name so that we can create language files for different languages.
(we will make that later)
```cpp
void function simplerandomiser_init(){
  AddPrivateMatchModeSettingEnum("#MODE_SETTING_CATEGORY_SIMPLERANDOMISER", "SimpleRandomiser", ["#SETTING_ENABLED", "#SETTING_ENABLED"], "0")
  
  #if SERVER
  AddCallback_OnPlayerRespawned(GiveRandomGun)
  #endif
  }
  ```
As you may have noticed, checking if it is a server is a special case, so we use #if SERVER and #endif instead of the usual if(thing){stuff} 

Now that our initial function is created we now have the game triggering `GiveRandomGun` on spawn, but we dont have any such function, so lets make one. but before we can do that, we need to know what weapons we can equip. 
for this we define an array 

```cpp
array<string> pilotWeapons = [
		"mp_weapon_alternator_smg",
		"mp_weapon_autopistol",
		"mp_weapon_car",
		"mp_weapon_dmr"]
```
here we have defined an array with only 4 weapons in it, you can make this list as long or as short as you like but remember to seperate all but the last item with a ,

Now lets make a function to check if you enabled the setting
```cpp
bool
Next lets make the randomise function:
	bool function SimpleRandomiserEnabled() 
		return GetCurrentPlaylistVarInt("SimpleRandomiser", 0) == 1
```
    
Randomise function
----------------
As we already know its going to call GiveRandomGun on respawn, lets define that now.

First we strip any existing weapons:
```cpp
void function GiveRandomGun(entity player){
  foreach ( entity weapon in player.GetMainWeapons() )
    player.TakeWeaponNow( weapon.GetWeaponClassName() )
```
this iterates through each weapon and removes them individually. 

Then lets give them a new, random weapon by selecting a random item from our previous array:
```cpp
  player.GiveWeapon(pilotweapons[RandomInt(pilotweapons.len())])
```
And done, surprisingly short script huh?
```cpp
void function simplerandomiser_init(){
  AddPrivateMatchModeSettingEnum("#MODE_SETTING_CATEGORY_SIMPLERANDOMISER", "SimpleRandomiser", ["#SETTING_ENABLED", "#SETTING_ENABLED"], "0")
  
  #if SERVER
  AddCallback_OnPlayerRespawned(GiveRandomGun)
  #endif
  }

array<string> pilotWeapons = [
		"mp_weapon_alternator_smg",
		"mp_weapon_autopistol",
		"mp_weapon_car",
		"mp_weapon_dmr"]

void function GiveRandomGun(entity player){
  foreach ( entity weapon in player.GetMainWeapons() )
    player.TakeWeaponNow( weapon.GetWeaponClassName() )
  player.GiveWeapon(pilotweapons[RandomInt(pilotweapons.len())])
}
```

Alas **we arent done yet** We still need the **mod.json** and the **language file**

Name this sh_SimpleRandomiser.gnut and place it in the `yourmodsname/mod/scripts/vscripts/` folder
