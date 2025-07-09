# SummerAnalytics_Project
# Dynamic Parking Price Optimization using Pathway

This project implements a real-time dynamic pricing system for parking spaces using the **Pathway** stream processing engine and **Bokeh** for visualization.

We simulate and analyze data from a smart parking system to design two pricing models that respond to real-time demand and traffic conditions.

---Ddt

## Tech Stack Used

- **Python 3.10+**
- **Pathway** 
- **Bokeh** (visualization)
- **Panel** (optional for dashboard rendering)
- **Google Colab** (used for implementation and testing)
- **Pandas** (for pre-processing and exporting)
- **Matplotlib / Seaborn** (optional visual checks)

  
## Architecture Diagram
![architecture_diagram png](https://github.com/user-attachments/assets/2bb656f4-30cb-4dfe-ab9c-e4a0d037e889)

## Detailed Explanation of Project Architecture & Workflow

This project builds a real-time dynamic pricing system for parking lots using **Pathway**, a stream processing engine. It simulates incoming smart parking data and applies pricing models to compute optimal prices based on live occupancy, queue levels, traffic, and vehicle type. Prices are visualized using Bokeh for easy analysis.

---

###  1. Data Ingestion Layer

- The system begins with `dataset.csv`, which contains simulated smart parking data.
- Key columns include:
  - `Occupancy`, `Capacity` → real-time usage
  - `QueueLength`, `TrafficConditionNearby` → congestion indicators
  - `IsSpecialDay`, `VehicleType` → external demand signals
  - `LastUpdatedDate`, `LastUpdatedTime` → merged into a `Timestamp` column

- The processed data is saved into `Parking_stream.csv` for use in streaming.

---

###  2. Stream Simulation with Pathway

- Pathway’s `replay_csv()` function simulates a real-time data stream from the CSV file.
- A schema is defined (`ParkingSchema`) to validate column types.
- The `input_rate` controls how fast data is streamed (e.g., 1000 rows/second).
- This lets us test real-time logic within a static environment (like Colab).

---

###  3. Model 1: Linear Occupancy-Based Pricing

- A simple, interpretable pricing model:

  \[
  \text{Price} = \text{BasePrice} + \alpha \cdot \left(\frac{\text{Occupancy}}{\text{Capacity}}\right)
  \]

- Prices are clamped between 0.5× and 2× base price (e.g., $5–$20).
- This model increases price as lots get fuller.
- Output is written to `model1_output.jsonl`.

---

###  4. Model 2: Demand-Based Multi-Feature Pricing

- A more advanced pricing model that uses:

| Feature                | Description                             |
|------------------------|-----------------------------------------|
| Occupancy Ratio        | Primary driver of demand                |
| Queue Length           | Signals congestion                      |
| Traffic Condition      | Mapped as low = 0, medium = 1, high = 2 |
| Special Day Indicator  | Boosts price on holidays or events      |
| Vehicle Type Weighting | e.g., car = 1.0, bike = 0.5, truck = 2.0|

- A weighted demand score is computed and normalized:

  \[
  \text{Price} = \text{BasePrice} \cdot (1 + \lambda \cdot \text{NormalizedDemand})
  \]

- Output is written to `model2_output.jsonl`.

---

###  5. Visualization with Bokeh

- Price outputs from both models are visualized using **Bokeh**.
- The plots show how prices change over time (`t` vs `Price`).
- In Colab, static visualizations are generated using Pandas + Bokeh.
- In local setups, Panel + Pathway can provide dynamic streaming dashboards.

---

###  6. Workflow Summary

1. **Preprocessing**: Clean and merge timestamp data
2. **Streaming**: Simulate live feed with `replay_csv()`
3. **Model 1**: Apply occupancy-based formula
4. **Model 2**: Apply multi-feature demand-based formula
5. **Output**: Save results to `.jsonl` files
6. **Visualization**: Plot prices using Bokeh

---

###  Architecture Layers

- **Ingestion Layer**: Loads and formats parking data
- **Streaming Layer**: Feeds data row-by-row via Pathway
- **Processing Layer**:
  - Model 1 logic (linear)
  - Model 2 logic (demand-based)
- **Output Layer**:
  - Files: `model1_output.jsonl`, `model2_output.jsonl`
  - Plots: Static Bokeh visualizations
- **Interaction Layer** (optional): Panel/slide_window for dynamic dashboard (starter-style)

---

###  Why This Architecture Works

- Modular design with clean model separation
- Stream-simulated logic that works in Colab
- Bokeh-compatible output for quick pricing insights
- Can scale to more models (e.g., competition-aware pricing)

