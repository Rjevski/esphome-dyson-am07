# ESPHome for Dyson AM07

This repo contains a work-in-progress configuration for ESPHome to connect a Dyson AM07 fan to Home Assistant. This config interfaces with the fan by emulating the official Dyson IR remote.

# Installation

## External

Connect an IR LED to your ESP, tweak the pin settings in `remote_transmitter` (you will probably need to set carrier duty cycle to 50% and remove the `inverted`).

## Internal

Upgrade the fan's built-in power supply; it has a ~5V rail to power its internal microcontroller, however this is not enough to power the ESP and as a result the rail sags and the built-in microcontroller constantly resets.

Assuming the power problem is sorted, you need to connect the data line from the IR receiver to the ESP. This way it can not only inject IR codes but also monitor incoming IR codes from the official remote (or any other IR remote for that matter).

# Docs

## Note on statefulness

Note that the fan is stateful and upon power loss it will return to its previous state - this is a problem as the ESPHome intentionally does not keep state (to avoid wearing down the flash); therefore on boot the ESPHome will automatically transmit key presses to get the fan to a known (off) state.

If you don't mind wearing down the flash it should be possible to persist the state in ESPHome and then the fan & ESP should remain in sync.

## Power button statefulness

You will notice some complexity in `transmit_power_button`. This is because Dyson official remotes alternate between 3 IR codes between each power button press and the fan syncs with that upon receiving the first code. Therefore, we need to replicate that behavior. Unfortunately this does make it impossible to use the power button on an official remote in "passthrough" mode as they're unlikely to be in sync.
