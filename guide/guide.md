# RETRO HUD

This is a do it your self guide, so feel free to customize it. 

## Requirements 

- [Parrot OS Home](https://www.parrotsec.org/download/) with KDE Plasma
	
- At least 4GB

## Conky HUD

Conky is a small system monitor that displays a window with the user, a clock, CPU and RAM graphs on your desktop.
It has no settings screen: the config file is the whole interface. It is not part of KDE, so it needs to be installed.

paste this into your terminal:

	sudo apt install conky-all

After installation you will need a workspace where you will store your files. 
Create in your home directory (or folder) a conky folder:

	cd ~

	mkdir conky

	touch conky/retro-hud.conf

The .conf file is where the magic happens. There is where you will write your code that is going to be displayed. 

### Structure

A conky config is written in Lua and has 
two parts:

conky.config = { ... } is a table of settings: where the window sits, how big it is, how it behaves.
conky.text = [[ ... ]] is the content. ${...} placeholders are replaced with live data (${cpu} becomes your CPU percentage).

Add settings one at a time and restart conky after each step so you can see what each one does.

### Content of .conf:

	conky.config = {
    	alignment = 'top_middle',
    	gap_y = 0,
    	gap_x = 0,
    	border_inner_margin = 10,
    	border_width = 0,
    	border_outer_margin = 0,
	    own_window = true,
    	own_window_type = 'normal',
    	own_window_class = 'conky-retro-hud',
    	own_window_transparent = false,
    	own_window_argb_visual = true,
    	own_window_argb_value = 190,
    	own_window_hints = 'undecorated,below,sticky,skip_taskbar,skip_pager',
    	update_interval = 2,
    	total_run_times = 0,
    	double_buffer = true,
    	use_xft = true,
    	font = 'Monospace:size=11',
    	default_color = '00FF00',
    	color1 = '00FF00',
    	color2 = '2F6FED',
    	color3 = 'FF3B3B',
    	color4 = '00FFFF',
    	draw_graph_borders = false,
    	minimum_width = 1280,
    	maximum_width = 1280,
	}

	conky.text = [[
    	${color1}${goto 8}⌜${goto 1272}⌝${color}
		${voffset 4}${goto 20}${font Monospace:size=20}${color1}» USER:${color} ${color4}${exec whoami}${color}${font Monospace:size=11}${alignr 8}${color4}${time %H:%M:%S}${color} ${color1}● ${color}${color4}${time %A, %d %B}${color}
    	${voffset 6}${goto 20}${color1}››› SYSTEM ONLINE ‹‹‹${color}
    	${voffset 4}${goto 8}${color1}────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────${color}
    	${voffset 8}${goto 500}${cpugraph cpu0 30,100 00FF00 00FF00}${goto 650}${memgraph 30,100 00FF00 00FF00}
    	${voffset 2}${goto 530}${color4}______________${color}${goto 650}${color4}______________${color}
    	${voffset 4}${goto 526}${color1}CPU${color} ${cpu}%${goto 637}${color1}RAM${color} ${mem} / ${memmax}
    	${voffset 4}${color1}${goto 8}⌞${goto 1272}⌟${color}

	]]


### IMPORTANT

All the pixel numbers are tuned for a 1280×800 screen. On another resolution, change the width and the goto values.
The row of ─ characters is a hand-drawn rule: add or remove characters until it ends at the right corner.

### Settings 

| Setting | What it does |
|---|---|
| `alignment = 'top_middle'` | Anchors the window to the top of the screen |
| `gap_x, gap_y = 0` | No offset from the screen edge |
| `own_window = true` | Conky draws its own window instead of painting on the wallpaper |
| `own_window_type = 'normal'` | See "Lessons learned": `'dock'` stays on top on KDE |
| `own_window_hints = '...below...'` | No title bar, stays behind other windows, on every desktop, hidden from the taskbar |
| `own_window_argb_visual, own_window_argb_value = 190` | Real transparency; 190 out of 255 is about 75% solid |
| `update_interval = 2` | Refresh every 2 seconds (gentle on old hardware) |
| `double_buffer = true` | Draws off-screen first, so there is no flicker |
| `use_xft, font` | Modern anti-aliased fonts; a monospace font keeps columns aligned |
| `default_color, color1..color4` | Named colors. Conky wants hex without the `#` |
| `minimum_width, maximum_width = 1280` | Full width of a 1280×800 screen |
| `border_inner_margin, border_width, border_outer_margin` | Padding inside the window. Set them explicitly: the defaults are not guaranteed to be 0 |
| `draw_graph_borders = false` | No box around the graphs |


#### The layout


| Placeholder | What it does |
|---|---|
| `${color1} ... ${color}` | Switch to a named color, then reset to the default |
| `${goto N}` | Jump to exactly N pixels from the left |
| `${alignr N}` | Right-align the rest of the line, N pixels from the right edge |
| `${voffset N}` | Push the next line down by N pixels |
| `${font Name:size=N} ... ${font}` | Change the font temporarily; a bare `${font}` resets it |
| `${exec whoami}` | Run any shell command and insert its output |
| `${time %H:%M:%S}` | Date and time, using the same `%` codes as the `date` command |
| `${cpugraph cpu0 30,100 C1 C2}` | Live CPU graph, height and width, then two gradient colors (same color twice = one flat color) |
| `${memgraph 30,100 C1 C2}` | The same for RAM |


The symbols ⌜, ⌝, ⌞, ⌟, ›, ‹, ● ─ are plain Unicode characters, so they need no special syntax.

### Run it 

	conky -c conky/retro-hud.conf &

conky doesn't reload by itself, so after each edit:

	killall conky
	conky -c conky/retro-hud.conf &

## Optional: start it at login 

Optional: start it at login

1. Copy the config to the place conky usually reads from, then create an autostart entry. Replace YOURNAME with your username.

		mkdir -p $HOME/.config/conky $HOME/.config/autostart
		cp conky/retro-hud.conf $HOME/.config/conky/

2. Then reate $HOME/.config/autostart/retro-hud.desktop:

		[Desktop Entry]
		Type=Application
		Name=Retro Future HUD
		Exec=conky -p 4 -c /home/YOURNAME/.config/conky/retro-hud.conf
		Hidden=false

-p 4 makes conky wait 4 seconds so the desktop is ready before the HUD appears. You can also add it through System Settings → Autostart.

### Lessons learned

- own_window_type = 'dock' stays on top on KDE. KDE treats dock windows like its own panel, so the below hint is ignored.
Use 'normal' with the hints.

- ${hr} is not symmetric. It starts at your left margin but runs to the very edge on the right. Draw the rule yourself out of ─ characters instead.

- Reset the font. If you change a font with ${font ...:size=20} and forget the bare ${font}, everything will overlap.


## Last words

I was really excited about this project and I am really proud on how this turned out. It's a very simple project. Due to lack of time I couldn't finish the project fully. In the future when I feel bored I might pick up on it and add some additional features. 

