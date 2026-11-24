keyd Macro Builder
==================

This folder contains a small Python generator for keyd macros. Edit
keyd_macro_builder.py, then run it to generate keyd-typing.conf. Do not edit
keyd-typing.conf by hand: it is overwritten every time the builder runs.

Requirements
------------

* Linux with keyd installed and managed by systemd.
* Python 3.10 or newer (the script uses modern type-union syntax).
* sudo access only when installing the configuration.

Create or change a macro
------------------------

In the AUTOMATIONS dictionary in keyd_macro_builder.py, each entry maps a
trigger key to an ordered list of steps:

    "kp1": [Text("Hello, world!"), Delay(250), Key("enter")],

The example above makes numpad 1 type "Hello, world!", wait 250 ms, and press
Enter. Replace the empty Text("") and Key("") placeholders in the supplied
numpad entries before using them; empty macros are not useful configurations.

Available steps
---------------

Text("text")
    Types plain text one character at a time. Spaces, newlines, and tabs are
    converted to keyd's space, enter, and tab tokens.

Delay(milliseconds)
    Pauses between actions. Delays must be whole numbers from 1 through 5000.

Key("key-or-chord")
    Sends a key or keyd key combination. Examples:

        Key("enter")
        Key("C-c")       # Ctrl+C
        Key("C-M-r")     # Ctrl+Windows/Super+R
        Key("a+b")       # press ordinary keys together

Modifier letters are C (Ctrl), M (Windows/Meta/Super), A (Alt), S (Shift), and
G (AltGr). Use lowercase for ordinary key names. Common names include enter,
tab, esc, space, left, right, up, down, f1 through f12, and a through z.
Use leftmeta or rightmeta to press only the Windows/Super key. Numpad triggers
include kp0 through kp9, kpplus, kpminus, kpasterisk, kpslash, kpenter, and
kpdot.

Media and hardware-control key names
------------------------------------

The following keyd key names are useful for media buttons and other special
hardware controls. Use them in Key(...).

This is not a complete list.
    mute             volumedown       volumeup         micmute
    playpause        nextsong         previoussong     rewind
    fastforward      playcd           pausecd          stopcd
    ejectcd          ejectclosecd     closecd          power
    sleep            suspend          wakeup           brightnessdown
    brightnessup     display          switchvideomode  kbdillumtoggle
    kbdillumdown     kbdillumup       battery          bluetooth
    media            sound            calc             back
    forward          open             close            new
    edit             find             copy             cut
    paste            undo             redo             print

Some names are only meaningful when the operating system or active application
handles the corresponding hardware/media event. To see the authoritative list
for the installed keyd version, run: keyd list-keys

Generate and install
--------------------

From this directory, generate a local preview:

    python3 keyd_macro_builder.py

Review the generated keyd-typing.conf. When it looks right, generate it again,
copy it to /etc/keyd/default.conf, enable/start keyd, and reload its macros:

    python3 keyd_macro_builder.py --install

The install command asks for your sudo password. It replaces
/etc/keyd/default.conf, so move any unrelated keyd mappings into this project
before installing or use a different config workflow.

How it works
------------

The generated configuration applies to all keyboards ([ids] contains *). Its
[main] section binds each configured trigger to keyd's macro(...) syntax.
Running with --install places that generated file at keyd's default config
location and reloads the keyd service.

Troubleshooting
---------------

* Run the script without --install first; it catches invalid delay values and
  lets you inspect the result before modifying the system configuration.
* If keyd rejects a macro, verify the key names and chord syntax against your
  installed keyd version's documentation.
* To remove these macros, replace /etc/keyd/default.conf with your preferred
  configuration and run: sudo keyd reload
