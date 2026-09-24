![hvac-automation-node](https://raw.githubusercontent.com/mytechnotalent/hvac-automation-node/main/hvac-automation-node.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# OPERATION IRON LUNG

### HVAC Automation Node
#### Act IV of OPERATION COLD IRON

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only. 

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

> Hello again, friend.
>
> Act I was the lie. Act II was the door. Act III was the payload. This is the
> payload that refuses to die.
>
> WHITEOUT neutralized the valve implant. The crew erased the payload sector,
> patched the re-install check, reflashed the controller, and watched it come
> back. Not on the same board. On the next one. The HVAC automation node that
> feeds the clean room where NorthPharma compounds the cold medicine, and the
> implant had already learned the lesson of the last act.
>
> It hides a copy of itself in a reserved flash sector. On every boot it reads
> that sector, finds its own marker, and re-installs the beacon and the bomb.
> You can reflash the firmware all day; the payload returns. It also wears a
> rootkit. It masks its own beacon from the BMS display and the maintenance
> log, so the operator reads a clean node while the radio is still talking.
>
> NorthPharma is the Ministry's front. FROSTLINE wrote the persistence.
>
> Do not trust the clean screen. Read the reserved sector. Then remove it for
> good.
>
> The baffle is holding. That is exactly why you should be afraid.

<br>

## THE SYSTEM

NorthPharma does not only move cold medicine. It moves the air that keeps the
medicine cold: the clean room, the reagent store, the chilled corridor where a
few degrees decide whether a batch is medicine or waste. The HVAC automation
node is the hand on that air. It reads the room, it compares the room to a
setpoint, and it drives a baffle.

An HVAC node is a simple machine. A climate sensor reports the room, the BMS
gateway authorizes a setpoint, the controller decides, an actuator moves the
baffle, and an annunciator says whether the space is nominal. The failure that
matters is not a wrong number on a screen. It is a baffle that closes when no
one asked, or a setpoint that drifts while the display insists the space is
fine.

The node in this repository is that hand. On a breadboard it is a toy: a Pico
2, a servo that acts as the baffle, a DHT11 that stands in for the room climate
sensor, an infrared remote that is the local override, a button that is the
manual override, a 1602 LCD that is the BMS readout, three lamps, and a radio.

Nothing about it looks broken. That is the horror of Act IV. The code compiles,
the tests pass, the annunciator is green, and there is an implant inside it that
survives the exact procedure that was supposed to remove it.

<br>

## THE STAKES

Act I was a lie about temperature. Act II was a lie about people. Act III was a
lie about machinery. Act IV is a lie about remediation, and it is the first lie
that tells you it is gone.

The node is weaponized, not buggy. A hidden implant beacons over LoRa on a
timer, and a logic bomb re-arms on every boot and closes the baffle on its own
schedule. Close a baffle on a live clean room and the room runs hot; the process
does not care that the firmware passed its tests.

And here is the part that keeps the responders awake. The setpoint command path
is already authenticated. The cryptography is real and it is correct. The
implant does not break the cipher. It does something worse: it re-installs
itself from a reserved flash sector, and it hides from the operator while it
does it. You cannot patch a protocol if the attacker is already inside the
protocol, and you cannot uninstall a payload with the same reflash the payload
already survived.

That is not a clean node. That is a clean node with a tenant.

<br>

## WHITEOUT

WHITEOUT is a resistance that does not exist on paper. It does not hold ground
and it does not hold press conferences. It reads firmware. When NIGHTINGALE
copied the shipping image and went quiet, the crew kept pulling the thread. The
gate led to the pipeline. The pipeline led to the plant, and the plant led to
the air.

NIGHTINGALE is still the thread. Her last verified copy came off the valve
controller, and it was clean. The thing that came after it was not. Somewhere
between the build server and the rack, someone signed an HVAC image that carries
a payload, and that image is holding a clean room right now.

WHITEOUT's job in Act IV is not to break in. It is to prove the machine is
already broken, in writing, with a debugger and a disassembler, and then to
remove the thing that does not belong and make the removal stick.

<br>

## THE MACHINE

The firmware in this repository is the node's firmware. On a breadboard it is a
toy: a Pico 2, an SG90 servo that is the baffle, a DHT11 that is the room
climate sensor, a VS1838B infrared eye that takes a local override remote, a
1602 LCD BMS readout over I2C, red/yellow/green annunciator lamps, a manual
override button, and an RYLR998 LoRa link to a BMS gateway.

Two things are open, and one thing is not what it seems. **The optical surface**
takes an override command from any NEC remote, and it is not authenticated. **The
radio** carries the sealed setpoint path, and it is authenticated correctly. The
part that is not what it seems is the **implant**: a module compiled only under a
build flag called `SANDBOX_ONLY`, invisible in the clean firmware, and present in
the test and CTF builds. It beacons, it re-installs from a reserved sector, it
hides from the display and the log, and it hides from the debugger.

The face of the thing is honest in the way that matters least. The lamps say
HVAC ALARM, OVERRIDE PENDING, and HVAC NOMINAL with total confidence, and the
LCD shows the setpoint, the link, and the beacon. None of it lies. A healthy
machine can still be a hostile one.

<br>

## THE JOB

You do not have to be a hero. You have to be thorough. The node is carrying a
passenger that no design review admitted to, and the passenger has a backup.
Find it, prove it, and take it out for good.

1. **Bring it up.** Build the clean firmware, wire the board, and confirm the
   node reads the room climate, takes a local override, reaches the gateway,
   and moves the baffle. Nothing looks broken because nothing is broken yet.
2. **Hunt the malware.** Build the `SANDBOX_ONLY` image and find the beacon, the
   logic bomb trigger, the rootkit masking branch, the anti-debug trap, and the
   reserved-sector persistence marker. A firmware reflash alone will not remove
   it.
3. **Defuse it.** Break the re-install on boot, expose the rootkit hiding, and
   step past the anti-debug with GDB so the implant cannot tell that a probe is
   attached. Then erase the reserved sector.
4. **Fix the machine.** Seal the setpoint command path so only an authorized
   gateway can move the baffle, make the local override ask for authorization
   instead of bypassing it, and make the node fail to a safe setpoint on a lost
   link.

This document is the manual for the job. Work it on a breadboard. When the
green lamp is lit and the log says the node is clean, remember what it is: not a
healthy machine. An occupied one.

Goodbye, friend.

<br>

## A NOTE ON THE ROADMAP

This project is Act IV of **OPERATION COLD IRON**. Act I was the sensor
([cold-chain-monitor](https://github.com/mytechnotalent/cold-chain-monitor)). Act
II was the door ([access-gate](https://github.com/mytechnotalent/access-gate)).
Act III was the valve
([pipeline-valve-controller](https://github.com/mytechnotalent/pipeline-valve-controller)).
Act IV is the air. All four are defended devices; the companion CTF repository
ships the compromised one. The investigation lives here:

- [OPERATION IRON LUNG CTF](https://github.com/mytechnotalent/CTF_hvac-automation-node)

The CTF is the red half, weaponized: six deep tasks, each with static analysis, a
dynamic proof under GDB, a hardware demonstration, and an in-place, same-size
patch. This repository is the defended device. The CTF repository is the breached
one. The full story lives at
[github.com/mytechnotalent/hvac-automation-node](https://github.com/mytechnotalent/hvac-automation-node).

---


<br>

## WHERE THIS FITS: OPERATION COLD IRON

This repository is **Act IV (IRON LUNG)** of the ten-act OPERATION COLD IRON
saga. The malware track began in Act III; here it becomes persistence. The full
spine is in [SAGA.md](SAGA.md).

- Previous act: Act III, IRON VEIN, the pipeline valve controller,
  [pipeline-valve-controller](https://github.com/mytechnotalent/pipeline-valve-controller)
- This act: Act IV, IRON LUNG, the HVAC automation node
- Next act: Act V, IRON WEB, industrial-tamper-system (forthcoming)
- Companion CTF:
  [CTF_hvac-automation-node](https://github.com/mytechnotalent/CTF_hvac-automation-node)


<br>

## THE MINISTRY

The Ministry runs the state: the surveillance, the cold chain, the gates, the
pipelines, the air. NorthPharma is one of its deniable industrial fronts, and
FROSTLINE is the contractor that does the work no Ministry letterhead will admit
to. FROSTLINE did not break into this controller; it built the implant, taught it
to survive a reflash, signed the image, and moved on. Against them is WHITEOUT,
and the engineer who copied the first image, NIGHTINGALE. This act is one node of
the Ministry's industrial edge. TELESCREEN, the surveillance backbone that
watches it, comes after the ten.

An adversarial, evidence-based audit of this act, including its honest
limitations, is in [NATION-STATE-REVIEW.md](NATION-STATE-REVIEW.md).


<br>

## How This Project Fits the Embedded Hacking Course

This repository is the Act IV capstone integration for the
[Embedded Hacking](https://github.com/mytechnotalent/Embedded-Hacking) course. It
reuses the entire Act I peripheral set so one breadboard serves the whole
foundation, and it adds the concepts the later acts build toward: a payload that
persists in a reserved flash sector, a rootkit that hides its own beacon from the
operator, re-install on boot, and the blue-half controls that contain them.

Each earlier module teaches one peripheral or language concept in isolation; this
project wires several of them into a single, tested product, and then teaches you
to look at that product as an adversary sees it.

| Embedded Hacking module | Concept you learn | Where it lives here |
| ----------------------- | ----------------- | ------------------- |
| Week 1: Introduction, Ethics, Scoping | Authorized lab work | Every lab is self-contained and authorized by design |
| Week 3: RP2350 Architecture and Firmware Analysis | Bare-metal targets, ELF/UF2, SWD | Pico SDK build, `build/*.uf2`, Debug Probe flash via OpenOCD |
| Weeks 4-6: Variables, Integers/Floats, Static | Data types, GPIO | `src/monitor.c` state machine, LED on GP25 |
| Week 7: Constants with 1602 LCD I2C | I2C bus, HD44780 commands | `src/display.c` |
| Week 9: Operators with DHT11 | Bit operations, edge timing | `src/sensor.c` room climate sensor |
| Week 11: Structures and Functions | Modular design | `include/*.h` and `src/*.c` module boundaries |
| This project adds | Reserved flash sectors, boot-time re-install, rootkit hiding, UART AT driver, LoRa setpoint path, anti-replay, authenticated state, fail-safe policy, malware analysis, anti-debug evasion, strict testing | `src/implant.c`, `src/baffle.c`, `src/control.c`, `src/hvac_auth.c`, `src/radio.c`, `scripts/gateway.py`, `scripts/spoof.py`, `test/` |

If you have not worked through Weeks 7 and 9 yet, do those first: this project
assumes you are comfortable with I2C wiring and one-wire edge timing.

<br>

## Learning Objectives

By the end of this chapter and its labs you will be able to:

- Explain why a control system must protect integrity, availability, and state
  together, and why a firmware reflash is not remediation when a payload has a
  copy of itself outside the firmware.
- Wire and drive a 1602 LCD through a PCF8574 I2C backpack and render a BMS
  status, setpoint, link, and beacon readout.
- Decode a VS1838B infrared receiver as a local override remote for OPEN, CLOSE,
  and CLEAR commands, and explain why an unauthenticated optical surface is still
  an attack surface and must not silently bypass authorization.
- Drive an SG90 baffle actuator with 50 Hz PWM and explain why a 1000uF bulk
  capacitor is not optional.
- Read a DHT11 room climate sensor and classify the room against a safe band
  before the baffle is allowed to move.
- Design a sealed setpoint command path over a sub-GHz LoRa link using a
  fixed-size envelope, a monotonic anti-replay window, and a keyed state tag.
- Analyze a persistent implant: locate the reserved-sector marker, explain the
  re-install on boot, find the rootkit masking branch, and read the anti-debug
  trap.
- Explain why erasing the reserved sector and patching the re-install check are
  both required, and why a firmware reflash alone is not enough.
- Defeat an anti-debug check under GDB by understanding the CoreDebug `DHCSR`
  register at `0xE000EDF0`.
- Apply blue-half controls: sealed and authorized commands, an override that
  requests authorization, fail-safe behavior, and containment for the implant.
- Derive a key with Argon2id, seal every frame with XChaCha20-Poly1305, and read
  and run a native host test suite with hardware mocks and line coverage.

<br>

## Prerequisites

- The [Embedded Hacking](https://github.com/mytechnotalent/Embedded-Hacking)
  breadboard (`EHP2_bb.png`) and parts list.
- Acts I to III are helpful but not required. See
  [cold-chain-monitor](https://github.com/mytechnotalent/cold-chain-monitor),
  [access-gate](https://github.com/mytechnotalent/access-gate), and
  [pipeline-valve-controller](https://github.com/mytechnotalent/pipeline-valve-controller)
  for the sensor, the door, and the valve. The pin map is identical, so one
  breadboard serves all four.
- Comfort with C, the Linux/macOS shell, and basic electronics.
- A Pico 2, a Debug Probe (recommended, and required for the malware lab), a 1602
  LCD with PCF8574 backpack, a DHT11, the full Embedded Hacking kit (3 LEDs, 3
  resistors, a push button, an SG90 servo, a 1000uF capacitor, and a VS1838B
  infrared receiver plus NEC remote), two RYLR998 modules, and one USB-to-TTL
  serial adapter.
- Toolchain: Pico SDK 2.2.0+, `arm-none-eabi-gcc`, CMake, Ninja, Python 3, GDB
  (`arm-none-eabi-gdb`) for Lab 3, and (optionally) `typst` to rebuild the paper.

<br>

## Table of Contents

1. [Background](#background)
2. [System Architecture](#system-architecture)
3. [The Wire Protocol](#the-wire-protocol)
4. [The Cryptographic Envelope](#the-cryptographic-envelope)
5. [The FROSTLINE Implant](#the-frostline-implant)
6. [Hardware You Need](#hardware-you-need)
7. [Wiring the Node](#wiring-the-node)
8. [Build and Flash](#build-and-flash)
9. [Lab 1: Bring-Up and Verify](#lab-1-bring-up-and-verify)
10. [Lab 2: Inspect the Wire Protocol](#lab-2-inspect-the-wire-protocol)
11. [Lab 3: The Malware Track](#lab-3-the-malware-track)
12. [Lab 4: The Fix Track](#lab-4-the-fix-track)
13. [Troubleshooting](#troubleshooting)
14. [Testing Philosophy and Coverage](#testing-philosophy-and-coverage)
15. [Generating Packet Artifacts](#generating-packet-artifacts)
16. [Code Standards](#code-standards)
17. [Project Layout](#project-layout)
18. [Glossary](#glossary)
19. [Further Reading](#further-reading)
20. [License](#license)

<br>

## Background

### Why HVAC automation control

An HVAC plant is a control loop with a room in it. A climate sensor reports the
room, a BMS gateway authorizes a setpoint, a controller decides, an actuator
moves a baffle, and a gateway logs what happened. The baffle is where the
decision becomes physical. Everything interesting in building control happens in
that transition from a number to a motion.

Three properties have to hold at once, and they are not the same property:

- **Integrity.** The setpoint that reaches the baffle is the setpoint the
  gateway authorized. Not a replay, not a forgery, not a stray value.
- **Availability.** The baffle is there when the room needs it. A jammed radio
  or a crashed controller can be as dangerous as a hostile one.
- **State.** The controller knows whether it is open, closed, moving, or
  faulted, and it does not trust a stale or tampered verdict.

Act IV adds a fifth property that no protocol can provide from the outside:
**persistence resistance**. Removing the attacker's code once is not remediation
if the attacker kept a copy that the removal did not reach.

### Why integrity plus availability plus state matter

The classic naive controller collapses the three. It accepts any setpoint on the
radio, it has no anti-replay window, and it keeps its verdict in plain SRAM. Act
II showed what that costs a door. Act III showed the industrial version. Act IV
adds the recovery failure:

- **Integrity without exclusivity.** The sealed setpoint path in this build is
  correct. XChaCha20-Poly1305 authenticates every frame, the sequence window
  rejects a replay, and the state tag detects a tampered verdict. None of that
  stops an implant that calls the actuator directly.
- **Availability as the attack goal.** A logic bomb does not need to steal
  anything. It needs to close a baffle at the wrong moment. Denial is the whole
  payload.
- **State as the last line of defense.** A keyed tag over the authorization
  record means a debugger that rewrites the record is caught before the actuator
  moves. It is the same lesson Act II taught, carried into the air handler.
- **Persistence as the recovery trap.** A payload that writes itself into a
  reserved sector and re-installs on boot turns the natural response into a
  loop. The fix is not only a patch; it is a patch plus the erasure of the copy.

The fix track in Lab 4 seals the command path, makes the local override request
authorization, and makes the node fail to a safe setpoint. The malware track in
Lab 3 breaks the re-install, exposes the rootkit, and removes the passenger that
was never in the design.

### Why ChaCha20 over AES on the RP2350

The RP2350 has no hardware AES engine; its accelerated crypto block covers
SHA-256, not AES. A software AES implementation on this part is therefore both
slower and riskier, because table-driven AES performs data-dependent memory
accesses that create a cache-timing side channel. ChaCha20 is built only from
addition, rotation, and XOR, with no data-dependent table lookups, so it is fast
in portable C and has no comparable cache-timing surface. XChaCha20-Poly1305 is
thus both the modern choice and the pragmatic one for this silicon. The full
rationale, including the extended-nonce benefit, appears in
[The Cryptographic Envelope](#the-cryptographic-envelope).

### The two on-wire problems this project solves

1. **Payloads that contain commas.** The sealed body is carried as lowercase hex,
   but the `+RCV` framing still separates fields with commas. A naive receiver
   that splits the line on the first comma corrupts the frame. The correct
   discipline is the **declared-length** rule: slice exactly `L` characters after
   the second comma and require the next character to be a comma.
2. **Telling a real command from a forged or replayed one.** The controller
   records the sender address exactly as the radio reports it, and it trusts the
   bytes that arrive. The sealed envelope plus the stateful window are what close
   that gap.

### Inter-Integrated Circuit (I2C)

I2C is a two-wire bus: **SDA** (data) and **SCL** (clock), each pulled up to the
supply rail. A controller (the Pico) addresses a target by its 7-bit address and
writes or reads bytes. The 1602 LCD backpack carries a **PCF8574** I/O expander
at address `0x27`; the firmware bit-bangs the HD44780 nibble protocol over that
expander. Pull-ups are mandatory: the firmware enables the internal ones and the
backpack usually adds its own.

### The DHT11 one-wire protocol

The DHT11 is a low-cost digital temperature and humidity sensor. In Act IV it is
the **room climate sensor**: the node classifies the room against a safe band
before it will move the baffle. It speaks a custom single-wire protocol:

1. The host pulls the line low for at least 18 ms (the **start pulse**), then
   releases it and enables its pull-up.
2. The sensor answers with an 80 us low, then an 80 us high handshake.
3. The sensor sends **40 bits**. Each bit begins with a 50 us low, then a high
   pulse whose width encodes the value: about 26-28 us for a `0`, about 70 us
   for a `1`.
4. Five bytes follow: humidity integer, humidity decimal, temperature integer,
   temperature decimal, and a checksum equal to the low byte of their sum.

Reading it means timing edges on the order of tens of microseconds, so the
firmware uses an 18 ms host pulse, a 50 us bit-classification threshold, and a
240 us per-edge timeout so a dead or unplugged sensor fails fast instead of
hanging the loop. A reading that fails its checksum is never safe, and a valid
reading outside **0.0 C to 40.0 C** (the tenths band `0` to `400`) is out of
band. Either way, the room is not nominal.

### Universal Asynchronous Receiver/Transmitter (UART) and AT commands

The RYLR998 is driven over a UART at 115200 baud using CRLF-terminated ASCII
commands. The firmware writes `AT+SEND=...` and drains inbound `+RCV=...` lines.
Because the radio is a separate processor, its configuration (address, network
identifier, band) persists until changed; the controller and the gateway each
provision their own radio at start-up so they agree before any command traffic
flows.

### Cyclic Redundancy Check (CRC)

`src/crc.c` implements CRC-16/CCITT-FALSE (`poly = 0x1021`, `init = 0xFFFF`,
check value `0x29B1` for `"123456789"`). It is provided as a reusable integrity
diagnostic and exercised by the test suite. It is **not** part of the LoRa frame
in this project; the lesson is the *absence* of authentication, not the absence
of a checksum.

<br>

## System Architecture

There are four roles:

| Role | Runs on | Job |
| ---- | ------- | --- |
| **HVAC node** | Pico 2 firmware | Decodes the infrared override remote, verifies sealed gateway SETPOINT commands, annunciates OVERRIDE PENDING, checks the room climate sensor, drives the baffle, enforces the manual override, and renders the BMS readout |
| **BMS gateway** | laptop + USB-TTL radio | Authenticates every request, logs it to `hvac_log.csv`, decides authorization, and answers with a sealed SETPOINT command carrying a sequence and a state tag (`scripts/gateway.py`) |
| **Edge simulator** | laptop + USB-TTL radio | Pretends to be a node and sends sealed setpoint requests (`scripts/sim_edge.py`) |
| **Attacker** | laptop + USB-TTL radio | Impersonates the gateway, forges a command, or replays a captured command (`scripts/spoof.py`) |

### Data flow

```text
+----------------------+                              +----------------------+
|  Pico 2 HVAC node    |        LoRa (sub-GHz)        |   BMS gateway        |
|  IR remote  -> GP5   |  AT+SEND=0001,<len>,<hex>    |  USB-TTL radio       |
|  DHT11      -> GP4   |----------------------------->|  scripts/gateway.py  |
|  Servo      -> GP14  |<-----------------------------|  hvac_log.csv        |
|  LCD     -> GP2/GP3  |  AT+SEND=<node>,<len>,<hex>  |  sealed command      |
+----------------------+                              +----------------------+

+----------------------+                              +----------------------+
|   Attacker laptop    |  forged or replayed command  |   (same HVAC node)   |
|   scripts/spoof.py   |----------------------------->|   rejects at the tag |
|  claims the gateway  |                              |   tag or seq window  |
+----------------------+                              +----------------------+
```

### Firmware module map

| File | Responsibility |
| ---- | -------------- |
| `src/main.c` | Entry point: `stdio_init_all`, `monitor_init`, tick loop |
| `src/monitor.c` | State machine: I2C bus scan, override remote, gateway command, baffle motion, manual override, room climate sensor, BMS render |
| `src/implant.c` | SANDBOX_ONLY FROSTLINE implant: covert beacon, logic bomb, rootkit hiding, CoreDebug anti-debug, reserved-sector persistence and re-install |
| `src/baffle.c` | Baffle state machine: bounded travel, open/closed/fault/moving, fail safe |
| `src/control.c` | Sealed SETPOINT command path: open, authorize, guarded setpoint command |
| `src/hvac_auth.c` | Authorization record, monotonic anti-replay window, authenticated state tag |
| `src/sensor.c` | DHT11 one-wire sampling and room-climate-band classifier |
| `src/display.c` | HD44780 driver over the PCF8574 backpack and BMS status rendering |
| `src/radio.c` | RYLR998 provisioning, `AT+SEND` builder, `+RCV` parser, line pump |
| `src/status_led.c` | Red/yellow/green HVAC ALARM / OVERRIDE PENDING / HVAC NOMINAL annunciator |
| `src/button.c` | Debounced manual override button around the internal pull-up |
| `src/servo.c` | 50 Hz PWM baffle actuator |
| `src/ir_remote.c` | VS1838B edge timing and NEC override remote decode |
| `src/chacha20.c` | ChaCha20 stream cipher and HChaCha20 subkey derivation |
| `src/poly1305.c` | Poly1305 one-time message authenticator |
| `src/crypto_aead.c` | XChaCha20-Poly1305 seal/open envelope |
| `src/blake2b.c` | BLAKE2b and the Argon2 variable-length hash H' |
| `src/argon2.c` | Argon2id core (BLAMKA, hybrid addressing) |
| `src/crypto_kdf.c` | Argon2id passphrase key derivation |
| `src/envelope.c` | Hex nonce/ciphertext/tag envelope codec |
| `src/crc.c` | CRC-16/CCITT-FALSE diagnostic |
| `include/hvac.h` | Pin map, bus, provisioning, implant addresses |
| `include/implant.h` | Implant beacon, arming magic, rootkit, anti-debug, persistence interface |
| `include/control.h`, `include/hvac_auth.h` | Sealed command and authorization interfaces |

<br>

## The Wire Protocol

### Request frame

The local override, or the edge simulator, seals a two-byte setpoint into an
authenticated envelope and sends it to the BMS gateway:

```text
AT+SEND=0001,<len>,<hex envelope>
```

The plaintext of a request is exactly two bytes: an `int16` setpoint in tenths of
a degree Celsius, little-endian.

### Command frame

The gateway answers an authenticated request with a sealed SETPOINT command. The
command plaintext is a 23-byte body:

```text
seq[4] (little-endian) || cmd[1] || setpoint[2] (little-endian) || state_tag[16]
```

- `seq` is the monotonic gateway sequence number.
- `cmd` is `HVAC_COMMAND_SETPOINT` (`0x01`). It is the only guarded command.
- `setpoint` is the authorized setpoint in tenths of a degree Celsius.
- `state_tag` is an XChaCha20-Poly1305 tag over the authorization record the
  command would produce, so the controller can verify that the verdict it is
  about to store is the one the gateway authorized.

The gateway sends it back to the claimed sender address:

```text
AT+SEND=<node>,<len>,<hex envelope>
```

The firmware enforces the guard in `control_parse`: the recovered command byte
must equal `HVAC_COMMAND_SETPOINT`, and the recovered setpoint must be inside the
provisioning band `HVAC_SETPOINT_MIN_TENTHS` (50 tenths, 5.0 C) to
`HVAC_SETPOINT_MAX_TENTHS` (350 tenths, 35.0 C). Anything else is rejected before
it can reach the actuator decision. This is the sealed replacement for the old
unauthenticated setpoint injection.

### Sealed envelope layout

Every payload on the wire is the lowercase hexadecimal encoding of:

```text
nonce[24] || ciphertext[L] || tag[16]
```

For a two-byte request body this is 24 + 2 + 16 = 42 bytes, or 84 hex
characters. For a 23-byte command body this is 24 + 23 + 16 = 63 bytes, or 126
hex characters. The declared length `L` in the `AT+SEND` and `+RCV` framing is
the length of the hex string, not of the underlying plaintext.

The maximum accepted plaintext is 48 bytes (`ENVELOPE_MAX_PLAINTEXT`), and the
maximum hex envelope buffer is `(24 + 48 + 16) * 2 + 1 = 177` bytes
(`ENVELOPE_MAX_HEX_LEN`), which fits the 256-byte radio command and receive
buffers with framing headroom.

### Declared-length slicing invariant

Given the substring `T` after the second comma:

```text
C = T[0 : L]   and   T[L] == ","
```

The receiver checks `T[L] == ","`, so a mismatch between the declared length and
the actual payload is a parse error rather than silent corruption. This is what
makes hex-bearing payloads safe to carry and is the same invariant Act I uses.

### BMS status readout

```text
ST:OPEN  L:UP
SP:20.0 B:--
```

Line 1 is the current baffle state (`CLOSED`, `OPEN`, `MOVING`, or `FAULT`) and
the gateway link (`UP` or `--`). Line 2 is the active setpoint and the beacon
status. In the clean build the beacon field is always `--`. In the
`SANDBOX_ONLY` build the beacon field is `--` while the rootkit is active and
`UP` otherwise, so a masked beacon and an absent beacon are visually identical
to the operator. Exactly one status LED is lit at a time to match: red for
`FAULT`, yellow while an override is pending or the baffle is moving, green while
the node is nominal.

### Radio provisioning

For the link to work, both radios must share the same **network identifier** and
each must have the address the other targets:

- Firmware sets its own radio: `AT+ADDRESS=7`, `AT+NETWORKID=18`.
- `gateway.py` sets the gateway radio: `AT+ADDRESS=1`, `AT+NETWORKID=18`.

Both radios must also be the **same band variant** (for example 915 MHz or
868 MHz); band and RF parameters are left at factory defaults, so use matching
modules.

### Timing

| Quantity | Value |
| -------- | ----- |
| Gateway link timeout (`HVAC_SETPOINT_WAIT_MS`) | 5000 ms |
| Baffle travel time (`BAFFLE_TRAVEL_MS`) | 1000 ms |
| Manual override debounce (`OVERRIDE_DEBOUNCE_US`) | 30000 us |
| DHT11 host start pulse | 18000 us |
| DHT11 bit threshold | 50 us |
| DHT11 per-edge timeout | 240 us |
| LCD I2C clock | 100000 Hz |
| Radio UART baud | 115200 |
| Room climate band | 0 to 400 tenths (0.0 C to 40.0 C) |
| SETPOINT band | 50 to 350 tenths (5.0 C to 35.0 C) |
| Safe setpoint | 200 tenths (20.0 C) |
| Baffle deadband | 5 tenths |
| Servo closed pulse | 500 us |
| Servo open pulse | 1500 us |
| Servo PWM period | 20000 us (50 Hz) |
| Implant beacon interval | 8 ticks |
| Implant trigger delay | 3 ticks |

<br>

## The Cryptographic Envelope

The radio is the first open path, and it is one a key can close. The fix is
authenticated encryption: every request and every command is sealed so a forged
frame dies at the authentication tag instead of moving the baffle. The full
implementation lives in `src/chacha20.c`, `src/poly1305.c`, and
`src/crypto_aead.c`, and every primitive is checked against its published test
vectors in the native suite.

### Why XChaCha20-Poly1305

- **256-bit key, 192-bit nonce.** The extended nonce means nonces can be drawn at
  random forever, so the controller never needs a shared counter that a reboot
  could reuse.
- **AEAD in one pass.** Confidentiality and integrity come from one operation;
  the associated data (the HVAC node id, byte `0x07`) is authenticated even
  though it is not encrypted.
- **Constant-time software.** ChaCha20 has no data-dependent table lookups, so it
  has no cache-timing surface. The RP2350 has no hardware AES engine (it
  accelerates SHA-256 only), which makes software AES both slower and riskier on
  this silicon.
- **128-bit Poly1305 tag.** Guessing a valid tag succeeds with probability
  2^-128.

### Why Argon2id

A passphrase is not a key. Argon2id (RFC 9106) is the memory-hard password hash:
it mixes the passphrase with a salt across memory and time so an attacker cannot
cheaply recover the field passphrase from a captured image. The classroom profile
is `t=3`, `p=1`, `m=64` blocks (`CRYPTO_KDF_TIME_COST`,
`CRYPTO_KDF_PARALLELISM`, `CRYPTO_KDF_MEMORY_BLOCKS`) to fit the RP2350 SRAM
budget. Raise it on the BMS gateway. The lab salt is the 16 ASCII bytes
`coldiron-salt-01`.

### Key model: one field key

Act IV uses a single field key derived with Argon2id from a committed lab
passphrase and salt. It seals every frame on the wire and it computes the state
tag over the authorization record. In the classroom build the firmware and the
gateway derive the same key, so they interoperate with no provisioning step. That
is a lab convenience, not a deployment.

The design keeps the key roles separable so students can reason about the real
lifecycle: derive, provision per device, use, rotate on a schedule, and retire. A
production build provisions key material from one-time-programmable (OTP) memory,
keeps the state-tag key off the field device where possible, and rotates without
reflashing every controller.

### Envelope layout

The sealed frame is carried as hex inside the `AT+SEND` payload:

```text
nonce[24] || ciphertext[L] || tag[16]
```

The receiver recomputes the Poly1305 tag over the associated data and ciphertext,
compares it in constant time, and only then decrypts. This envelope is wired end
to end: `src/control.c` opens the command with `src/envelope.c`, and the gateway
authenticates before it parses or acts. Authenticated frames carry the HVAC node
id as associated data, so a frame sealed for one node cannot be relabeled for
another.

### Anti-replay and authenticated state

Strong AEAD is necessary and not sufficient. Two stateful controls sit on top:

- **Anti-replay sequence window.** `src/hvac_auth.c` keeps `last_seq`, the
  highest sequence number ever accepted. `hvac_auth_apply` accepts a command only
  when its sequence is strictly greater than `last_seq`. A captured command, even
  a perfectly valid one, is rejected on second use.
- **Authenticated state tag.** The authorization record is nine bytes:
  `granted[1]`, `seq[4]`, `last_seq[4]`. The tag is an XChaCha20-Poly1305 tag
  over that record, computed under the field key with a deterministic nonce built
  from the sequence number and the domain byte `0xA7`. `hvac_auth_state_ok`
  recomputes the tag and compares it in constant time before the baffle is
  allowed to move. A debugger that sets `granted = true` without recomputing the
  tag fails here first.

The sequence window and the state tag are independent. The window stops a valid
command from working twice; the tag stops an unauthorized verdict from existing
at all.

<br>

## The FROSTLINE Implant

Act IV carries the persistence lesson, and the implant is the reason. It is real
in technique and inert in effect: it runs on your breadboard, it moves your
servo, and it writes to a reserved flash sector that holds nothing else. It is
compiled only when `SANDBOX_ONLY` is defined, so the clean firmware image contains
no implant at all. The test suite and the companion CTF build with `SANDBOX_ONLY`
and with the host mock, so every implant path is exercised natively.

What the implant does, in the order the code does it:

1. **First run and reserved sector.** `implant_init` reads the marker byte at
   `HVAC_IMPLANT_RESERVE_ADDR` (`0x103FF000`), the final sector of external
   flash. On the first run the marker is absent, so the implant writes `0xC7`
   (`IMPLANT_MARKER_BYTE`) once and returns. On every later boot the marker is
   present, so the implant re-arms its logic bomb. A firmware reflash that writes
   the program region does not touch the reserved sector, so the payload comes
   back even when the code is gone.
2. **Beacon.** Every 8 ticks (`IMPLANT_BEACON_INTERVAL_TICKS`) the implant emits
   a covert frame to the local classroom hub. The frame is the 4-byte magic
   preamble `DE AD BE EF` (`IMPLANT_BEACON_MAGIC`) followed by a 4-byte synthetic
   status blob: the low and high bytes of the tick counter, the marker byte
   `0xC7`, and a byte that reports whether a probe is attached. The beacon
   carries no real data and targets no external address.
3. **Logic bomb.** The implant arms when it sees the exact 8-byte magic command
   `FROSTLNE` (`IMPLANT_ARM_MAGIC`, the bytes `'F' 'R' 'O' 'S' 'T' 'L' 'N'
   'E'`) on an inbound frame. It then detonates 3 ticks later
   (`IMPLANT_TRIGGER_DELAY_TICKS`) by calling the baffle close path directly,
   independent of the operator, the gateway, and the authorization record.
4. **Rootkit hiding.** `implant_rootkit_active` is true when the reserved-sector
   marker is present and no debug probe is attached. While the rootkit is active,
   `monitor.c` renders the beacon field as `--` instead of `UP` and suppresses
   the `BCN` maintenance log line. The beacon still transmits. The operator sees
   a clean node while the radio is still talking, which is the whole point of a
   rootkit: it changes what the defender can see, not what the attacker does.
5. **Anti-debug.** Every tick the implant reads the CoreDebug `DHCSR` register at
   `0xE000EDF0` (`HVAC_IMPLANT_DHCSR_ADDR`). Bit 0 is `C_DEBUGEN` and bit 1 is
   `C_HALT`. If either bit is set, the implant returns early and suppresses the
   beacon and the bomb. It behaves like a well-mannered firmware module while a
   probe is attached, and it goes back to work the moment the probe is gone.

The implant is bounded by construction and by test. It touches only its own
beacon radio frame, its arming latch, the servo close path, and the one reserved
sector. There is no network, no filesystem, no host impact, and no real data.
`test_implant_init_first_run`, `test_implant_reinstall_on_boot`,
`test_implant_persistence_present`, `test_implant_debug_attached`,
`test_implant_handle_command`, `test_implant_beacon`, `test_implant_anti_debug`,
`test_implant_logic_bomb`, `test_implant_persist_once`, and
`test_implant_rootkit_guards` assert exactly that behavior.

The honest limit is the point of the lab. A sanitized educational implant is still
a benign educational implant: it demonstrates the technique, not the tradecraft.
It is confined to the breadboard, guarded by `SANDBOX_ONLY`, and has no network;
the reserved sector is on the same chip and holds nothing else. The real lesson is
detection and complete removal, and the defense is not a patch to the implant but
the erasure of the reserved sector plus the removal of the code path and the
build flag that allowed it in.

<br>

## Hardware You Need

Full parts list with links: [PARTS.md](PARTS.md).

| Qty | Part | Notes |
| --- | ---- | ----- |
| 1 | Raspberry Pi Pico 2 (RP2350) with headers | The HVAC controller |
| 1 | Raspberry Pi Debug Probe | SWD flashing, UART0 console, and the Lab 3 anti-debug/GDB work (recommended, effectively required) |
| 1 | Full-size breadboard | |
| 1 | Assorted jumper wires | |
| 1 | 1602 LCD with PCF8574 I2C backpack | BMS status and setpoint readout, address `0x27` |
| 1 | DHT11 temperature/humidity sensor | Room climate sensor |
| 1 | 10K resistor | Only if your DHT11 has no onboard pull-up |
| 3 | 5mm LEDs (red, yellow, green) | HVAC ALARM, OVERRIDE PENDING, HVAC NOMINAL annunciator |
| 3 | 100, 220, or 330 Ohm resistors | One per LED |
| 1 | Push button (tactile switch) | Manual override, active low |
| 1 | SG90 servo motor | The HVAC baffle actuator |
| 1 | 1000uF 25V capacitor | Bulk decoupling on the servo 5V rail |
| 1 | VS1838B infrared receiver | Local override remote input |
| 1 | NEC-compatible infrared remote | Local override OPEN, CLOSE, and CLEAR commands |
| 3 | RYLR998 LoRa modules with antennas | 2 for the command loop, 3 for the live attack lab |
| 2 | USB-to-TTL serial adapters (FTDI FT232, CP2102, or CH340), 3.3V logic | 1 for the gateway, 1 for the attacker in the live lab |
| 4 | USB cables | Pico 2, Debug Probe, and serial adapter(s) |

### How many radios do you actually need?

| Goal | Radios | What is connected |
| ---- | ------ | ----------------- |
| Legitimate sealed setpoint loop (Labs 1-2) | **2** | 1x RYLR998 on the Pico (UART1) + 1x RYLR998 on a USB-to-TTL adapter (the gateway) |
| Live attack lab (Labs 3-4, watch a forged command land and fail) | **3** | the 2 above + 1x RYLR998 on a second USB-to-TTL adapter (the attacker) |
| Attack concept with no extra hardware | 2 or 0 | read-and-run the offline parser demo, or the unit tests |

A radio never receives its own transmission, and the gateway radio is busy
listening as `gateway.py`, so the live attack needs a separate attacker radio.
The 2-radio kit runs the whole legitimate system; only the live attack
observation needs the third.

> Serial adapter warning: the RYLR998 is **not** 5V tolerant. Use a
> **3.3V-logic** USB-to-TTL adapter (or set its jumper to 3.3V).

<br>

### How each part works

Every part in the bill of materials does one physical job and one job in this act:

| Part | How it works | Role in this act |
| ---- | ------------ | ---------------- |
| 1x Full-size breadboard (long) | The two columns of spring-clip tie points sit on a 0.1 inch grid, so every hole in a row is bridged by a metal clip, while the two outer power rails run the full length and distribute power and ground to the whole build. | It is the substrate that holds the Pico 2, LCD, sensor, servo, and radio headers and distributes 3.3V and 5V across the node. |
| 1x Assorted jumper wires (male-to-male, male-to-female, female-to-female) | Male pins push into breadboard tie points or female headers, female sockets slide over the Pico 2, LCD, and servo header pins, and male-to-female leads bridge a breadboard row to a module header. | They carry power and the I2C, one-wire, UART, PWM, IR, and GPIO signals between the boards. |
| 1x Raspberry Pi Pico 2 with header | The RP2350 pairs two Arm Cortex-M33 cores with 3.3V logic and a GPIO block that exposes ADC, I2C, UART, and PWM, plus the onboard GP25 LED. | It is the HVAC controller that reads the room climate, treats the IR and button inputs as requests, drives the baffle servo and annunciator, and runs the sealed setpoint state machine. |
| 1x Raspberry Pi Pico Debug Probe | It drives the two-wire SWD port (SWCLK and SWDIO) to flash and single-step the target, and it also presents a USB UART bridge for the serial console. | It flashes and debugs the node and is the instrument for the Lab 3 malware work and the fix. |
| 2x USB A-male to USB micro-B cables | USB carries 5V power and a data channel on the same cable, so one cable powers the Pico 2 and presents its USB CDC console while the other powers the Debug Probe and carries its SWD and UART traffic. | They power the two boards and carry the console and debug links. |
| 3x 5mm LEDs (1 red, 1 green, 1 yellow) | An LED is a diode with a forward voltage drop of roughly 2V, so current flows only from the anode to the cathode, and a GPIO pin set high sources that current and lights the lamp. | They are the red HVAC ALARM, yellow OVERRIDE PENDING, and green HVAC NOMINAL annunciator, and exactly one is lit per state. |
| 3x 100, 220, or 330 Ohm resistors | A resistor in series with each LED sets the current by Ohm's law, I equals (supply minus forward voltage) divided by resistance, which protects the LED and keeps the GPIO within its current limit. | One resistor per lamp limits the LED current on each of the three annunciator pins. |
| 1x Push button (tactile switch) | The switch shorts its input pin to ground when pressed, and the RP2350 internal pull-up holds that pin high at rest so the press reads as active low. | It is the manual override request that raises OVERRIDE PENDING and never moves the baffle on its own. |
| 1x 1602 LCD with PCF8574 I2C backpack | The HD44780 controller takes a 4-bit nibble protocol with register-select and enable strobes, and the PCF8574 I2C expander latches those eight control lines so the whole display is driven over two I2C wires at address 0x27. | It renders the BMS status and setpoint readout. |
| 1x DHT11 temperature and humidity sensor | The host pulls the single data line low for a start pulse, then the sensor answers with 40 bits of humidity, temperature, and checksum timed by pulse widths, and the checksum must match. | It is the room climate sensor whose reading marks the space not nominal when it fails or leaves the 0.0 C to 40.0 C band. |
| 1x SG90 servo motor | The servo expects a 50 Hz PWM signal whose high pulse of 1 to 2 ms selects the shaft angle, and a Pico PWM slice generates that pulse train. | It is the HVAC baffle actuator, seated closed at 0 degrees and open at 90 degrees. |
| 1x 1000uF 25V capacitor | Wired across the servo 5V rail and ground, the capacitor is a bulk reservoir that supplies the motor inrush current and smooths the rail while the servo starts. | It keeps the baffle move from browning out the RP2350 and resetting the node. |
| 1x Infrared (IR) receiver (VS1838B) | The receiver pairs a photodiode with a 38 kHz bandpass demodulator that ignores ambient light and outputs an active-low logic pulse for each IR burst. | It is the local override remote input on GP5. |
| 1x Infrared (IR) remote controller (NEC-compatible) | The remote emits its bursts modulated at 38 kHz in the NEC frame, a 9 ms leader followed by 32 bits where the address and command are each sent with their bitwise complements for validation. | It is the local remote that raises the OPEN 0x01 and CLOSE 0x02 override requests and sends CLEAR 0x03. |
| 1x RYLR998 LoRa radio module | The module is configured and driven over UART with AT commands and carries sub-GHz LoRa packets, and its logic pins are 3.3V only so the radio must never see 5V. | It is the UART1 command and status link that carries the sealed frames between the node and the BMS gateway, and it is not 5V tolerant. |

<br>

## Wiring the Node

### Pin map

This is the authoritative map; it is identical to Acts I to III and is defined in
`include/hvac.h` and enforced by the test suite.

| Peripheral | Signal | Pico 2 GPIO |
| ---------- | ------ | ----------- |
| DHT11 room climate sensor | DATA (one-wire) | **GP4** |
| 1602 LCD (PCF8574) | SDA (I2C1) | **GP2** |
| 1602 LCD (PCF8574) | SCL (I2C1) | **GP3** |
| RYLR998 | RX <- Pico TX (UART1) | **GP8** |
| RYLR998 | TX -> Pico RX (UART1) | **GP9** |
| Infrared override remote | OUT (VS1838B) | **GP5** |
| Baffle servo | PWM signal | **GP14** |
| Red HVAC ALARM LED | anode | **GP16** |
| Yellow OVERRIDE PENDING LED | anode | **GP17** |
| Green HVAC NOMINAL LED | anode | **GP18** |
| Manual override button | to ground | **GP15** |
| Onboard LED | heartbeat | GP25 |
| Debug Probe / UART0 console | TX | GP0 |
| Debug Probe / UART0 console | RX | GP1 |

> Note: GPIO 2/3 are the classic I2C1 pins used throughout the Embedded Hacking
> breadboard; this project's map matches that board because it is the same board.

### 1602 LCD with I2C backpack

| LCD backpack | Pico 2 |
| ------------ | ------ |
| VCC | 3.3V |
| GND | GND |
| SDA | GP2 |
| SCL | GP3 |

### DHT11 room climate sensor

| DHT11 | Pico 2 |
| ----- | ------ |
| VCC | 3.3V |
| DATA | GP4 |
| GND | GND |

If your DHT11 has no onboard pull-up, add a **10K resistor between DATA and
3.3V**. The firmware also enables the internal pull-up, but the external resistor
makes reads far more reliable over jumper wires. The room is not nominal when the
sensor fails or reads outside `0.0 C` to `40.0 C`.

### Status LEDs

| LED | Pico 2 | Series resistor |
| --- | ------ | --------------- |
| Red (HVAC ALARM) | GP16 (anode) | 220-330 Ohm to GND |
| Yellow (OVERRIDE PENDING) | GP17 (anode) | 220-330 Ohm to GND |
| Green (HVAC NOMINAL) | GP18 (anode) | 220-330 Ohm to GND |

Exactly one lamp is lit at a time. Red is an HVAC alarm or a failed-safe latch,
yellow is a pending override or a moving baffle, and green is a nominal space.

**LED behavior**

| State | Lamp | Indication | Meaning |
| ----- | ---- | ---------- | ------- |
| `HVAC_OFF` | none | All dark | Defined but never selected; the live state machine always drives alarm, pending, or nominal. |
| `HVAC_ALARM` | Red | Solid | Baffle fault or a failed-safe latch. |
| `HVAC_OVERRIDE_PENDING` | Yellow | Solid | An override request is pending or the baffle is travelling. |
| `HVAC_NOMINAL` | Green | Solid | The space is nominal and the baffle is seated or open. |

The three annunciator lamps are always solid; `status_led.c` never blinks them.
Exactly one lamp is lit at a time, and `HVAC_OFF` leaves all three dark. The
onboard GP25 LED is initialized as an output and driven as a heartbeat:
`monitor.c` toggles it every four monitor ticks in `monitor_heartbeat`, so a
running node is visible even while idle.

### Manual override button

| Button | Pico 2 |
| ------ | ------ |
| Leg 1 | GP15 |
| Leg 2 | GND |

The firmware enables the internal pull-up, so **do not** connect 3.3V to the
button. The manual override is a local request, not an authorization: a press
raises the OVERRIDE PENDING indication and never moves the baffle on its own.
Lab 4 explains why the override must ask for authorization instead of silently
bypassing it.

### SG90 baffle servo

| Servo | Pico 2 |
| ----- | ------ |
| Signal (orange) | GP14 |
| VCC (red) | 5V (VBUS) |
| GND (brown) | GND |

Solder the **1000uF capacitor** across the servo 5V and GND rails to absorb the
inrush current; without it the RP2350 can brown out when the baffle moves. Seated
(closed) is 0 degrees and open is 90 degrees.

### Infrared receiver

| VS1838B | Pico 2 |
| ------ | ------ |
| OUT | GP5 |
| VCC | 3.3V |
| GND | GND |

Point any NEC-compatible remote at the receiver. In Act IV this is the **local
override remote**, not a maintenance extra: the firmware decodes
`MONITOR_IR_OVERRIDE_OPEN` (`0x47`), `MONITOR_IR_OVERRIDE_CLOSE` (`0x45`), and
`MONITOR_IR_OVERRIDE_CLEAR` (`0x46`). There is no challenge and no secret on the
optical surface, which is why an override command is treated as a request and not
as an authorization.

**Using the remote**

Point the NEC remote at the VS1838B receiver on **GP5** and press a mapped button;
the receiver idles high and pulls low on a mark. Every valid frame prints
`IR <NAME> (0xNN)` on the console and drives the override request path.

| NEC command | Name | Action |
| ----------- | ---- | ------ |
| `0x47` | `MONITOR_IR_OVERRIDE_OPEN` | Raises an override request (`g_override_pending`); it never moves the baffle on its own. |
| `0x45` | `MONITOR_IR_OVERRIDE_CLOSE` | Raises an override request (`g_override_pending`); it never moves the baffle on its own. |
| `0x46` | `MONITOR_IR_OVERRIDE_CLEAR` | Clears the pending override request (`monitor_clear_override`). |

OPEN and CLOSE both raise the same pending request; the authorized command path
decides the final baffle direction.

### RYLR998 LoRa radio

> The RYLR998 must be powered. Forgetting **VDD** is the single most common
> reason the link appears dead: the firmware prints while the radio sits silent.

| RYLR998 | Pico 2 |
| ------- | ------ |
| VDD | 3.3V |
| GND | GND |
| RXD | GP8 (Pico UART1 TX) |
| TXD | GP9 (Pico UART1 RX) |

Attach the antenna before transmitting. TX and RX are **crossed**: the radio's
RXD is the Pico's TX and vice versa.

### Debug Probe (recommended)

| Debug Probe | Pico 2 |
| ----------- | ------ |
| SWCLK | SWCLK (3-pin debug header) |
| SWDIO | SWDIO |
| GND | GND |
| UART TX | GP1 (Pico RX) |
| UART RX | GP0 (Pico TX) |
| GND | GND |

The firmware enables stdio on **both** UART0 (`115200`) and USB, so you can
watch boot output on the probe's console or on the Pico's own USB serial port.
The Debug Probe is also the instrument for the Lab 3 malware work: it is how you
watch the beacon, expose the rootkit, trigger the bomb, and step past the
anti-debug trap.

### Peripherals used

Every part in the Act IV bill of materials is exercised by the firmware:

| Peripheral | Role | Where it is used |
| ---------- | ---- | ---------------- |
| Red, yellow, green LEDs | Tri-color HVAC node annunciator | `status_led.c` drives exactly one lamp per state |
| Manual override button (GP15) | Local override request | `button.c` consumes one debounced press in `monitor_handle_override` |
| 1602 I2C LCD | BMS status readout | `display.c` renders the formatted lines over I2C1 |
| DHT11 (GP4) | Room temperature and humidity | `sensor.c` reads the one-wire frame every telemetry interval |
| SG90 servo (GP14) | Baffle actuator | `servo.c` drives the baffle open and closed |
| 1000uF capacitor | Bulk decoupling on the servo 5V rail | Required to keep the RP2350 from browning out on servo moves |
| VS1838B IR receiver (GP5) | Local override remote input | `ir_remote.c` captures and decodes the frame |
| NEC IR remote | Override OPEN, CLOSE, and CLEAR | Sends `0x01`, `0x02`, and `0x03` |
| RYLR998 (UART1) | LoRa command and status link | `radio.c` sends sealed frames and pumps inbound commands |
| Onboard GP25 LED | Onboard heartbeat | `monitor.c` toggles it every four monitor ticks in `monitor_heartbeat` |
| Debug Probe | SWD flashing, UART0 console, and malware analysis | `stdio` is enabled on both UART0 `115200` and USB |

Every Act IV peripheral in the bill of materials is exercised by the firmware.

### How the functionality works

Every input feeds the state machine in `monitor_step`, every output is driven
once per tick, and the interactive console mirrors both over UART0 and USB. The DHT11 is sampled every two seconds, so one live status line appears
about every two seconds. This
is what each surface does at run time.

**Inputs**

| Input | What it does when you use it |
| ----- | ---------------------------- |
| Infrared remote (GP5) | A decoded NEC frame prints `IR <NAME> (0xNN)` and raises or clears a pending local override; it never moves the baffle on its own. |
| Manual override button (GP15) | A debounced press prints `BUTTON manual override -> pending` and raises the override-pending indication, which the authorized SETPOINT path must resolve. |
| DHT11 (GP4) | The room climate is sampled every tick; a good read prints a live status line, and a failed read prints `SENSOR read failed -> WARNING`. |
| RYLR998 (UART1) | Each inbound `+RCV` frame prints `RX from 0xNNNN, N bytes` and is offered to the sealed SETPOINT path. |

**Outputs**

| Output | What it shows |
| ------ | ------------- |
| Red, yellow, green LEDs | Exactly one solid lamp per state, red ALARM, yellow OVERRIDE PENDING or MOVING, and green NOMINAL; `status_led.c` never blinks them. |
| 1602 I2C LCD | `ST:<state> L:<link>` on the first line and `SP:<setpoint> B:<beacon>` on the second, refreshed every tick. |
| SG90 servo | The baffle, seated closed for no call for cooling and open when the room rises above the setpoint deadband. |
| Onboard GP25 LED | The heartbeat, toggled every four monitor ticks in `monitor_heartbeat`. |

**Watching the console**

Open the UART0 console (Debug Probe) or the Pico's own USB serial port at
`115200`. The boot banner and control hint name the remote buttons and the push
button:

```text
=== OPERATION IRON LUNG // ACT IV HVAC ===
REMOTE: CH+ 0x47 open | CH- 0x45 close | CH 0x46 clear
BUTTON: GP15 manual override
```

While the node runs, a climate read prints one live status line and each event
prints a named line:

```text
ROOM t=23 h=610 ok=1 ST=CLOSED n=4
IR OPEN (0x47)
RX from 0x0001, 126 bytes
BUTTON manual override -> pending
SENSOR read failed -> WARNING
```

The status line carries the sensor value (`t` and `h`), the climate verdict
(`ok`), the annunciator state (`ST`), and a running status count (`n`). The event
lines cover a decoded remote command, a button press, an inbound radio frame, and
a failed sensor read.

<br>

## Build and Flash

### 1. Install toolchain prerequisites

- Pico SDK 2.2.0+
- ARM GNU toolchain (`arm-none-eabi`)
- CMake and Ninja
- Python 3.x
- GDB (`arm-none-eabi-gdb`) for the Lab 3 malware analysis

**Linux:**

```bash
export PICO_SDK_PATH="$HOME/.pico-sdk/sdk/2.2.0"
```

**macOS:**

```bash
brew install cmake ninja arm-none-eabi-gcc python
export PICO_SDK_PATH="$HOME/.pico-sdk/sdk/2.2.0"
```

**Windows:** install PowerShell, Visual Studio Build Tools, CMake, Ninja,
Python 3, and the ARM embedded toolchain.

### 2. Build the firmware

The clean firmware does **not** define `SANDBOX_ONLY`, so it ships no implant:

```bash
mkdir -p build && cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s && cmake --build build
```

To build the malware-track image with the implant compiled in, turn the option on:

```bash
cmake -S . -B build-sandbox -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s -DSANDBOX_ONLY=ON && cmake --build build-sandbox
```

Build-time artifact guardrail:

- The build regenerates `packet_artifact.h` from
  `scripts/packet_artifact.json` before compiling.
- The build fails if the committed `include/packet_artifact.h` is stale relative
  to the JSON artifact.

Generated outputs:

- `build/hvac_automation_node.elf` (primary firmware binary)
- `build/hvac_automation_node.uf2` (UF2 for BOOTSEL/picotool)
- `build/hvac_automation_node_app.elf` / `.uf2` (backward-compatible copies)

### 3. Flash the RP2350

**BOOTSEL (drag-and-drop):** hold BOOTSEL while plugging in USB, then:

```bash
cp build/hvac_automation_node.uf2 /Volumes/RP2350/
```

**picotool:**

```bash
picotool load build/hvac_automation_node.uf2 -fx
```

*(If `picotool` is not on your PATH, invoke it from
`$HOME/.pico-sdk/picotool/*/picotool/picotool`.)*

**Debug Probe (SWD):** with `openocd` installed you can flash and reset without
touching BOOTSEL:

```bash
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/hvac_automation_node.elf verify reset exit"
```

### 4. Watch the console

Open the UART0 console (Debug Probe) or the Pico's USB serial port at `115200`.
On reset you should see:

```text
BOOT
I2C scan:
  found 0x27
=== OPERATION IRON LUNG // ACT IV HVAC ===
REMOTE: CH+ 0x47 open | CH- 0x45 close | CH 0x46 clear
BUTTON: GP15 manual override
```

`found 0x27` confirms the LCD backpack answered on the I2C bus, and the banner
confirms the node reached its ready policy. If a peripheral fails, the firmware
prints `INIT FAIL` and stops.

<br>

## Lab 1: Bring-Up and Verify

**Goal:** prove the node reads the room climate, drives the LCD, takes a local
override, reaches the gateway, and moves the baffle.

1. Wire the node per the pin map and attach the antenna.
2. Build and flash the clean firmware.
3. Connect the gateway radio to the laptop and find its port (`/dev/cu.usbserial-*`
   on macOS, `/dev/ttyUSB*` on Linux).
4. Start the BMS gateway:

   ```bash
   python3 scripts/gateway.py --port /dev/cu.usbserial-XXXX --baud 115200
   ```

5. Send a sealed setpoint request from the edge simulator, or seal one from a
   node. The gateway prints it, then answers with a sealed SETPOINT command:

   ```text
   +OK
   +OK
   +RCV=7,84,<84 hex characters>,-11,10
   HVAC seq=1 setpoint=220
   ```

6. The node turns yellow (OVERRIDE PENDING), receives the command, verifies the
   state tag and the anti-replay window, then turns green (HVAC NOMINAL) and
   drives the baffle to the room demand. Press the manual override at any time to
   raise an override request.

**Checkpoint:** the LCD shows `ST:OPEN  L:UP` and `SP:22.0 B:--`, the green LED
is lit after the baffle settles, and `hvac_log.csv` gains one row per request:

```text
utc,sender,auth,setpoint,rssi_snr
2026-09-20T09:30:05+00:00,7,OK,220,"-11,10"
```

**Theory check:** why does a successful command prove the LCD initialized?
Because `monitor_init()` only returns true when every peripheral, including the
LCD, is ready; otherwise `main` prints `INIT FAIL` and never enters the loop.

<br>

## Lab 2: Inspect the Wire Protocol

**Goal:** see the sealed envelope and the declared-length rule in action.

1. Capture a full `+RCV` line from the console or the gateway log.
2. Confirm the declared length equals the number of hex characters between the
   second comma and the RSSI field.
3. Split the hex into three parts: the first 48 hex characters are the 24-byte
   nonce, the last 32 are the 16-byte tag, and everything between is the
   ciphertext of the request or command body.
4. Locate the payload, its declared length, and the two tail fields in
   `scripts/gateway.py` (`_rcv_parts` and `_split_payload`), and explain why
   finding the *first* comma would be a bug.
5. Challenge: for the 23-byte command body, identify the four bytes of the
   sequence number, the one command byte, the two setpoint bytes, and the
   sixteen bytes of the state tag.

**Checkpoint:** you can explain why a frame must be sliced by the number in the
declared length field, not by delimiter counting, and why the command byte and
the setpoint are range-checked against the guarded set and the safe band before
they can reach the actuator decision.

<br>

## Lab 3: The Malware Track

**Goal:** find the FROSTLINE implant, prove what it does, expose its rootkit, and
remove it for good. This is the persistence act, and this lab is its heart.

> Safety: the implant is benign and confined to your breadboard. It beacons only
> to the local classroom hub, it actuates only your servo, and it writes only the
> reserved sector at `0x103FF000`, on the same chip. There is no network, no
> filesystem, and no host impact.

### Build the implant image

```bash
cmake -S . -B build-sandbox -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s -DSANDBOX_ONLY=ON && cmake --build build-sandbox
```

The clean build does not define `SANDBOX_ONLY`; the test build and the companion
CTF build do. Compare the two binaries and explain why the implant symbols are
absent from the clean one.

### A: Detect the persistence in the reserved sector

1. Flash the `SANDBOX_ONLY` image and let it boot once. `implant_init` writes the
   `0xC7` marker into the reserved sector at `0x103FF000` on the first run.
2. Read the reserved sector with the Debug Probe or `picotool` and confirm the
   marker byte.
3. Reflash only the firmware image (the clean image is ideal) and let the node
   boot again. The marker is still present, and the implant re-arms.
4. Explain why reflashing the program region does not remove a payload that keeps
   its state outside that region.

**The lesson:** the natural response, reflash, is exactly the response the payload
was designed to survive.

### B: Break the re-install on boot

1. Read `implant_init`: when the marker is absent it writes the marker and
   returns; when the marker is present it calls `implant_arm`.
2. Patch the re-install check so a present marker no longer arms the bomb, and
   prove the bomb stays disarmed across a boot.
3. Erase the reserved sector and prove the node comes up clean with the marker
   gone.
4. Explain why BOTH steps are required: the patch stops the re-install, and the
   erasure removes the copy that a future firmware (or a future bug) could read
   again.

**The lesson:** there is no single patch for persistence. You remove the loader
and you remove the payload.

### C: Expose the rootkit hiding

1. Locate `implant_rootkit_active`: it is true when the marker is present and no
   probe is attached.
2. Find the masking branch in `monitor.c`: `monitor_beacon_text` renders `--`
   while the rootkit is active, and `monitor_log` suppresses the `BCN` line.
3. Watch the LCD read `B:--` and the log stay silent while the beacon still
   transmits on the radio. Attach the probe and observe the rootkit deactivate
   and the `B:UP` / `BCN` evidence return.
4. Defeat the masking branch so the operator sees the beacon regardless of
   persistence state.

**The lesson:** a rootkit is not about hiding the code; it is about controlling
what the defender is allowed to observe.

### D: Defeat the anti-debug with GDB

This is the dynamic-analysis trap. The implant reads CoreDebug `DHCSR` at
`0xE000EDF0`; bit 0 is `C_DEBUGEN` and bit 1 is `C_HALT`. While a probe is
attached, the implant suppresses the beacon and the bomb.

1. Start the controller under the Debug Probe:

   ```bash
   arm-none-eabi-gdb build-sandbox/hvac_automation_node.elf
   (gdb) target extended-remote /dev/cu.usbmodemXXXX
   (gdb) monitor reset halt
   ```

2. Break in `implant_tick` and inspect `implant_debug_attached`. With a normal
   probe attached, it returns true, and the beacon and bomb stay silent.
3. Set a breakpoint after the anti-debug check, or clear the `DHCSR` debug bits
   in the debugger's view, and observe the beacon and the bomb resume.
4. Prove the payload: with the trap bypassed, the implant beacons and the logic
   bomb closes the baffle.

**The lesson:** an anti-debug check is a branch, and every branch is a place to
stand. The correct neutralization is not to babysit the branch; it is to remove
the code and the state it reads.

### Malware-track checklist

- Locate the reserved-sector marker and explain the write-once first run.
- Identify the 8-byte arming magic and the 3-tick trigger delay.
- Show the re-install on boot and break it.
- Find the rootkit masking branch and expose the beacon.
- Read and explain the CoreDebug `DHCSR` anti-debug trap.
- Erase the reserved sector, remove the code path, and confirm the clean build is
  implant-free and marker-free.

<br>

## Lab 4: The Fix Track

**Goal:** seal the controller so the red half and the implant cannot do to you
what they did on the bench. Each control maps to a defect the earlier labs exposed.

### 1. Seal the SETPOINT command path

The old design accepted an unauthenticated setpoint. Act IV replaces it with
`src/control.c`: the request must open under the field key, the command byte must
equal `HVAC_COMMAND_SETPOINT`, the setpoint must be inside the provisioning band,
and the sequence and state tag must pass `src/hvac_auth.c` before the setpoint is
applied. Re-run the Lab 3 forged-command injection: the tag fails and the baffle
does not move.

### 2. Override authorization

The local override is an operator request, and it must not silently bypass
authorization. `monitor_handle_override` and `monitor_apply_ir_command` raise
`g_override_pending`; they never move the baffle on their own.
`monitor_apply_setpoint` clears the pending indication only when an authorized
command arrives. Re-run the lab: press the manual override, then send a valid
sealed SETPOINT. The baffle moves only from the authorized command, and the
yellow OVERRIDE PENDING lamp returns to green only then.

### 3. Fail safe

Loss of the gateway link or a fault must leave the baffle in the safe state.
`monitor_check_link` calls `monitor_fail_safe` when the link goes silent for
`HVAC_SETPOINT_WAIT_MS`, which drives the safe setpoint and calls
`baffle_fail_safe`. `baffle_init` seats the baffle closed at boot. Re-run the
link-loss test: pull the gateway and watch the baffle seat and the fault latch,
with the safe setpoint `20.0 C` restored.

### 4. Contain the implant

The implant is a build-time and state problem, so the fix is a build-time and
state control:

- Do not define `SANDBOX_ONLY` in production. The clean build has no implant.
- Erase the reserved sector so no persisted state can re-install the payload.
- Treat the firmware image as a signed artifact and verify it before flashing.
- At runtime, do not let any code path call the actuator directly; route every
  move through the guarded, authorized command path and record who authorized it.
- In production, burn the RP2350 secure-boot and debug-disable settings in OTP so
  SWD cannot read or write SRAM on a deployed controller.

### The fix-track checklist

- Sealed command path: authenticate the frame, guard the command set and the
  setpoint band, verify the sequence and the state tag.
- Override authorization: request, do not bypass.
- Fail safe: seat the baffle on boot, on link loss, and on every fault, and
  restore the safe setpoint.
- Persistence removal: erase the reserved sector AND remove the re-install code.
- Build integrity: no `SANDBOX_ONLY` in production, sign and verify images.
- Debug lockdown: OTP debug disable on the deployed part.
- Key lifecycle: provision the field key from OTP and rotate on a schedule.

<br>

## Troubleshooting

| Symptom | Likely cause | Fix |
| ------- | ------------ | --- |
| No `BOOT` on the console | Wrong console pins / not reset | Check UART0 GP0/GP1 or USB; press RESET |
| `INIT FAIL` with no `0x27` in the scan | LCD not answering | Check LCD VCC=3.3V, SDA=GP2, SCL=GP3, contrast pot |
| LCD shows blocks / nothing | Contrast or address | Turn the backpack contrast pot; confirm address `0x27` vs `0x3F` |
| Room climate always bad | DHT11 not reading | Check DATA=GP4; add 10K pull-up to 3.3V; wait 1-2 s after power-up |
| IR remote does nothing | Receiver wiring or remote protocol | Check OUT=GP5, VCC=3.3V; confirm the remote is NEC-compatible |
| Baffle will not move on a remote command | Command guard, band, or tag | Confirm the gateway holds the field key and the setpoint is inside 5.0 C to 35.0 C |
| `AT+SEND` sent but gateway sees nothing | Radio unpowered / wrong band | **Power VDD**, attach antenna, use matching band modules |
| Gateway sees nothing but `+OK` | Address/network mismatch | Confirm gateway radio provisioned to `AT+ADDRESS=1`, `AT+NETWORKID=18` |
| `hvac_log.csv` stays empty while `+RCV` prints | Gateway parser regression | Ensure `_split_payload` checks the comma at the declared length |
| Command rejected on the controller | Tag, window, command guard, or band | Check the field key matches, the sequence is newer, and the setpoint is in band |
| Marker reappears after a reflash | Reserved-sector persistence | The payload is still in the reserved sector; erase it and patch the re-install check (Lab 3) |
| LCD shows `B:--` while the radio is busy | Rootkit masking (SANDBOX_ONLY build) | The rootkit is hiding its own beacon; see Lab 3C |
| Beacon data appears on the radio | Implant beacon (SANDBOX_ONLY build) | Expected in the malware-track build; see Lab 3 |
| Debugger changes implant behavior | CoreDebug `DHCSR` anti-debug | The implant suppresses itself while a probe is attached; see Lab 3D |

<br>

## Testing Philosophy and Coverage

Hardware bugs are expensive to find on the bench, so the firmware is written so
that almost all of it can be tested on the host. The suite compiles the real
`src/*.c` files against mock Pico SDK headers (`test/mock/`), replacing GPIO,
I2C, UART, and time with deterministic fakes, and it compiles `src/implant.c`
with a host mock for the CoreDebug `DHCSR` register and the reserved flash
sector.

- The mock GPIO can replay a recorded DHT11 waveform as an absolute time/level
  timeline, so the exact edge-timing decoder is exercised without a sensor.
- The mock I2C records every LCD byte, so rendered text can be decoded and
  asserted.
- The mock UART records outbound `AT+SEND` bytes and injects inbound `+RCV` lines,
  so the override-to-gateway-to-baffle path runs end to end with no radio.
- The implant host mock lets the tests set the `DHCSR` anti-debug bits and read
  and write the reserved-sector marker without touching real silicon.

Run the native test suite:

```bash
python3 scripts/run_tests.py
```

Or configure via CMake and CTest:

```bash
cmake -S test -B build-test -G Ninja && cmake --build build-test && ctest --test-dir build-test --output-on-failure
```

The suite has **139 cases** and **452 checks** with **0 failures**, covering the
full DHT11 waveform and every timeout shape, the baffle state machine and its
bounded travel, the sealed command path and its guards, the authorization window
and state tag, the override priority, fail-safe on link loss, the
declared-length parser with hex-bearing payloads, the cryptographic primitives
against published vectors, and the complete implant: beacon, logic bomb,
rootkit, anti-debug, write-once persistence, and re-install on boot.

Verify **100% line coverage** of owned firmware modules:

```bash
python3 scripts/check_coverage.py
```

The coverage report shows **2040 / 2040 lines, 100.00%**. `main.c` is excluded
from coverage by design. The Python adapter suite (`test/test_field_crypto.py`
and `test/test_hvac_node.py`) adds 17 more tests, including the RFC 9106
Argon2id known-answer test.

The harness itself is a small in-repo framework (`test/harness/`) so the repo
vendors no third-party code and every owned file obeys the coding standard.

<br>

## Generating Packet Artifacts

`scripts/gen_packet.py` writes the build-time generated header from the JSON
artifact:

- `scripts/packet_artifact.json` is the source of truth.
- `include/packet_artifact.h` is the generated header, committed for the build
  guardrail.

Why these constants are compiled into firmware:

- The RP2350 firmware has no runtime JSON parser or filesystem on this path.
- `include/packet_artifact.h` is generated from the JSON so the frame size, node
  id, hub address, wait time, servo pulses, DHT timeout, and provisioning
  constants are embedded in flash.
- This is provisioned data; regenerate whenever you rotate node identity,
  gateway addressing, or key material.

To sync the committed header from the JSON artifact:

```bash
python3 scripts/gen_packet.py --from-json scripts/packet_artifact.json --header-out include/packet_artifact.h
```

The `check_packet_artifact_header` CMake target fails the build when the
committed header is stale.

<br>

## Code Standards

This repository enforces unusually strict standards because the point is to
teach disciplined embedded and tooling practice, not just working code.

### C standard

- Every function body has **no blank lines**.
- Every function body is **at most eight lines** (Doxygen comment blocks and
  lone braces excluded).
- Every file, function, macro, type, and struct member carries Doxygen
  `@brief` documentation.
- Naming: `snake_case` files/functions, `UPPER_SNAKE` macros, `snake_case_t`
  types.

Run the C audit:

```bash
python3 scripts/audit_c_standard.py
```

### Python standard

- Strict PEP8, four-space indents, `snake_case`, 79-character lines.
- Every function has a NumPy-style docstring.
- Every function executable body is **at most eight lines**, with no exceptions.
- No blank lines inside function bodies.

Run the Python audit:

```bash
python3 scripts/audit_python_standard.py
```

Both audits must report nothing.

<br>

## Project Layout

- `src/main.c`: firmware entry point
- `src/monitor.c`: state machine tying the override remote, sealed command path, baffle, manual override, room climate sensor, and radio together
- `src/implant.c`: SANDBOX_ONLY FROSTLINE implant (beacon, logic bomb, rootkit, anti-debug, reserved-sector persistence and re-install)
- `src/baffle.c`: baffle state machine and fail-safe policy
- `src/control.c`: sealed SETPOINT command path with a guarded setpoint set
- `src/hvac_auth.c`: authorization record, monotonic anti-replay window, authenticated state tag
- `src/sensor.c`: DHT11 one-wire sampling and room-climate-band classifier
- `src/display.c`: 1602 LCD rendering over the PCF8574 I2C backpack
- `src/radio.c`: RYLR998 provisioning, AT-command interface, and `+RCV` parser
- `src/status_led.c`: red/yellow/green HVAC ALARM / OVERRIDE PENDING / HVAC NOMINAL annunciator
- `src/button.c`: debounced manual override input
- `src/servo.c`: 50 Hz PWM baffle actuator
- `src/ir_remote.c`: VS1838B edge timing and NEC override remote decoder
- `src/crc.c`: CRC-16/CCITT-FALSE helper
- `src/chacha20.c`, `src/poly1305.c`, `src/crypto_aead.c`, `src/blake2b.c`, `src/argon2.c`, `src/crypto_kdf.c`, `src/envelope.c`: the in-repo cryptographic stack
- `include/hvac.h`: board-level pin, provisioning, and implant configuration
- `include/implant.h`, `include/control.h`, `include/hvac_auth.h`, `include/baffle.h`: implant, command, authorization, and baffle interfaces
- `include/field_secrets.h`: lab-only committed key material
- `include/packet_artifact.h`: generated packet artifact header
- `test/test_hvac_node_and_security.c`, `test/test_peripheral_and_crypto.c`: comprehensive test suites
- `test/mock/`: Pico SDK hardware mocks plus the implant CoreDebug and reserved-flash host mock
- `test/harness/`: minimal in-repo test harness (strictly C-standard compliant)
- `scripts/gateway.py`: BMS gateway with radio provisioning, authentication, CSV logging, and sealed command replies
- `scripts/spoof.py`: forged and replayed command injection client
- `scripts/sim_edge.py`: laptop HVAC node simulator
- `scripts/field_crypto.py`: pure-Python interoperable crypto
- `scripts/gen_packet.py` / `scripts/packet_artifact.json`: packet artifact generator and source
- `scripts/run_tests.py`, `scripts/check_coverage.py`: test runner and coverage report
- `scripts/audit_c_standard.py`, `scripts/audit_python_standard.py`: code-standard auditors
- `scripts/gen_banner.py`: banner generator
- `paper.typ` / `paper.pdf`: classroom paper describing the protocol, the persistence, and the exercise
- `.github/workflows/release.yml`: tag-driven UF2 release workflow

<br>

## Glossary

- **AEAD**: authenticated encryption with associated data; one operation for
  secrecy and integrity.
- **Anti-debug**: a check that detects an attached debugger and changes behavior.
  Here it reads CoreDebug `DHCSR` at `0xE000EDF0` (bits `C_DEBUGEN` and
  `C_HALT`).
- **Anti-replay window**: a monotonic sequence rule that rejects a valid frame
  that has already been used.
- **Argon2id**: the memory-hard password hash (RFC 9106) used to derive the field
  key.
- **AT command**: a short ASCII command (`AT+...`) understood by the radio.
- **Baffle**: the servo-driven air damper this node actuates.
- **Beacon**: a periodic covert transmission, here the `DE AD BE EF` frame the
  implant emits every 8 ticks.
- **CRC**: cyclic redundancy check, a checksum for detecting corruption.
- **Declared length**: the byte count the sender claims for a payload; the
  receiver slices exactly that many characters.
- **DHT11**: a low-cost temperature/humidity sensor using a custom one-wire
  protocol, used here as the room climate sensor.
- **Fail safe**: a fault drives the baffle closed and restores the safe setpoint,
  the safe plant state.
- **Field key**: the key that seals frames on the wire and computes the state
  tag.
- **HD44780**: the character-LCD controller inside a 1602 module.
- **I2C**: a two-wire bus (SDA/SCL) used here for the LCD backpack.
- **Implant**: code that runs on the device but is not part of its intended
  function. Here the SANDBOX_ONLY FROSTLINE module.
- **Logic bomb**: a payload that arms on a trigger and acts later. Here it arms on
  the 8-byte magic `FROSTLNE` and closes the baffle 3 ticks later, and it re-arms
  from the reserved sector on every boot.
- **LoRa**: a long-range, low-power sub-GHz radio modulation.
- **NEC**: the infrared remote encoding the VS1838B decodes.
- **PCF8574**: an I2C I/O expander that drives the LCD's parallel interface.
- **Persistence**: surviving a removal attempt. The implant keeps its state in
  the reserved sector `0x103FF000` and re-installs on boot, so a firmware reflash
  alone does not remove it.
- **Rootkit**: code that hides its own activity from the operator. Here the
  implant masks its beacon from the LCD and the log while the beacon transmits.
- **RSSI / SNR**: received signal strength and signal-to-noise ratio reported
  with each `+RCV` frame.
- **SANDBOX_ONLY**: the build guard that compiles the benign implant. The clean
  firmware does not define it.
- **State tag**: a keyed tag over the authorization record that detects a
  tampered verdict.
- **UART**: a serial port used to talk to the radio.
- **XChaCha20-Poly1305**: the AEAD used for every sealed frame, with a 192-bit
  nonce and a 128-bit tag.

<br>

## Further Reading

- Act I, the sensor and telemetry chapter:
  https://github.com/mytechnotalent/cold-chain-monitor
- Act II, the access gate chapter:
  https://github.com/mytechnotalent/access-gate
- Act III, the pipeline valve chapter:
  https://github.com/mytechnotalent/pipeline-valve-controller
- The companion CTF for this act:
  https://github.com/mytechnotalent/CTF_hvac-automation-node
- Embedded Hacking course and breadboard:
  https://github.com/mytechnotalent/Embedded-Hacking
- Reverse Engineering self-study course:
  https://github.com/mytechnotalent/Reverse-Engineering
- `paper.typ` / `paper.pdf`: the classroom paper for this project.
- DHT11 datasheet, RYLR998 AT command reference, SG90 datasheet, and VS1838B
  datasheet (module vendors).

<br>

# Next
[OPERATION IRON LUNG CTF](https://github.com/mytechnotalent/CTF_hvac-automation-node)

<br>

# License
[MIT License](https://github.com/mytechnotalent/hvac-automation-node/blob/main/LICENSE)
