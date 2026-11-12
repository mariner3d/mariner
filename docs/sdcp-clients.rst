Printing over SDCP
==================

Mariner speaks **SDCP v3.0.0**, the protocol ChiTu network mainboards use, so
slicers and printer managers that support it treat Mariner as if the printer
had network support built in. You slice, pick the printer, and the file is on
the machine: no USB stick, no web interface.

This page walks through the clients that have been tested, with screenshots of
each step. The protocol details live in :ref:`Network Printing`.

.. contents:: On this page
   :local:
   :depth: 2


Before you start
----------------

* Mariner is running and reachable from your desktop. The SDCP provider is
  enabled by default; nothing needs to be turned on.
* The computer running the slicer and the machine running Mariner are on the
  same subnet, and the network allows UDP broadcast. Discovery sends an
  ``M99999`` broadcast on port 3000, so guest networks, client isolation and
  separate VLANs will hide the printer.
* Ports 3000 (discovery) and 3030 (control and uploads) are free. They are
  fixed by the protocol. If either is taken, Mariner logs an error at startup
  and carries on serving the web interface without SDCP.

Clients show the printer under the name Mariner reports, which defaults to the
printer's display name. Brand, model, resolution and build volume come from
``config.toml``. With nothing configured and no sliced file to measure, a
client shows empty or zero values:

.. figure:: /_static/sdcp/chitumanager-details-unconfigured.png
   :alt: Printer details with an empty brand and a 0x0 exposure screen
   :align: center

   Nothing pinned in ``config.toml`` yet, so the brand is blank and the screen
   reports 0x0.

Set them once and every client shows the right thing:

.. code-block:: toml

   [sdcp]
   brand_name = "ELEGOO"
   machine_name = "Mars 3"
   resolution = "4098x2560"
   xyz_size = "143.43x89.6x175"

See :ref:`Network Printing` for the full list of SDCP settings.


ChiTuBox
--------

ChiTuBox talks to printers through **ChituManager**, which is installed
alongside the slicer. You add Mariner there once, then send from the slicer.

Adding the printer
~~~~~~~~~~~~~~~~~~

1. Open ChituManager and select **Manage**. Mariner answers the LAN scan and
   appears under **Add Printer** with its name and IP address.

   .. figure:: /_static/sdcp/chitumanager-discover.png
      :alt: ChituManager listing the discovered printer
      :align: center

2. Click **Add Printer**. The printer moves to **Added Printer** on the right.

   .. figure:: /_static/sdcp/chitumanager-added.png
      :alt: Confirmation that the printer was added
      :align: center

3. Click the card to inspect what Mariner reports: protocol version, firmware,
   the mainboard ID as the serial number, the exposure screen resolution and
   the build volume.

   .. figure:: /_static/sdcp/chitumanager-details.png
      :alt: Printer details panel showing protocol V3.0.0 and the build volume
      :align: center

   The serial number is Mariner's mainboard ID, derived from the host machine
   ID so it stays the same across restarts. That is what lets the client
   remember the printer.

Sending a file
~~~~~~~~~~~~~~

1. Slice as usual, then open **Network Sending**. Pick the printer and click
   **Send**.

   .. figure:: /_static/sdcp/chitubox-network-sending.png
      :alt: The Network Sending dialog with the printer selected
      :align: center

2. The transfer is chunked and verified against the MD5 the client sends. When
   it finishes the row shows **completed**, and a **Print** button appears.

   .. figure:: /_static/sdcp/chitubox-send-complete.png
      :alt: Transfer completed, with a Print button next to the printer
      :align: center

3. **Print** asks for confirmation before it starts the job.

   .. figure:: /_static/sdcp/chitubox-print-confirm.png
      :alt: Confirmation dialog before starting the print
      :align: center

   .. warning::
      Nothing on the printer checks that the plate is clean, levelled and that
      the vat has resin in it. Mariner starts whatever the client asks it to
      start, so make sure the machine is ready before confirming.

Watching the print
~~~~~~~~~~~~~~~~~~

The **Printer** tab shows progress while the job runs: percentage, current
layer against the total, elapsed and estimated time, and buttons to pause or
stop. All of it comes from the same serial link the web interface uses.

.. figure:: /_static/sdcp/chitumanager-printing.png
   :alt: ChituManager showing print progress, layer count and controls
   :align: center

While a print is running, the printer reports **Busy** to any client that tries
to send another file.

.. figure:: /_static/sdcp/chitubox-printer-busy.png
   :alt: The Network Sending dialog showing the printer as busy
   :align: center

Uploaded files land in the same directory the web interface uses, so they show
up in Mariner's file manager and on the printer's USB share:

.. figure:: /_static/sdcp/mariner-files-after-upload.png
   :alt: The uploaded file listed in Mariner's file manager
   :align: center


Lychee Slicer
-------------

1. Open the printer settings for your machine and click **Discover** next to
   **Ethernet/Wifi**.

   .. figure:: /_static/sdcp/lychee-discover.png
      :alt: Lychee printer settings with the network not configured
      :align: center

2. Lychee finds Mariner and fills in the name and IP address. The panel gains
   **Upload a file** and **Upload and print** buttons.

   .. figure:: /_static/sdcp/lychee-connected.png
      :alt: Lychee printer settings showing the printer connected
      :align: center

3. Slice, then use **Send wirelessly to <printer>** on the export panel.
   **Send** uploads the file; **Send and Print** uploads it and starts the job.

   .. figure:: /_static/sdcp/lychee-send.png
      :alt: The export panel with the send wirelessly menu open
      :align: center

4. Lychee confirms what it sent, along with the name the file was given on the
   printer.

   .. figure:: /_static/sdcp/lychee-sent.png
      :alt: Confirmation that the file was sent to the printer
      :align: center


DragonFruit Slicer
------------------

1. Slice the plate and export it as CTB.

   .. figure:: /_static/sdcp/dragonfruit-export.png
      :alt: The slicer export panel with CTB output selected
      :align: center

2. Sending the plate opens an **Upload to SDCP 3.0.0** step. Mariner receives
   the file in chunks and then reads its metadata, which is what the
   *Processing on device* stage is waiting for.

   .. figure:: /_static/sdcp/dragonfruit-upload.png
      :alt: Upload dialog showing the processing stage
      :align: center

3. Once the file is readable the stage becomes **Ready to print**.

   .. figure:: /_static/sdcp/dragonfruit-ready.png
      :alt: Upload dialog reporting the file is ready to print
      :align: center

4. If starting the print fails, the upload itself still succeeded: the file is
   on the machine and listed in Mariner's file manager. Start it from the web
   interface, or from the client's own file list.

   .. figure:: /_static/sdcp/dragonfruit-failed.png
      :alt: Upload complete but the start-print step failed
      :align: center

   Mariner starts a print from the ``Filename`` that command 128 carries. A
   client that sends the command without one, or with an identifier Mariner
   cannot map back to a file, gets a refusal rather than a print of the wrong
   file.


Troubleshooting SDCP
--------------------

**The printer never appears in the client.**
   Discovery is a UDP broadcast on port 3000. Check that both machines are on
   the same subnet, that the network does not isolate clients from each other,
   and that no firewall on the Mariner host drops UDP 3000 or TCP 3030. Check
   Mariner's log for a message about either port already being in use.

**The client shows the printer but the details are empty or zero.**
   Mariner cannot ask a ChiTu board over serial for its brand, screen
   resolution or build volume. It derives the resolution from a sliced file
   when it has one, and otherwise reports zeros. Pin ``brand_name``,
   ``machine_name``, ``resolution`` and ``xyz_size`` in ``config.toml``.

**The client shows a hardware warning.**
   Some clients render a warning triangle with a hardware fault, such as an
   axis or release-film message, when attributes read zero.

   .. figure:: /_static/sdcp/chitumanager-error.png
      :alt: A client showing a hardware warning next to an idle printer
      :align: center

   Mariner always reports ``ErrorNumber`` 0 (no error) and reports zero for the
   hardware it cannot read over the serial link: exposure screen state, release
   film state and UV LED temperature. The warning is the client's reading of
   those zeros, and prints run normally.

**The print always shows the same sub-status.**
   An active print reports ``EXPOSURING`` for its whole duration. The serial
   link has no notion of the lift and drop phases SDCP models, so layer
   progress is derived from the print file, exactly as the web interface does
   it.

**Print history is missing jobs.**
   History is built by watching the printer, and status polling only runs while
   a client is connected. A print started with no client connected is not
   recorded. History is also written into the cache directory by default, which
   is often cleared on reboot; point ``history_path`` somewhere durable to keep
   it.
