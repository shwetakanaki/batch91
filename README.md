# Talking Robot Head – Raspberry Pi 5

## What you need
- Raspberry Pi 5 with Raspberry Pi OS (Bookworm)
- USB microphone (or a USB webcam that has a mic)
- A speaker (USB, 3.5 mm via a USB sound card, or Bluetooth). The Pi 5 has **no 3.5 mm jack**.
- Optional: 2 × SG90/MG90S servos for the mouth and neck, with a **separate 5 V supply** (don't power servos from the Pi)

## Install
```bash
cd robot_head
chmod +x setup.sh
./setup.sh
```

## Run
| Command | What it does |
|---|---|
| `python3 robot_head.py --text` | Type commands to test (no mic needed) |
| `python3 robot_head.py` | Voice mode. Say **"Robo, what time is it"** |
| `python3 robot_head.py --no-wake` | Responds to everything, no wake word |
| `python3 robot_head.py --servos` | Also moves the mouth and neck servos |

You can also say just **"Robo"**. It answers "Yes?" and then listens for 8 seconds.

## Commands it understands
hello · what is your name · how are you · what time is it · what is the date · tell me a joke ·
look left / right / straight · nod · shake your head · help · thank you · goodbye · shut down

## Add your own command
In `robot_head.py`, inside `Brain.__init__`, add a line to `self.commands`:
```python
(["dance"], self.dance),
```
Then add the method:
```python
def dance(self):
    self.servos.shake()
    return ("Watch my moves!", False)
```
Return `True` instead of `False` if the robot should stop after replying.

## Servo wiring (BCM pins)
| Servo | Signal | Power | Ground |
|---|---|---|---|
| Mouth | GPIO 18 (pin 12) | external 5 V | external GND **+ Pi GND (pin 6)** |
| Neck  | GPIO 13 (pin 33) | external 5 V | external GND **+ Pi GND** |

## Better voice (optional)
Install [Piper](https://github.com/rhasspy/piper) and put `en_US-lessac-medium.onnx` and its `.json` file in a `voices/` folder. The script switches to Piper automatically.

## Start automatically at boot
```bash
crontab -e
# add this line (change the path to yours):
@reboot sleep 20 && cd /home/pi/robot_head && python3 robot_head.py --servos >> robot.log 2>&1
```

## Troubleshooting
- **No sound:** run `aplay -l` to list speakers, and pick the output from the speaker icon on the desktop.
- **Doesn't hear you:** run `arecord -l` to check the mic, then `arecord -d 3 test.wav && aplay test.wav`.
- **Mishears words:** try the Indian-English Vosk model (link in `setup.sh`) and speak close to the mic.
