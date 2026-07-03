A simple helper app for single player tabletop roleplaying meant to run without an internet connection.

Use the BaseSettings.json and MapTemplate.json files as templates for your own versions.

# Field Companion

> **Solo Play Toolkit • Bookkeeping & Oracle, Not Arbitration**

Field Companion is a lightweight, browser-based digital toolkit designed for solo Tabletop Role-Playing Games (TTRPGs). It acts as an automated journal, procedural hex-crawler, and narrative generator.

Built entirely in a single file with native vanilla web technologies, it requires no internet connection, no installations, and no server backend.

---

## 🚀 Getting Started

Because Field Companion is a self-contained application, there is no setup process.

1. Download or clone `Field Companion.html` to your local machine.
2. Double-click the file to open it in any modern web browser (Chrome, Edge, Firefox, Safari).
3. (Optional) In the configuration menu, load your world's custom setting layout template `.json` or jump straight into the default setting.

---

## 🛠️ Core Features

* **Hex-Based Cartography:** Generates a flat-top axial grid (defaulting to *"The Hollow Reach"*) featuring an exploration "Fog of War" mechanic. Inspect, rename, and track custom notes on individual hexes.
* **Contextual Oracle Engine:** Draws prompts from localized databases to generate encounter threats based specifically on the terrain type you are currently traversing.
* **Campaign Dashboard:** Includes a scrolling log for narrative momentum, alongside automated dice configurations (`d4` through `d100`) that highlight critical hits and misses.
* **Tension & Complication Meter:** An SVG dial tracking shifting environmental danger. As tension ticks upward, pending complications seamlessly influence your travel events.
* **Campaign Bookkeeping:** Dedicated generators and rosters to instantly spin up NPCs (with traits and custom image refs) and procedural quest hooks.

---

## 💾 Data Management & Privacy

* **100% Local & Private:** Your campaign data never leaves your computer. No tracking, no cloud servers.
* **Save/Load System:** Progress is packaged and downloaded as a plain text `.json` file. To resume your adventure, simply upload your save file back into the companion interface.
* **Image Optimization:** Embedded portrait images are automatically downscaled and compressed client-side to keep your JSON save files lightweight and quick to download.

---

## 🗺️ Customizing Settings (For Creators)

Field Companion can adapt to entirely different TTRPG rulesets or world settings. You can construct a custom blueprint `.json` file defining your own:

* Terrain weights and names
* Contextual encounter tables
* NPC name pools, professions, and quirks
* Procedural quest verb/item tables

Load your custom setting via the application's configuration layout panel to completely reskin the generator databases.

---

## 📋 Road Map / Upcoming Features

* [ ] **Organizations & Nations Tab:** Dedicated roster tracking to support factions, political borders, and overarching regional powers.
* [ ] **Data Integrity Link Checking:** Automatic validation to ensure deleted roster elements (like NPCs) smoothly update associated quests and logs without breaking data structures.

---

## 📄 License & Technical Stack

* **Stack:** Vanilla HTML5, CSS3, JavaScript (ES6+), and inline SVGs. No external npm packages, CSS frameworks, or runtime dependencies.
* **License:**
This software and its asset templates are released under a modified permissive license:

*   **Modification and Derivative Works:** You are free to copy, modify, distribute, and build upon this software, its templates, or its underlying structures for any purpose, including commercial products (such as your own commercial TTRPG settings, custom modules, or derivative toolkits built using or adapting the template structures).
*   **Original Software Restriction:** You may not sell, redistribute for profit, or commercialize the original, unmodified `Field Companion.html` codebase, `MapTemplate.json`, and/or `SettingTemplate.json` as standalone products. 
*   **Attribution:** Any derivative works must include a credit line acknowledging this original project.

*THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.*