<div align="center">

<img src="docs/_static/logo.png" alt="mariner 2 logo" width="140">

# mariner 2

**Remote control for MSLA resin 3D printers based on ChiTu controllers.**

Monitor, upload and print over WiFi, from any browser.

[![CI](https://github.com/mariner3d/mariner/workflows/CI/badge.svg)](https://github.com/mariner3d/mariner/actions/workflows/ci.yaml)
[![Documentation](https://readthedocs.org/projects/mariner/badge/?version=latest)](https://mariner.readthedocs.io/en/latest/?badge=latest)
[![codecov](https://codecov.io/gh/mariner3d/mariner/branch/master/graph/badge.svg)](https://codecov.io/gh/mariner3d/mariner)
[![Python](https://img.shields.io/badge/python-3.11%20%7C%203.12%20%7C%203.13-blue)](https://www.python.org/downloads/)
[![MIT license](https://img.shields.io/badge/License-MIT-blue.svg)](https://amd.mit-license.org/)

[Documentation](https://mariner.readthedocs.io/en/latest/) ·
[Installation](https://mariner.readthedocs.io/en/latest/install.html) ·
[Supported printers](https://mariner.readthedocs.io/en/latest/supported-printers.html) ·
[Troubleshooting](https://mariner.readthedocs.io/en/latest/troubleshooting.html)

<img src="docs/_static/screenshot.png" alt="Mariner 2 web interface" width="860">

</div>

## Features

- Web interface built for both desktop and mobile.
- Upload files to be printed through the web UI over WiFi.
- Remotely check print status: progress, current layer, time left.
- Remotely control the printer: start prints, pause/resume and stop.
- Browse the files available for printing.
- Inspect `.ctb`, `.cbddlp`, `.fdg` and `.photon` files: image preview, print
  time and slicing settings.
- Inspect encrypted `.ctb` files.
- Network printing: send files straight from your slicer. Mariner speaks
  SDCP v3.0.0, so ChiTuBox discovers and drives it as if the printer had
  network support built in, plus native support for UVTools transfers.

For more details on the feature set, refer to the
[documentation](https://mariner.readthedocs.io/en/latest/).

## Supported printers

Mariner 2 works with a wide range of MSLA printers, including models from:

| | | |
| --- | --- | --- |
| Anycubic | Creality | EPAX |
| Elegoo | Peopoly | Phrozen |
| Voxelab | | |

See the full list of [supported
printers](https://mariner.readthedocs.io/en/latest/supported-printers.html) for
the models that have been tested. If you have access to other printers and want
to contribute, please open an issue. We're happy to support more printers!

## Installation

Mariner 2 runs on a Raspberry Pi (or similar board) wired to the printer. The
[installation guide](https://mariner.readthedocs.io/en/latest/install.html)
covers the hardware setup, the USB gadget and the serial port end to end.

**Debian / Ubuntu / Raspberry Pi OS**

```shell
curl -fsSL https://packages.alejandro.md/setup.sh | sudo sh
sudo apt install mariner3d
```

**Fedora / RHEL**

```shell
curl -fsSL https://packages.alejandro.md/setup.sh | sudo sh
sudo dnf install mariner3d
```

**Docker**

```shell
docker run -d --name mariner --device /dev/ttyUSB0 -v /mnt/usb_share:/mnt/usb_share -p 5000:5000 amd989/mariner3d
```

The image is also published to GitHub Container Registry as
`ghcr.io/mariner3d/mariner`.

Once the package is installed, the helper script configures the boot partition,
USB gadget and serial port on Raspberry Pi OS and Armbian:

```shell
sudo mariner3d-setup-pi
```

## Development

```shell
poetry install          # install the Python dependencies
poetry run mariner      # run the backend on :5050
```

```shell
cd frontend/
npm install
npm run dev             # run the frontend on :3000, proxying /api to :5050
```

Tests, linting and type checking:

```shell
poetry run green && poetry run black . && poetry run flake8 && poetry run pyre check
```

See the [contributing
guide](https://mariner.readthedocs.io/en/latest/contributing.html) for the full
development workflow.

## Documentation

The documentation lives on [Read the
Docs](https://mariner.readthedocs.io/en/latest/) and covers hardware setup,
software installation, troubleshooting and contributing.

[This blog
post](https://l9o.dev/posts/controlling-an-elegoo-mars-pro-remotely/) explains
the setup end to end, with pictures of the modifications done to an Elegoo Mars
Pro.

## License

MIT. Mariner was started by [Luiz Ribeiro](https://github.com/luizribeiro) in
2020; Mariner 2 continues the project. See [LICENSE.txt](LICENSE.txt).
