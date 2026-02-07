# Rocket TVC Sim (PC + ESP32)

Weekend project to see how far I can push a quick-and-dirty thrust vectoring loop: PC runs a 1 kHz 2D rocket sim, the ESP32 pretends to be the flight computer and spits back a tilt command. No enterprise diagrams, just the pieces that matter.

## What you get
- PC side: physics step + serial link at 921600 baud.
- ESP32 side: proportional controller that nudges the rocket from -5 deg to +5 deg over ~30 seconds.
- Metrics are printed so you can watch the loop behave (or fall apart).

## Fast setup
1. Plug in an ESP32 dev board.
2. Flash the firmware in `src/main.cpp` (PlatformIO handles it). Quick CLI: `pio run --target upload`.
3. Build the PC sim on Windows with `scripts\build_pc.bat` (uses `cl.exe`).
4. Run `rocket_sim.exe`; it should auto-find the board and start chatting.

## How the link talks
- PC sends: `I,seq,velocity,angle` once per physics tick.
- ESP32 replies: `A,tilt`.
- Baud: 921600. If your cable/port flakes out, drop it in `pc/src/rocket_sim.cpp` and `platformio.ini` to match.

## Knobs to turn
- `CONTROL_GAIN`, `START_ANGLE`, `END_ANGLE`, `TRANSITION_TIME` live in `src/main.cpp` (ESP32).
- Thrust, gravity, and timestep live in `pc/src/rocket_sim.cpp`.
- If auto-detect misses your COM port, hardcode it near the top of `rocket_sim.cpp`.

## Quick notes
- Tested on random ESP32 dev kits with USB serial; nothing fancy required.
- If prints stutter, close other serial monitors and try a lower baud.
- The physics is intentionally lightweight so timing is the thing we measure, not frame rate.
