Spriteguns: a mod for Minetest that adds guns using moving animated image_waypoints for visuals/aimpoints.
High Resolution Texture pack is available at https://elkien3.com/hiresguns.zip, do '/spritegunscale .2' ingame to fix scaling issue.

Using the guns:
	There are two main ways guns are loaded: through magazines or by inserting the ammunition directly into the firearm.

	Colt Army: 9mm cartridges
	Pardini: Pardini Magazine (9mm)
	Remington 870: 12 gauge shells
	Thompson: Thompson Magazine (9mm)
	Mini14: Mini14 Magazine: (7.62mm)
	CZ527: Cz527 Magazine (7.62mm)

	Loading a magazine: take the appropriate magazine for the gun you want to load, and put it in the crafting grid with the appropriate ammunition. Complete the craft and the ammunition will be loaded into the magazine. Put the magazine into the craft grid by itself and then craft to empty it.

	To load a firearm, make sure you have the appropriate ammunition or magazine in your inventory, Then press the 'zoom' button (default keybind is Z) to load.
	To unload, press the 'drop' button once. (default keybind is q)
	Pressing q while already unloading will drop the firearm.
	Empty magazines must be unloaded before a fresh magazine can be loaded.

	Bullets always go where the gun is pointed. (shocker) This is called the "aimpoint," But the aimpoint is not always in the middle of your screen.
	Right click to aim the weapon, you movement speed will be slower when aiming. Right click again to go back to hip fire.
	When you walk, the gun will move around a lot, and when standing still, it won't move as much, but will still move slightly over time.
	The idle aim drift will be faster if you are injured (medical mod), gassed (special grenade, not included), or are low on stamina.
	Aiming while hipfiring is a bit tricky, if you move your camera left, the point your gun is aiming at will move left, relative to the middle of your screen. this goes for all directions.
	When firing, the aimpoint will recoil upwards and sideways temporarily, then move back down to rest on a new position. Accurate fire will have to be aimed after every shot.
	Headshots do 50% more damage than body shots.
	Bullets will do less damage if they have to travel farther, depending on the weapon you are using.
	The Remington 870 shotgun has the lowest range, CZ527 has the highest.
	You can look at https://github.com/Elkien3/spriteguns/guns.lua for all gun stats.
	
Creating your own guns:
	It is not terribly hard to create your own guns, hardest part is making models, animating them, and rendering them.
	
	I will start with how they are registered in code, then move on to how to create the image assets for them.
	
	Registering guns:
	
		Below is a full registration for the Remington 870 shotgun. I've added description to all the settings.
		All you need to do is create a new mod, add spriteguns as a dependency, put this code in, then edit as needed.
		
		spriteguns.register_gun("spriteguns:remington870",{--The itemname of your gun for backend. Must use regular minetest itemstring conventions.
			description = "Remington 870 Shotgun",--The description for the item.
			inventory_image = "rem870_inv.png",--The image that represents the gun in your inventory
			zoomfov = 60,--The fov the player changes to when zooming/aiming. lower=more zoomed. default fov is 75
			scale = 7.5,--how big the image for the gun appears on the screen, suggested to leave this as is for 320x180 images.
			range = 100,--how far until the damage from the bullet goes to 0, at 1/2 range it will do 3/4 damage, 3/4 will do 1/2, etc.
			fire_sound = "rem870_fire",--the sound that plays when it is fired.
			fire_gain = 10,--volume of fire sound
			fire_sound_distant = "distant_hical",--the sound that carries farther than fire_sound
			size = 8,--how many bullets fit in the gun at a time. if it is a magazine fed gun you must make sure this is the same as the magazine capacity.
			magazine = false,--set to true if gun is magazine fed.
			loadtype = "manual",--"auto", "semi", and "manual". self explanatory, manual has an animation that must finish between slots.
			ammo = "spriteguns:bullet_12",--what kind of ammunition it uses. if it is a magazine fed gun, use the magazine itemstring.
			firetime = .2,--minimum time between shots.
			offsetrecoil = 120,--when firing, the gun will recoil more or less depending on this value
			targetrecoil = 80,--after firing, the aimpoint will rest closer or farther from the original location depending on this value.
			damage = 3,--how much damage each bullet/pellet does.
			maxdev = .16,--how far the gun can travel from the center before it is stopped.
			maxzoomdev = .06,--same as maxdev, but applies only when zoomed in.
			pellets = 12,--how many pellets/projectiles are sent out each time the gun is fired.
			space = 3,--if invspace is enabled, the gun will reduce your inventory slots by this amount.
			unload_amount = 1,--how many bullets are unloaded each animation cycle.
			concealed = false,--if true, gun will appear on your back and must be placed in the first hotbar slot to be used.
			spread = 35,--how far from the aimpoint the bullets will spread.
			textures = {--table containing all the names of the images for the sprite/image waypoint.
				prefix = "rem870_",--the prefix put in front of all the rest of the texture names. (eg: rem870_hipidle.png)
				hipidle = "hipidle.png",--for when gun is idle and not aimed
				hipidlenomag = "hipidle.png",--for when gun is idle, not aimed, and has no magazine in it.
				hipfire = "hipfireflash.png",--for when gun is firing but not aimed.
				hippostfire = "hipfire.png",--shows after firing but before gun can be fired again.
				aimidle = "aimidle.png",--same as hip, just when aiming with rightclick
				aimidlenomag = "aimidle.png",
				aimfire = "aimfireflash.png",
				aimpostfire = "aimfire.png",
				load = {--for manual guns: the animation that plays between shots.
					length = .75,--the time in seconds the full animation takes to play
					sounds = {nil, "rem870_rackslide"},--sounds to be played at each frame
					frames = {"hipidle.png", "load.png"},--images to be shown at each frame
					zoomframes = {"aimidle.png", "loadzoom.png"}--images to be shows when aiming
				},
				reload = {--the animation for loading bullets/magazines
					length = 1.15,
					speed = .75,
					loopstart = 2,--for repetitive loading animations: which frame to loop back to when it reaches loopend If this is not defined then gun will load fully after this animation.
					loopend = 5,--for repetitive loading animations: which frame after which to go to loopstart
					sounds = {nil, nil, "rem870_loadshell"},
					frames = {"reload1.png", "reload2.png", "reload2.png", "reload3.png", "reload3.png", "reload4.png"}
				},
				unload = {--the animation for unloading bullets/magazines
					length = .5,
					sounds = {nil, "rem870_rackslide"},
					frames = {"hipidle.png", "load.png"},
				},
			},
		})
		
	Creating image assets for your guns:
		It's fairly straightforward to make image assests for your gun.
		The method I used was importing models from magicavox to Blender and doing simple image renders.
		The exact process will differ depending on what software you are using.
		Unless you are sure of what you're doing, it's recommended to make your textures with these specifications:
			320x180 for low res (default, good for performance and dosn't take much time to send clients on a server)
			1920x1080 for high res (only recommended for higher end PCs, release as seperate texture pack, users can use "/spritegunscale .2" to fix the scaling issue.
			camera field of view for renders should be 120 degrees or 9.2mm
		If you use different size textures for your guns you will probably want to tweak the "scale" property in the gun registration until you get the desired outcome.
		For low resolution images may be blurry and require manual changes in an image editor, especially for aimidle. (It is hard to aim when your sights are blurry.)
		For continuity, you may want to use the same arms in your guns as were using in the ones already provided in the mod.
		Technically there is nothing stopping you from using images taken with a real camera, nor from creating the images entirely from an image editor, but it's just probably not as easy.
		
	If you create more guns I would love to see them! I will happily link them for people to find.
	I might allow custom fire logic in the future. (eg: to allow non-raycast projectiles) but for now I don't have that exposed in the api.
	If you have any feature requests or found some bugs please let me know.