# Installing Ardupilot and MAVProxy

### Video tutorial at https://youtu.be/wlkoq65mM2A

## Clone ArduPilot

In home directory:
```
cd ~
sudo apt install git
git clone https://github.com/ArduPilot/ardupilot.git
cd ardupilot
git checkout Copter-3.6
git submodule update --init --recursive
```

## Install dependencies:
```
sudo apt install python-matplotlib python-serial python-wxgtk3.0 python-wxtools python-lxml python-scipy python-opencv ccache gawk python-pip python-pexpect
```

## Use pip (Python package installer) to install mavproxy:
```
sudo pip install future pymavlink MAVProxy
```

MAVProxy is a fully-functioning GCS for UAV’s. The intent is for a minimalist, portable and extendable GCS for any UAV supporting the MAVLink protocol (such as one using ArduPilot). For more information check out http://ardupilot.github.io/MAVProxy/html/index.html

Open `~/.bashrc` for editing:
```
gedit ~/.bashrc
```

Add these lines to end of `~/.bashrc` (the file open in the text editor):
```
export PATH=$PATH:$HOME/ardupilot/Tools/autotest
export PATH=/usr/lib/ccache:$PATH
```

Save and close the text editor.

Reload `~/.bashrc`:
```
. ~/.bashrc
```

Run SITL (Software In The Loop) once to set params:
```
cd ~/ardupilot/ArduCopter
sim_vehicle.py -w
```

################################################################################################################
below is the working docs 

Perfect! I can create a **complete `.md` tutorial** for setting up ArduPilot SITL and MAVProxy on Ubuntu (or WSL). I’ll combine the official info you gave me, along with the working commands for SITL we discussed, step by step, ready to copy-paste into a markdown file.

Here’s a full **`ArduPilot_SITL_Setup.md`**:

````markdown
# ArduPilot SITL Setup on Ubuntu / WSL

This guide explains how to set up **ArduPilot SITL** (Software In The Loop) with **MAVProxy** on Ubuntu or WSL, including all dependencies and working commands.

---

## 1. Prerequisites

Update your system:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git build-essential ccache gawk python3-dev python3-pip python3-opencv python3-wxgtk4.0 python3-matplotlib python3-lxml python3-scipy python3-serial python3-pexpect python3-pygame
````

Make sure Python 3 and pip are installed:

```bash
python3 --version
python3 -m pip --version
```

---

## 2. Clone ArduPilot Repository

```bash
cd ~
git clone https://github.com/ArduPilot/ardupilot.git
cd ardupilot
git submodule update --init --recursive
```

---

## 3. Setup Python Virtual Environment (optional but recommended)

```bash
python3 -m venv venv-ardupilot
source venv-ardupilot/bin/activate
python3 -m pip install --upgrade pip
```

---

## 4. Install MAVProxy

Install MAVProxy inside your environment:

```bash
python3 -m pip install --user --upgrade mavproxy pymavlink PyYAML
echo 'export PATH="$PATH:$HOME/.local/bin"' >> ~/.bashrc
source ~/.bashrc
```

> If you encounter **permission denied** errors when accessing serial devices:

```bash
sudo usermod -a -G dialout $USER
# Then restart your system
```

---

## 5. Build ArduPilot SITL

1. Go to the ArduPilot root directory:

```bash
cd ~/ardupilot
```

2. Configure the build for SITL:

```bash
./waf configure --board sitl
```

3. Clean any previous build (optional but recommended):

```bash
./waf clean
```

4. Build the code:

```bash
./waf build
```

* If you see errors about MAVLink, run:

```bash
git fetch --all
git reset --hard origin/master
git submodule update --init --recursive
./waf clean
./waf configure --board sitl
./waf build
```

---

## 6. Running SITL

### 6.1 ArduCopter Example

```bash
cd ~/ardupilot/ArduCopter
../Tools/autotest/sim_vehicle.py -v ArduCopter -f quad --console --map
```

* `-v ArduCopter` → vehicle type
* `-f quad` → frame type (`quad`, `hexa`, etc.)
* `--console` → opens MAVProxy console
* `--map` → opens map GUI

> Other vehicles: `ArduPlane`, `APMrover2`, `ArduSub`, `AntennaTracker`

---

### 6.2 Notes

* Ignore **SyntaxWarning: invalid escape sequence '\s'** — harmless.
* Always run `sim_vehicle.py` from the **vehicle directory**.
* Use `clear` in Ubuntu to clear the terminal (not `cls`).

---

## 7. MAVProxy Quickstart

Once SITL is running, MAVProxy commands can be used:

```text
mode GUIDED
arm throttle
takeoff 10
```

* You can forward telemetry to other clients or connect multiple vehicles.

For full MAVProxy documentation: [MAVProxy Official Docs](http://firmware.ardupilot.org/Tools/MAVProxy/)

---

## 8. Updating ArduPilot or MAVProxy

* Update ArduPilot:

```bash
cd ~/ardupilot
git fetch --all
git reset --hard origin/master
git submodule update --init --recursive
./waf clean
./waf configure --board sitl
./waf build
```

* Update MAVProxy:

```bash
python3 -m pip install --user --upgrade mavproxy pymavlink
# Or latest development version:
python3 -m pip install --user git+https://github.com/ArduPilot/mavproxy.git@master
```

---

## 9. Troubleshooting

| Issue                                         | Solution                                                                  |           |           |         |                                                 |
| --------------------------------------------- | ------------------------------------------------------------------------- | --------- | --------- | ------- | ----------------------------------------------- |
| `sim_vehicle.py` complains about vehicle type | Always use `-v ArduCopter                                                 | ArduPlane | APMrover2 | ArduSub | AntennaTracker` and run from the vehicle folder |
| `permission denied` on serial                 | Add user to dialout group and restart: `sudo usermod -a -G dialout $USER` |           |           |         |                                                 |
| MAVLink errors                                | Update submodules: `git submodule update --init --recursive`              |           |           |         |                                                 |

---

## 10. References

* [ArduPilot SITL Guide](https://ardupilot.org/dev/docs/sitl-simulator-software-in-the-loop.html)
* [MAVProxy Docs](http://firmware.ardupilot.org/Tools/MAVProxy/)
* [Official ArduPilot GitHub](https://github.com/ArduPilot/ardupilot)

---

> ✅ This setup works on Ubuntu 20.04+, WSL2, and Debian-based Linux systems.


