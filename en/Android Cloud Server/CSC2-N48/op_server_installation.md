

# Server Installation
## 1 ESD Safety
### 1.1 General Anti-Static Operation Specifications
Electrostatic discharge from the human body can easily break down electrostatic-sensitive components such as the server motherboard and storage devices. Before performing any hardware assembly, rack mounting, or disassembly operations, the following specifications must be observed:
1. Data center environment requirements
    - Lay anti-static flooring/mats in the data center, with matching anti-static work chairs; the partitions and curtains in the data center must be made of anti-static materials.
    - The metal enclosures of cabinets and racks, as well as floor-standing equipment, must be reliably grounded; instruments on the workbench must be connected to a common grounding terminal.
    - Maintain reasonable temperature and humidity in the data center; a dry environment greatly increases the probability of static electricity generation.
2. Personnel operation requirements
    - Before operating, remove conductive items such as watches, necklaces, and metal jewelry, and wear anti-static work clothes.
    - When touching components such as boards and hard drives, anti-static wrist straps / anti-static gloves must be worn.

    ![ESD Prohibited Conductive Objects Diagram](../../../servers_img/common/esd_remove_conductive_items.png)

3. Material storage and handling
    - Unassembled components must be stored in anti-static packaging bags / anti-static boxes at all times.
    - Temporarily removed hardware must be placed on anti-static foam pads.
4. Hardware touching prohibitions
    - Do not directly touch circuit board solder joints, chip pins, or exposed gold finger areas.

### 1.2 Steps for Wearing the Anti-Static Wrist Strap
Before operating, confirm that the cabinet has been reliably grounded:
1. Insert the wrist into the anti-static wrist strap;

    ![ESD Wrist Strap Wearing Diagram](../../../servers_img/common/esd_wrist_strap.png)

2. Tighten the buckle so that the inner side of the strap fully fits the skin;
3. Insert the grounding plug of the wrist strap into the ESD grounding jack of the cabinet.

## 2 Required Installation Tools
Before performing physical installation and disassembly of the server, prepare the following tools and protective materials in advance:
- Protection: anti-static wrist strap, anti-static gloves, anti-static packaging bags, work gloves
- Tools: M3 Phillips screwdriver, flat-head screwdriver

## 3 Installation Environment
### 3.1 Space and Ventilation Requirements
To ensure heat dissipation and later maintenance, data center deployment must meet the following conditions:
- The server may only be installed in a restricted-access area.
- Keep the area around the equipment clean and free of debris.
- Reserve ≥800 mm of maintenance and heat dissipation space in front of and behind the cabinet.
- The server air intakes must not be blocked to ensure smooth ventilation.
- The air supply volume of the data center air conditioning must meet the heat dissipation requirements of the whole server.

### 3.2 Temperature and Humidity Standards
The equipment must be placed in a data center with controlled temperature and humidity, equipped year-round with temperature control and humidification/dehumidification equipment. The parameter standards are as follows:

| Item | Operating Standard |
| :--- | :--- |
| Temperature | 5 ℃ to 40 ℃ (41 ℉ to 104 ℉) |
| Humidity | 8% RH to 90% RH (non-condensing) |

### 3.3 Cabinet Compatibility Requirements
- The cabinet must comply with the IEC 297 standard, be a 19-inch standard rack, and have a cabinet depth ≥800 mm.
- The front door of the cabinet must be equipped with a dust filter.
- Reserve an AC power inlet position at the rear of the cabinet.