# i3blocks-arrows

## Example

```ini
command=~/.config/i3blocks/scripts/$BLOCK_NAME
full_text=
align=center
color=#ffffff
background=#073642
separator=false
separator_block_width=0
markup=pango 

[brightness]
icon=
background=#ffffff
color=#000000
format=%s%%
markup=pango
interval=5

[disk]
background=#657b83
icon= /home
interval=30
markup=pango

[volume]
instance=Master
background=#cb4b16
icon=
interval=once
signal=10
format=%s%%
markup=pango

[time]
background=#dc322f
icon=
interval=5
```

Here's what this config produces:

![Bar](https://i.imgur.com/kmOl1ts.png)

## Installation

```bash
git clone https://github.com/tiger31/i3blocks-arrows
cd i3blocks-arrows
ln -s $PWD/i3* /usr/local/bin
```

If i won't be lazy, AUR package coming soon

## Configuratiog

i3-arrows is a script that translates default i3blocks config into one another, needed to make it looks like arrows.
If you already have a config from i3blocks, you're in half way.

### i3bar

i3arows requires two argument:

**_-c_**
  Path to your actual i3blocks config
  
**_-o_**
  Path to i3-arrows generated config

```
bar {
  status_command i3arrows -c ~/.config/i3blocks/config -o /tmp/i3arrows.conf
}
```

### blocks

i3arrows require just one must-have parameter, defined in a global part of config: ```backgound```.
This parameter shows an actual color of i3bar, defined in i3 config. Without this property, i3-arrows won't work properly at all.

Also clearing some mechanics: i3blocks takes output from ```command``` property, but i3arrows not.
When generating config, i3arrows rewrites all ```command``` properties into ```script``` property and adds just one ```command``` property on top of the config
_Note:_ If you have ```scpirt``` property in your i3blcoks config, it will be removed before generating i3arrows config!

Now let's take a look at new properies we have to describe our arrows:

| Property  | Value | Description |
| --------- |:-----:| -----------:|
| background | ```string``` (hex-color) | Defines background of an arrow |
| edge | ```array```  | ```edge``` is an array of "points" used to chande color/icon depends of script output. For example you can change battary icon depends of how much charge it has. Can be an array of int's or string's (explained further) |
| mode | compare\|* | Defines how exactly icons/colors depends on edge. If set to "compare", output from ```script``` will be compared with ```edge``` by "<=". If set to anything but "compare", values compared strictly with "==" |
| color | ```array(string)``` (hex-color) | Array of font colors, arrow can have |
| icon | ```array(string)```  | Array of string/char (I prefer using font-awesome icons) arrow can have. It's like ```label``` property in i3blocks |
| format | ```string``` | Format of output string, used by ```printf```. For example "_%s%%_" to add "%" sign after number |#

_Note: arrays format different from .ini format, but similar to bash one: color=(#ff0000 #00ff00 #0000ff)_

### How exactly edge works:

Let's imagin you have some script that outputs random number from 1 to 100 and you want i3-arrows to set icons depends of value it returs.
For example if value <= 15 you want icon to be "1", if 15 < value <= 80, then "2", else "3".
So here's properties you need:

```
edge=(15 80)
icon=(1 2 3)
```
If none of values edge values mathes, it uses last one array element.

Complete example, my battary config: 

Script:
```bash
#!/bin/sh
now=$(cat /sys/class/power_supply/BAT0/charge_now);
full=$(cat /sys/class/power_supply/BAT0/charge_full);
echo "$((now * 100 / full))"
```

Config:
```ini
[battary]
edge=(10 25 50 75)
icon=(    )
color=(#dc322f #b58900 #ffffff)
format=%s%%
mode=compare
background=#2aa198
markup=pango
interval=30
```

Here we can see, that if battary-level will be over 25, then font color alway be blue. Same mechanic with icons

### My curent i3bar look, using i3-arrows:

![Bar](https://i.imgur.com/GV3FtW3.png)
