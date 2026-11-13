# 911 Bench: Spec v0.1

An interactive explorer for a Porsche 911. It starts on the exterior, lets you move to the interior and the engine, and lets you zoom into any part to see what it does. It follows the concept of robotics-engineer.vercel.app, with original artwork and original copy.

## 1. Scenes

The app has three scenes, switched by a scene bar at the top: **Exterior**, **Interior**, **Engine**. A fourth, **Frunk** (front trunk), is optional for v1.

Every scene is a 3D view with a full 360-degree camera. The user can orbit around the exterior and engine, and look around from inside the cabin and the engine bay. Each scene has the same camera controls (section 2).

Note: on a 911 the engine sits behind the rear seats, and you reach it through the rear engine lid. The front trunk holds no engine. The Engine scene opens from the rear engine lid.

### Exterior (default view)
Side-profile illustration of the car. Hotspots:
- Headlights (LED), tail lights
- Rear engine lid and rear grille (opens to Engine scene)
- Front trunk lid (opens to Frunk, optional)
- Wheels and brake discs and calipers
- Exhaust tailpipes
- Side mirrors
- Fuel filler cap
- Roofline and body panels (generic, for orientation)

### Interior
Driver-side cockpit view. Hotspots:
- Steering wheel and horn
- Instrument cluster
- Central infotainment touchscreen
- Shift lever / PDK paddles
- Driver seat, passenger seat, and rear seats, each with seat belts
- Pedals (accelerator, brake, clutch on manual variants)
- Centre console and climate controls
- Glovebox

### Engine
A top-down or three-quarter view of the rear-mounted flat-six (boxer) engine. This scene is the core of the app. See section 3.

## 2. Interactions

- **360 view:** drag to orbit the camera a full 360 degrees around the car or engine. In the Interior, drag to look around the cabin. Scroll or pinch to dolly in and out.
- **Auto-rotate:** a toggle slowly spins the current view. It stops on any user input.
- **Snap views:** buttons jump to preset angles (front, side, rear, top, cabin, engine bay) and animate the camera there.
- **Hotspots follow the model:** hotspot markers stay attached to their 3D position, and they hide when they turn behind the car.
- **Hover:** a small tooltip shows the part name.
- **Click:** selects the part. The right-hand panel shows its name, function, and related parts.
- **Zoom:** scroll or pinch zooms into the selected part. A detail view shows a larger illustration and labelled sub-components where they exist.
- **Parts list:** in the Engine scene, a scrollable list shows every part. Clicking a list item selects it on the diagram, and selecting on the diagram scrolls the list to it. The list has a search box.
- **Reset:** a button returns to the default Exterior view and clears the selection.
- **Keyboard:** arrow keys cycle through hotspots in the current scene, Enter selects, Esc clears.

## 3. Engine parts (v1 list)

Reference model: 992-generation Carrera (air- and water-cooled details noted where they vary). Each part has a name, a one-to-two sentence function, and a related-parts list.

| # | Part | What it does |
|---|------|--------------|
| 1 | Crankcase | Splits into two halves that hold the crankshaft and link the cylinder banks. |
| 2 | Crankshaft | Converts the up-and-down motion of the pistons into rotation. |
| 3 | Connecting rods | Link each piston to the crankshaft. |
| 4 | Pistons | Move up and down in the cylinders, compressing the air-fuel mix and receiving the force of combustion. |
| 5 | Cylinder barrels | Guide the pistons and form the combustion chambers with the heads. |
| 6 | Cylinder heads | Seal the top of each cylinder and house the valves and spark plugs. |
| 7 | Camshafts | Open and close the valves at the right moment in each cycle. |
| 8 | Valves (intake and exhaust) | Let air-fuel mix in and exhaust gas out. |
| 9 | Variable valve timing actuators | Adjust camshaft timing for power across the rev range. |
| 10 | Timing chain | Keeps the crankshaft and camshafts in step. |
| 11 | Intake manifold | Distributes incoming air to each cylinder. |
| 12 | Throttle body | Controls how much air enters the engine. The driver's accelerator input sets it. |
| 13 | Fuel injectors | Spray fuel into the intake or cylinder at a precise time and amount. |
| 14 | Spark plugs | Ignite the air-fuel mix at the right moment. |
| 15 | Ignition coils | Step up battery voltage to the level needed for each spark. |
| 16 | Exhaust manifolds (headers) | Collect exhaust from each cylinder and send it to the exhaust system. |
| 17 | Oil pump | Circulates engine oil under pressure to bearings and moving parts. |
| 18 | Oil cooler | Cools engine oil through a heat exchanger. |
| 19 | Cooling fan and radiator (water-cooled models) | Remove heat from the coolant. Air-cooled earlier models use fins and a fan instead. |
| 20 | Engine control unit (ECU) | Reads sensors and sets fuel, spark, and valve timing in real time. |
| 21 | Sensors (mass airflow, lambda/oxygen) | Measure intake air and exhaust oxygen so the ECU can adjust the mix. |
| 22 | Turbochargers and intercoolers (Turbo variants only) | Force more air into the engine and cool it before it enters. |
| 23 | Engine mounts | Hold the engine to the chassis and absorb vibration. |

Each entry should carry a `variant` tag (`all`, `air-cooled`, `water-cooled`, `turbo`) so the UI can filter it.

## 4. Content model

Store all content as JSON, not in React code.

```
content/
  vehicle.json        # model name, generation, variant notes
  scenes/
    exterior.json     # svg asset path + hotspots
    interior.json
    engine.json
  parts/
    engine.json       # array of parts (section 3)
```

A hotspot:

```json
{
  "id": "rear-engine-lid",
  "label": "Rear engine lid",
  "scene": "exterior",
  "shape": { "type": "polygon", "points": [[x,y], ...] },
  "opens": "engine",
  "description": "...",
  "related": ["engine-mounts"]
}
```

A part:

```json
{
  "id": "crankshaft",
  "name": "Crankshaft",
  "function": "...",
  "variant": "all",
  "related": ["connecting-rods", "pistons"],
  "detail_svg": "engine/crankshaft.svg"
}
```

## 5. Architecture

- **Frontend:** React, Vite, TypeScript. Three.js through React Three Fiber, with Drei for camera controls and HTML hotspot labels. Zustand for selection and camera state.
- **3D assets:** one glTF model per scene (exterior, interior, engine), with named mesh nodes that match the part IDs. Models are compressed with Draco or meshopt to keep load times down.
- **Backend:** FastAPI with Pydantic models that mirror the JSON content. Endpoints:
  - `GET /api/vehicle`
  - `GET /api/scenes/{scene_id}`
  - `GET /api/parts?scene=engine&variant=all`
  - `GET /api/parts/{part_id}`
- The backend serves content, not state. No user accounts in v1.
- **Deploy:** Vercel for the frontend, Render or Fly for the backend.

## 6. Artwork and branding

- Every 3D model is original, built for this project, or licensed with a written permission that allows this use. Record the source and license of each model in `content/ASSETS.md`.
- Do not use Porsche logos, crests, official photos, or other Porsche-owned artwork.
- Use "Porsche 911" only as a descriptive name, not as a logo treatment. A disclaimer in the footer should state that the project is not affiliated with Porsche AG.

## 7. Milestones

1. **M0:** Build three stylized glTF models (exterior, interior, engine), with named nodes that match the part IDs. Keep polygon counts low, around 20k to 50k triangles per scene. Build them in Blender with a script so the models are reproducible, and record the build in `content/ASSETS.md`.
2. **M1:** Exterior scene with 360 orbit, snap views, hotspots, tooltips, and the side panel.
3. **M2:** Engine scene with the parts list, selection sync, search, and the Carrera/Turbo toggle.
4. **M3:** Zoom and detail views for the engine parts, using camera dolly into the selected part.
5. **M4:** Interior scene with look-around camera and all seats.
6. **M5:** Backend API, with the frontend reading from it instead of local JSON.
7. **M6:** Frunk scene (optional), polish, accessibility pass, deploy.

## 8. Decisions

- **Default generation:** 992 Carrera.
- **Turbo variant:** included in v1. Turbo-only parts (turbochargers, intercoolers) are tagged `turbo` and shown with a variant toggle in the Engine scene (Carrera / Turbo).
- **Interior:** all seats in v1. The Interior scene includes driver and passenger seats and the rear seats, with hotspots on each.
- **3D style:** stylized models, not photorealistic. Simplified, clean shapes with flat colours and soft materials, so the scenes stay fast to load and clearly original.
- **360 view in every scene:** required in v1. Exterior and Engine orbit around the object. Interior looks around from inside the cabin.
