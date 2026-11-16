# Canteen Zero Waste

A prototype system that cuts food waste in college canteens. IoT devices measure what gets cooked, served and thrown away. An ML model uses that data to tell the kitchen how much to cook the next day. Any surplus goes to students at a discount or to a local NGO.

**Live demo:** open `index.html` in a browser, or see the GitHub Pages link in the repo description.
**Architecture diagram:** [`docs/architecture.html`](docs/architecture.html)

## The problem

College canteens cook a fixed amount every day, whether 300 students show up or 150. On Saturdays, during exam weeks and on rainy days, a lot of that food ends up in the bin. Students also leave food on their plates when portions are too big or a dish isn't good that day. Nobody measures any of this, so nothing changes.

## What the prototype does

The demo has four views:

| View | What it shows |
|---|---|
| Kitchen plan | Set tomorrow's conditions (day, exam week, campus event, rain). The model predicts demand for each dish and suggests how much to cook, compared with the usual fixed batch. |
| Live service | A simulated lunch service from 12:00 to 2:30 pm with counter scales, a smart bin with a camera, and a cold-storage sensor. It sends discount alerts near closing and an NGO pickup request at the end. Buttons let you trigger a bad batch or a fridge fault to see the anomaly alerts. |
| Student app | Students pre-book tomorrow's lunch, get surplus alerts, and rate dishes. Pre-bookings feed straight into the kitchen forecast. |
| Reports | Daily waste over 8 weeks, waste by dish (unsold vs. left on plates), and recommendations such as smaller batches or half portions. |

## How it works

**Input.** Smart bins weigh and photograph discarded food. Counter scales record servings, and storage sensors track temperature. The canteen logs what it cooked and sold. Students pre-book and rate meals. The system also uses the academic calendar and the weather forecast.

**Processing.** Edge devices (ESP32 or Raspberry Pi) send readings over MQTT to the cloud, where the data is cleaned and stored. A computer vision model identifies which dishes are being wasted. A forecasting model predicts next-day demand for each dish. Anomaly detection flags unusually high waste or unsafe storage temperatures.

**Output.** The kitchen sees how much to cook each morning. Students get discount alerts for leftover food, and an NGO is notified to collect whatever is still left. Weekly reports show which dishes are wasted most.

## The model

The demo trains a ridge regression for each dish, right in the browser. The features are day of week, exam week, campus event, rain and the number of pre-bookings. It's trained on weeks 1 to 4 and tested on weeks 5 to 8, which it hasn't seen. A 6% buffer is added so the counter rarely runs out.

In a full deployment this would be replaced by XGBoost or an LSTM trained on real canteen records, with YOLO handling food recognition from the bin camera.

## Planned tech stack

- **IoT:** load-cell smart bins, bin camera, counter scales, DHT22 temperature sensors, ESP32 / Raspberry Pi, MQTT
- **Cloud:** AWS IoT Core, FastAPI, PostgreSQL, object storage for images
- **AI/ML:** YOLO (food recognition), XGBoost / LSTM (demand forecasting), Isolation Forest (anomaly detection)
- **Apps:** Flutter student app, React kitchen dashboard, Firebase Cloud Messaging for alerts

## About the data

All sensor readings and the 8 weeks of canteen history in this demo are **simulated**. They come from a seeded random generator in `index.html`, so the numbers are the same on every load. The waste reduction figures show how the system would behave, not results measured in a real canteen.

## Running it

It's one HTML file with no build step and no dependencies. Open `index.html` in any browser.

To host it with GitHub Pages, go to **Settings → Pages**, set the source to the `main` branch and the root folder, and save. The demo will be live at `https://<your-username>.github.io/canteen-zero-waste/`.

## Environmental impact

- Less food in landfills, where it rots and gives off methane
- Saves the water, energy and fertilizer that went into the extra food
- Less LPG and electricity used for cooking smaller batches
- Surplus food reaches people through NGOs instead of being thrown away
- Fewer ingredients spoil in storage
