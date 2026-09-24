# OPERATION IRON LUNG - Nation-State Accuracy Review

**An adversarial, evidence-based audit of the entire project where every claim is
verified by a re-runnable command or explicitly labelled as a limitation.**

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

## 1. Scope and Method

This review treats the project as hostile-to-itself. Every module, constant, test
vector, document, and artifact is independently checked. The method is:

1. **Re-run every gate** (`audit_c_standard`, `audit_python_standard`,
   `run_tests`, `check_coverage`) and record the exact output and exit codes.
2. **Re-verify every constant** against the generated artifact header and the
   JSON source of truth, by regenerating the header and diffing it.
3. **Re-verify every cryptographic claim** against a published standard vector,
   and separate vectors that run in the native C suite from vectors that run only
   in the Python suite.
4. **Audit the implant adversarially**, with a dedicated Persistence and Rootkit
   section: what it does, how it is detected, how it is bounded and removed, and
   what it does not prove.
5. **Re-verify every artifact** by SHA-256 and by the in-repo build guardrail,
   because this project ships no CTF firmware artifact.
6. **Read the documents adversarially** for overclaims, stale numbers, and
   omissions, then correct them in this review.

## 2. Gate Results (all re-run for this review)

| gate | command | observed result |
|---|---|---|
| C standard | `python3 scripts/audit_c_standard.py` | exit 0, no output, **0 violations** |
| Python standard | `python3 scripts/audit_python_standard.py` | exit 0, no output, **0 violations** |
| Native tests | `python3 scripts/run_tests.py` | **452 checks, 0 failures**, 139 test cases |
| Coverage | `python3 scripts/check_coverage.py` | exit 0, **100.00% line coverage**, 2040 owned lines |
| Python suites | `python3 -m unittest test.test_field_crypto test.test_hvac_node -v` | **17 tests, OK** |

The coverage gate passes on line coverage. It does not require 100% branch or
region coverage, and the raw report is not 100% there: regions 99.23% and
branches 93.57%. That gap is real and is stated in the module table below.

## 3. Module-by-Module Audit

Owned lines are the instrumented statement lines reported by `llvm-cov report`
through `check_coverage.py`. Raw `wc -l` over `src/*.c` includes comments and
blank lines; `main.c` is excluded from coverage by design. Every owned module is
at 100.00% line coverage.

| module | role | owned lines | line coverage | verification performed | honest limitation |
|---|---|---|---|---|---|
| `crc.c` | CRC-16/CCITT-FALSE diagnostic | 20 | 100.00% | `test_crc16_ccitt` check value `0x29B1` | not on the wire; a checksum is not authentication |
| `sensor.c` | DHT11 room-climate-band classifier | 151 | 100.00% | waveform, all timeout shapes, CRC error, climate ok/reject/invalid | mock GPIO replays a recorded waveform, not real silicon |
| `display.c` | HD44780 over PCF8574 | 73 | 100.00% | `test_display_format_lines`, `test_display_render_lines` via recorded I2C | mock I2C, not real HD44780 bus timing |
| `radio.c` | RYLR998 provisioning, `AT+SEND`, `+RCV` parser | 187 | 100.00% | build/parse/reject/pump, oversize guards, `test_radio_spoofed_sender_attribution` | mock UART; the RF band is not simulated |
| `status_led.c` | red/yellow/green HVAC ALARM annunciator | 14 | 100.00% | `test_status_led_show` | none |
| `button.c` | manual override input | 35 | 100.00% | pressed/consume/debounce/reset | active-low input is exercised only through mocks |
| `servo.c` | 50 Hz baffle PWM | 26 | 100.00% | `test_servo_map`, `test_servo_init`, `test_servo_actuate` | mock PWM; no real servo or inrush load |
| `ir_remote.c` | VS1838B NEC override remote decode | 116 | 100.00% | decode valid/reject/bad leader/mark/ambiguous/address/command, `test_ir_poll_*` | optical path is unauthenticated; no anti-replay |
| `baffle.c` | baffle state machine and fail-safe policy | 40 | 100.00% | `test_baffle_init`, open/close travel, `test_baffle_fail_safe`, `test_baffle_reject_unauthorized` | bounded travel only; no real damper or airflow |
| `control.c` | sealed SETPOINT command path | 77 | 100.00% | `test_control_handle_success`, bad command, bad setpoint, bad tag, replay, short body, key guards | shared lab key; guard set is only SETPOINT |
| `hvac_auth.c` | anti-replay window and state tag | 74 | 100.00% | `test_hvac_auth_state_tag`, apply window/advance/bad tag, null, key, and tag guards | deterministic nonce from sequence; single key |
| `chacha20.c` | ChaCha20 and HChaCha20 | 99 | 100.00% | RFC 8439 block and stream vectors, HChaCha20 draft vector | none |
| `poly1305.c` | Poly1305 one-time authenticator | 169 | 100.00% | RFC 8439 tag vector, aligned path | none |
| `crypto_aead.c` | XChaCha20-Poly1305 seal/open | 38 | 100.00% | round-trip, tamper tag/ct/ad, constant-time `tag_equal` | built from the in-repo primitives, not an audited library |
| `blake2b.c` | BLAKE2b and Argon2 H' | 161 | 100.00% | `test_blake2b_abc`, multiblock, H' 32 and 256 vectors | none |
| `argon2.c` | Argon2id core (BLAMKA, hybrid addressing) | 336 | 100.00% | `test_argon2_lanes`, `test_argon2_type_i`, `test_argon2_clamp` branch coverage | the RFC 9106 KAT runs in Python, not in this C suite |
| `crypto_kdf.c` | Argon2id field key derivation | 28 | 100.00% | reject, empty password, determinism, salt sensitivity | classroom profile `t=3 p=1 m=64`; committed passphrase and salt |
| `envelope.c` | hex nonce/ciphertext/tag codec | 91 | 100.00% | nonce, round-trip, seal/open rejects, uppercase, known vector | none |
| `monitor.c` | HVAC BMS controller state machine | 238 | 100.00% | init, idle, render (including masked and unmasked beacon), climate, override remote open/close/clear/other, remote open/close/replay/bad tag/bad command/bad climate, override no-bypass, link loss, link unseen/within, guards | mocks are not the real silicon |
| `implant.c` | SANDBOX_ONLY FROSTLINE implant | 67 | 100.00% | first run, re-install on boot, persistence present, debug attached, handle command, beacon, anti-debug, logic bomb, persist once, rootkit guards | benign educational implant; build-guarded and breadboard-bound |
| `main.c` | entry point | n/a | excluded | build only | excluded from coverage by design |

**Total owned lines at 100.00% line coverage: 2040.**

Branch coverage below 100% in the same report: `display.c` 85.71%, `monitor.c`
85.33%, `control.c` 87.50%, `radio.c` 89.87%, `hvac_auth.c` 92.31%,
`envelope.c` 92.86%, `sensor.c` 94.74%, `implant.c` 95.83%, `ir_remote.c`
96.00%, `argon2.c` 97.56%.

## 4. Cryptographic Claim Verification

The native suite asserts the following published vectors. Each name below appears
as a passing case in the `run_tests.py` output for this review.

| claim | standard | vector | observed |
|---|---|---|---|
| ChaCha20 block function | RFC 8439 section 2.3.2 | key 00..1f, nonce 000000090000004a00000000 | `test_chacha20_block` PASS |
| ChaCha20 stream cipher | RFC 8439 section 2.4.2 | "Ladies and Gentlemen..." 114-byte ciphertext | `test_chacha20_stream` PASS |
| HChaCha20 subkey | XChaCha20 draft (irtf-cfrg-xchacha) | published subkey vector | `test_hchacha20` PASS |
| Poly1305 tag | RFC 8439 section 2.5.2 | "Cryptographic Forum Research Group" tag `a8061dc1305136c6c22b8baf0c0127a9` | `test_poly1305`, `test_poly1305_aligned` PASS |
| BLAKE2b-512 | BLAKE2 reference | digest of "abc", multiblock, long-input | `test_blake2b_abc`, `test_blake2b_multiblock` PASS |
| Argon2 variable-length hash H' | RFC 9106 section 3.3 | H' of {1,2,3,4} at 32 and 256 bytes | `test_blake2b_long_short`, `test_blake2b_long` PASS |
| Argon2id known-answer | RFC 9106 section 5.3 | `0d640df58d78766c08c037a34a8b53c9d01ef0452d75b65eb52520e96b01e659` | `test.test_field_crypto.TestFieldCrypto.test_rfc9106_argon2id_vector` PASS (Python suite) |
| Envelope layout | project vector | known nonce, node id 7, fixed body | `test_envelope_known_vector` PASS |
| Firmware and Python interop | project vector | shared field key and envelope | `test_field_key_matches_firmware`, `test_envelope_matches_firmware` PASS (Python suite) |

The RFC 9106 Argon2id known-answer test is a Python `unittest` in
`test/test_field_crypto.py`; it is not part of the 452 native checks. Running the
Python suites directly confirms all 17 tests pass, including the KAT and the
firmware-interop vectors.

### 4.1 Sealed command path, guarded set, and safe band

`src/control.c` opens the envelope under the field key with the HVAC node id as
associated data, then `control_parse` rejects the body unless
`pt[4] == HVAC_COMMAND_SETPOINT` and the decoded setpoint lies between
`HVAC_SETPOINT_MIN_TENTHS` (50) and `HVAC_SETPOINT_MAX_TENTHS` (350). The guarded
set is therefore exactly `HVAC_COMMAND_SETPOINT` (`0x01`), and the accepted
setpoint band is exactly 5.0 C to 35.0 C. The behavior is asserted by
`test_control_handle_success`, `test_control_bad_command`,
`test_control_bad_setpoint`, `test_control_bad_tag`, `test_control_short_body`,
`test_control_replay`, `test_control_authorize`, and `test_control_key_guards`.
All pass in this review.

### 4.2 Anti-replay sequence window

`hvac_auth_apply` in `src/hvac_auth.c` accepts a command only when
`seq > auth->last_seq`, then verifies the keyed tag, then advances the floor. The
behavior is asserted by `test_hvac_auth_apply_window` (accept once, reject the
same sequence, reject an older sequence), `test_hvac_auth_apply_advance` (a
newer sequence advances `last_seq`), `test_hvac_auth_bad_tag`, and the
end-to-end `test_monitor_remote_replay`. All pass in this review.

Honest limitation: `last_seq` is plain SRAM and resets to zero on every boot, so
a command captured before a reboot can be replayed after one. The paper's Threat
Model states this; the README does not. A production controller would persist the
floor in non-volatile memory.

### 4.3 Authenticated state tag

`hvac_auth_state_tag` seals a nine-byte record (`granted`, `seq[4]`,
`last_seq[4]`) under the field key with a nonce built from the sequence and the
domain byte `0xA7`; `hvac_auth_state_ok` recomputes and compares in constant time
(`crypto_aead_tag_equal` is a branchless XOR accumulator).
`test_hvac_auth_state_tag` proves a modified record fails, and the monitor paths
prove the end-to-end denial before the baffle moves.

Honest limitations: the lab derives the field key and the state-tag key from one
committed secret, so a compromised device can compute tags the gateway accepts;
the tag protects against casual tamper and a debugger that flips `granted`, not a
physical attacker who can read the key out of SRAM and recompute the tag; there
is no per-device key or rotation; and the nonce is deterministic in the sequence,
so two distinct records that ever share a sequence would violate AEAD nonce
uniqueness.

## 5. Persistence and Rootkit Audit

Act IV is the persistence act, so the implant gets its own dedicated audit. The
review asks six questions: what it does, how the persistence works, how the
rootkit hides, how it is detected, how it is bounded and removed, and what it
does not prove.

### 5.1 What it does

`src/implant.c` is compiled only under `SANDBOX_ONLY`. The clean firmware build
does not define the guard, so the shipping image has no implant. In the
`SANDBOX_ONLY` build:

- **Reserved-sector persistence.** `implant_init` reads the marker byte at
  `HVAC_IMPLANT_RESERVE_ADDR` (`0x103FF000`). On the first run the marker is
  absent, so `implant_persist` programs `0xC7` (`IMPLANT_MARKER_BYTE`) exactly
  once and returns. The write goes through the RP2350 flash API, not a memory
  mapped store: `implant_flash_write` masks interrupts with
  `save_and_disable_interrupts`, calls `flash_range_erase` on the reserved
  sector offset `HVAC_IMPLANT_RESERVE_OFFSET` (`0x3FF000`), and calls
  `flash_range_program` with a page whose first byte is the marker.
- **Re-install on boot.** On every later boot the marker is present, so
  `implant_init` calls `implant_arm` and the logic bomb is live again without any
  firmware change. A reflash of the program region does not touch the reserved
  sector.
- **Beacon.** Every `IMPLANT_BEACON_INTERVAL_TICKS` (8) ticks, `implant_beacon`
  emits an 8-byte frame to the local classroom hub: the magic `DE AD BE EF`
  (`IMPLANT_BEACON_MAGIC`) plus the low and high bytes of the tick counter, the
  marker byte `0xC7`, and a byte reporting whether a probe is attached.
- **Logic bomb.** `implant_handle_command` arms the bomb only on the exact
  8-byte magic `FROSTLNE` (`IMPLANT_ARM_MAGIC`). It records the trigger tick as
  the current tick plus `IMPLANT_TRIGGER_DELAY_TICKS` (3). At the trigger,
  `implant_detonate` calls the baffle close path directly, clears the armed
  latch, and writes the persistence marker.
- **Rootkit.** `implant_rootkit_active` is true when the reserved-sector marker
  is present and no probe is attached. While it is active, `monitor_beacon_text`
  renders `--` instead of `UP`, and `monitor_log` suppresses the `BCN` line,
  while `implant_beacon` still transmits.
- **Anti-debug.** `implant_debug_attached` reads CoreDebug `DHCSR` at
  `0xE000EDF0` (`HVAC_IMPLANT_DHCSR_ADDR`). Bit 0 is `C_DEBUGEN` and bit 1 is
  `C_HALT`. When either is set, `implant_tick` returns early and suppresses the
  beacon and the bomb.

Every one of these behaviors is asserted by a native test:
`test_implant_init_first_run`, `test_implant_reinstall_on_boot`,
`test_implant_persistence_present`, `test_implant_debug_attached`,
`test_implant_handle_command`, `test_implant_beacon`, `test_implant_anti_debug`,
`test_implant_logic_bomb`, `test_implant_persist_once`, and
`test_implant_rootkit_guards`. The Python adapter additionally asserts the
end-to-end logic bomb with `test_09_implant_logic_bomb`. All pass in this review.

### 5.2 How the persistence works, and why the reflash fails

The persistence is deliberately narrow. The implant keeps exactly one byte of
state, the marker `0xC7`, in the final sector of external flash at
`0x103FF000`. It stores that byte with the hardware flash API
(`flash_range_erase` then `flash_range_program`), because a plain memory mapped
store does not program real NOR flash. A firmware reflash writes the program
region and leaves that sector alone, so the marker survives. On the next boot, `implant_init` reads the marker
and re-arms the bomb. The persistence is therefore not a hidden second stage and
not a bootkit in the chip's boot ROM; it is state that the firmware image does
not own, plus a boot path that trusts it. That is enough to defeat a naive
remediation, which is the lesson, and it is also exactly why removal requires two
steps: erase the sector and remove the check that trusts it.

### 5.3 How the rootkit hides

The rootkit is a visibility control, not a stealthy loader. It does not hide the
code or the sector; it hides the evidence in the operator's two channels. When
the marker is present and no probe is attached, the LCD beacon field becomes
`--` and the `BCN` maintenance line is suppressed, while the beacon still
transmits on the radio. A masked beacon and an absent beacon are therefore
visually identical to the operator. The moment a probe is attached, the anti-debug
branch returns early and the rootkit's own condition flips, so the masking
disappears and the `B:UP` / `BCN` evidence returns. That inversion is a teaching
feature: it shows that controlling what the defender observes is separable from
what the attacker does.

### 5.4 How it is detected and removed

- **By build comparison.** The clean and `SANDBOX_ONLY` images differ by the
  implant translation unit and its symbols, which is the simplest and strongest
  detection: the payload is absent from the shipping build.
- **By reserved-sector inspection.** The marker at `0x103FF000` is state the
  firmware image does not own, and it is visible with the Debug Probe or
  `picotool`.
- **By beacon signature.** The periodic `DE AD BE EF` frame is a static and
  dynamic signature on the radio.
- **By display and log mismatch.** A silent `B:--` and no `BCN` line next to a
  live radio is the rootkit's fingerprint.
- **By static analysis.** The `FROSTLNE` arming magic, the baffle close call in
  `implant_detonate`, the `DHCSR` read address, and the reserved-sector address
  are all literal constants in the image.
- **By controlled observation.** Because the anti-debug branch is a single early
  return, a student can break after it under GDB and observe the beacon and the
  bomb resume, which proves the payload rather than merely suspecting it.
- **By removal.** The documented fix is two steps that must both happen: erase
  the reserved sector, and remove the re-install check and the `SANDBOX_ONLY`
  build flag so no future boot trusts the marker.

### 5.5 How it is bounded

The implant is bounded by construction and by test. It touches only its own
beacon radio frame, its arming latch, the servo close path, and the one reserved
sector. It has no network, no filesystem, and no host impact. It uses synthetic
data only. The `SANDBOX_ONLY` guard is the containment boundary, the reserved
sector is on the same chip and holds nothing else, and the native implant tests
assert both the behavior and its limits.

### 5.6 What it does not prove, and the honest limitation

The implant is a **benign educational implant**. It is confined to the
breadboard, guarded by `SANDBOX_ONLY`, and has no network. It actuates only the
student's own servo and writes only to a reserved sector on the same chip that
holds nothing else. It is a demonstration of technique, not tradecraft: it does
not encrypt itself, it does not load a second stage, it does not exfiltrate
anything, it does not persist across a deliberate sector erase, and it does not
resist physical forensics. Its persistence is persistence against a firmware
reflash, not persistence against a determined defender. Any claim that this
module is operationally representative would be an overclaim, and this review
records that plainly.

The deeper honest limitation is architectural: the implant bypasses the entire
sealed command path by calling the actuator directly, and it keeps state outside
the image. No amount of wire authentication fixes the first, and no amount of
protocol fixes the second. Mitigating them is a build-integrity, image-signing,
debug-lockdown, and state-erasure control, which is why the blue half names those
controls rather than pretending the protocol covers them.

## 6. Artifact Verification

This project ships no CTF firmware artifact (`build/` holds only untracked local
test binaries). The companion CTF is external:
`https://github.com/mytechnotalent/CTF_hvac-automation-node`, which ships the
compromised image with the implant and its verifier. What is verified in this
repository is the source tree and the provisioning artifact.

Source-tree aggregate SHA-256 over all 44 `.c` and `.h` files under `src/` and
`include/`, computed as `find src include ... | sort | xargs shasum -a 256 |
shasum -a 256`:

```
1041e29f8938343eb8ea4fbb19ec20245e11a25fd4e3b88c76146ea202df1bf2
```

Key artifacts by SHA-256:

```
paper.pdf                     0850dda24f247dc295bdd709b8c06ac08d38441880dcc6404176f8c51e7bf8bd
paper.typ                     0d6d111e17f70f9d7bef6f59a4b24b22bc494c1cd89249f6104b8d770d61e3f9
hvac-automation-node.png      334f993749e32f3edff3be62189f29c764f51fd512fa48de672deb6983ec72a0
scripts/packet_artifact.json  653619ccbf0a0825addb36634fce453fedac08690ee4bb82448635c429d46df4
include/packet_artifact.h     2287b0f28e5c03c2d42fe72cc0e500085ef3ac10cf0c5c9bf98d3d692ada980a
include/field_secrets.h       63cffbbeb9e4740c4031865d6bcf5bb8302ede090579eb4fbf0ef41764135d07
```

The build guardrail `check_packet_artifact_header` regenerates
`include/packet_artifact.h` from `scripts/packet_artifact.json` and fails if the
committed header is stale. Re-run for this review:

```
$ python3 scripts/gen_packet.py --from-json scripts/packet_artifact.json \
      --header-out /tmp/il_pa.h --check-header-path include/packet_artifact.h
Wrote generated firmware header: /tmp/il_pa.h
Verified header matches: include/packet_artifact.h
exit=0
```

Constants re-read from `include/packet_artifact.h` and matched to the README and
the pin map: `PACKET_NODE_ADDRESS` 7, `PACKET_HUB_ADDRESS` 0x0001,
`PACKET_FRAME_SIZE` 48, `PACKET_SETPOINT_WAIT_MS` 5000,
`PACKET_SERVO_CLOSE_PULSE_US` 500, `PACKET_SERVO_OPEN_PULSE_US` 1500,
`PACKET_DHT_TIMEOUT_US` 240, `PACKET_LCD_I2C_ADDRESS` 0x27,
`PACKET_MAX_RCV_LEN` 256, plus the shared pin map.

The banner is generated by `scripts/gen_banner.py` and is 1500 x 1500 pixels,
matching the other acts.

## 7. Adversarial Document Review

| document claim | audit verdict |
|---|---|
| README does not imply the device is unhackable | **accurate**; it states the protocol is sealed, that the implant survives a reflash, and that removal is not a single patch |
| README states the implant is benign, guarded, and confined | **accurate**; it appears in the narrative, the malware section, Lab 3, PARTS.md, and this review |
| README "The baffle is holding. That is exactly why you should be afraid" | **rhetorical framing**; the clean firmware is functional and implant-free, and the line refers to the `SANDBOX_ONLY` malware-track image, which the surrounding text makes clear |
| README gives the beacon, bomb, anti-debug, rootkit, and persistence details | **accurate**; the magic, interval, delay, `DHCSR` bits, marker, reserved address, and masking behavior all match the firmware |
| README states the clean build does not define `SANDBOX_ONLY` | **accurate**; the CMake option defaults to OFF and the module is guarded |
| README "The suite has **139 cases** and **452 checks**" | **accurate**; the native runner reports exactly 139 cases and 452 checks |
| README claims 100% line coverage of owned modules | **accurate**; the report is 2040 / 2040 lines |
| README does not mention per-device key rotation | **omission**; the paper's Threat Model is the only place that states the single-key limitation |
| README anti-replay section does not mention the reboot reset | **omission**; the paper states it, the README does not |
| README persistence section states the marker survives a reflash | **accurate**; it matches `implant_init` and is the central claim of the act |

Corrections: the README's anti-replay and key-model sections should carry the
same reboot-reset and no-per-device-rotation caveats the paper already carries.
No claim of unhackability was found.

## 8. Honest Limitations

- **Physical access wins.** A Debug Probe over SWD can read the field key from
  SRAM. The authenticated state tag detects a flipped verdict, but a probe that
  can read the key and recompute the tag defeats the design. Only OTP debug
  disable closes this.
- **Key extraction from flash.** `include/field_secrets.h` commits the passphrase
  and salt. Anyone holding the image holds the key. This is a lab convenience,
  not a deployment.
- **Single shared field key.** Both the wire key and the state-tag key derive
  from one committed secret, so a compromised device can compute tags the gateway
  accepts. There is no per-device key and no rotation in this build.
- **Replay after reboot.** `last_seq` resets to zero, so a command captured
  before a power cycle can be replayed after it. The floor is not persisted.
- **Classroom crypto profile.** Argon2id runs at `t=3 p=1 m=64` to fit SRAM; the
  state-tag nonce is deterministic in the sequence; both are teaching parameters,
  not hardening parameters.
- **The implant is inert, guarded, and breadth-limited.** It is benign,
  breadboard-bound, `SANDBOX_ONLY`-guarded, networkless, and confined to a
  reserved sector on the same chip. Its persistence is persistence against a
  firmware reflash, not against a deliberate sector erase or physical forensics.
  It demonstrates technique, not tradecraft.
- **The implant bypasses the protocol.** A module that calls the actuator
  directly is a build-integrity problem, not a wire-authentication problem.
  Signing and debug lockdown are named as the real controls.
- **Unauthenticated optical input.** Any NEC remote can send an override request.
  The optical surface is a documented exposure; the sealed radio path is the
  authorization path.
- **Manual override is a single input.** It is debounced and it never bypasses
  authorization, but it is one button; a failed button or a stuck line is a
  hardware reliability problem outside the firmware's control.
- **Supply chain and sensor trust are out of scope.** The DHT11 is checksummed,
  not authenticated, and the firmware is only as trustworthy as the toolchain and
  the parts.
- **Availability is not protected.** An attacker on the band can jam or flood the
  receiver.
- **Coverage is line coverage.** Branch coverage is not 100%, and the harness
  mocks are not the real silicon.

## 9. Conclusion

The project is internally consistent and candid: 20 owned modules, 2040
instrumented lines, 100.00% line coverage, 452 native checks passing with 0
failures, 139 native cases, and 17 passing Python tests, every cryptographic
primitive anchored to a published vector. The four gates all pass with exit 0.
Act IV adds real persistence and rootkit behavior over Acts I to III: reserved-
sector state that survives a firmware reflash, a re-install on every boot, a
rootkit that masks the beacon from the LCD and the log while the beacon still
transmits, and a two-step removal that erases the sector and removes the code
path. It keeps the sealed and guarded SETPOINT path, the strictly monotonic
anti-replay window, and the keyed tag over the authorization record, all
exercised end to end, and it adds an override that requests authorization instead
of bypassing it. Every implant behavior is asserted by a native test and every
limit is stated. The documentation is unusually honest about the shared key, the
open debug port, the inert implant, and the scope limit of a firmware image, with
minor omissions (reboot reset and no per-device rotation) that should be folded
into the README. The core lesson holds and is stated: the wire is sealed, the
verdict is tagged, the override cannot bypass, and the remaining risk is the key,
the probe, and the copy of the payload that the reflash never touched.

---

*This review is reproducible: run the five commands in section 2, the header
check in section 6, and the Python suites in section 4.*

This is Act IV of the ten-act OPERATION COLD IRON saga. See SAGA.md.
