# Semailles Dental display

This folder contains the custom apps and display setup for the practice's Ulanzi TC001.

The clock runs the standard AWTRIX NG firmware. The `.ax` files in this folder are the
custom apps installed through its web interface. Their `@config` headers define the
settings available under **Apps → gear**. [setup.json](setup.json) holds the timezone,
clock appearance and rotation. Geneva uses `Europe/Zurich`, including summer time.

The patient count is saved on the clock. Installing an updated script preserves it.
The right button adds and the left button subtracts while the patient counter is shown.
The counter does not reset automatically.

Outdoor weather comes from [MET Norway](https://api.met.no/weatherapi/locationforecast/2.0/documentation).
The weather icon follows the forecast condition and its day/night variant. The app
draws its own icons, so it needs no icon downloads. MET Norway data is used under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Install on a clock

1. Install AWTRIX NG and connect the clock to Wi-Fi using the
   [upstream installation guide](../docs/getting-started/flashing.md).
2. Open the clock's web interface and go to **Scripts**.
3. Create a script for each `.ax` file. Use the filename without `.ax` as its name,
   paste the complete file, and save. The error field must be empty.
4. Apply the display setup below. This requires `curl` and `jq` on your computer.
5. Use **Apps → gear** to change each app's settings. The practice name, website,
   weather location, colours and per-app display times are configurable there.

### Apply the display setup

Run these commands from the repository root. Replace `YOUR-CLOCK-IP` with the address
shown by the clock. If you enabled a device login, add `-u username` to each `curl`
command and enter the password when prompted.

```bash
AWTRIX_URL=http://YOUR-CLOCK-IP

jq '.system' Berry/setup.json | curl --fail-with-body -X PUT \
  "$AWTRIX_URL/api/v1/system" -H 'Content-Type: application/json' --data-binary @-

jq '.settings' Berry/setup.json | curl --fail-with-body -X PATCH \
  "$AWTRIX_URL/api/v1/settings" -H 'Content-Type: application/json' --data-binary @-

jq '.apps' Berry/setup.json | curl --fail-with-body -X PUT \
  "$AWTRIX_URL/api/v1/apps/order" -H 'Content-Type: application/json' --data-binary @-
```

The setup is saved on the clock. It does not contain Wi-Fi credentials, device login
details, the live patient count or firmware backups. Before replacing a clock, use
the web interface's backup feature to preserve its current data.
