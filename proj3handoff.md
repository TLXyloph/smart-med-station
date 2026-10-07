# Handoff: Smart Medication Station for EMS Vehicles

**Project:** Engineering class design project
**Status:** Concept phase. Research is done and an architecture is proposed, but nothing has been validated with paramedics yet.
**Last updated:** 2026-10-05

---

## 1. TL;DR

- **Original idea:** an automatic pill dispenser for ambulances. We compared two options: a rotary dispenser that dispenses pills on request, and a gantry that grabs bottles of pills, liquids or powders.
- **What the research showed:** pills are a small minority of EMS medications, and grabbing them is not a time bottleneck. Calculating doses and drawing them up is the slow, error-prone step, especially for children. Controlled substances also carry a heavy storage and paperwork burden.
- **Pivot:** a **rotary carousel of unit-dose packages** (prefilled syringes, vials, blister cards). It adds **weight-based dose guidance, barcode verification and automatic logging**, plus a **hardened controlled-substance compartment** designed around the 2026 DEA rule.
- **Value proposition:** fewer dosing errors and faster drug delivery on high-acuity calls (cardiac arrest, pediatrics, seizures, anaphylaxis), and less manual controlled-substance counting and paperwork.

---

## 2. Problem Framing

### 2.1 What EMS actually administers

Source: NEMSIS 2023 national report, top 20 medications on 911 calls with patient contact. Percentages are each drug's share of administrations within the top 20. **The grouping by delivery form is our estimate**; NEMSIS does not split this list by route.

| Delivery form | Drugs (share of top-20 administrations) | Total |
|---|---|---|
| Gas / nebulized | Oxygen 27.3%, albuterol/DuoNeb 8.6% | ~36% |
| IV fluid bags | Saline 15.0%, lactated Ringer's 0.7% | ~16% |
| Syringe (IV/IO/IM, or intranasal via atomizer) | Fentanyl 7.1%, epinephrine 6.8%, naloxone 3.8%, midazolam 1.9%, methylprednisolone 1.2%, morphine 0.8%, ketamine 0.6%, ketorolac 0.6%, diphenhydramine 0.6%, sodium bicarbonate 0.5%, dexamethasone 0.4% | ~24% |
| Could be a tablet | Ondansetron 9.0% (dissolving tablet or IV), aspirin 6.9%, nitroglycerin 5.7% (under-the-tongue tablet or spray), acetaminophen 0.6% | 7–22% |
| Oral gel | Glucose 1.8% (NEMSIS codes oral sugar as "glucose" and IV sugar as "dextrose") | ~2% |

**Takeaways**
- Aspirin is the only drug that is reliably a pill everywhere. Ondansetron and nitroglycerin are often given IV or as a spray, depending on the agency.
- Liquids taken by mouth are essentially absent. Powders are rare and come as single-dose vials that mix themselves, not as bottles.
- Oxygen and IV fluids (~52%) are out of scope for any dispenser.
- **Eight drugs make up about 86% of top-20 administrations:** oxygen, saline, ondansetron, albuterol, fentanyl, aspirin, epinephrine and nitroglycerin.

### 2.2 How many drugs are on a truck

- A typical paramedic (ALS) unit carries **roughly 30–50 drugs**, depending on the state and agency formulary. Basic (BLS) units carry far fewer.
- **There are more stock items (SKUs) than drugs.** One drug can come in several forms; for example, Alabama requires two epinephrine concentrations and more than one dextrose strength.
- **Design target:** about **15–25 carousel slots** should cover high-use items. Rarely used drugs can stay in a normal drawer.

### 2.3 Why the fast pill dispenser was dropped

1. **Pill doses are fixed and need no math.** Aspirin and nitroglycerin are grabbed from a pouch in seconds.
2. **Many drugs are given at the patient's side,** from the jump bag, before the patient is in the ambulance. Equipment mounted in the vehicle only helps after loading.
3. **Loose-pill hoppers don't fit how drugs are stocked.** Nitroglycerin tablets lose potency unless kept in their original sealed glass bottle. Agencies also track lot numbers and expiration dates per package.

### 2.4 The real bottleneck: calculating and preparing doses

- **Errors are common.** Studies report pediatric EMS dosing errors in **more than 30% of doses**, and **over 60% for epinephrine**. A Michigan study found errors in 34.7% of pediatric administrations.
- **Reference charts alone don't fix it.** A 2025 Michigan simulation found errors in **28.8% of doses** even with a statewide dosing chart. The causes were **wrong patient weight, air in the syringe and incorrect dilution.**
- **Guided preparation helps.** A randomized trial (150 Swiss paramedics, simulated pediatric cardiac arrest) tested a step-by-step drug-prep app. It cut medication errors by **66.5 percentage points**, preparation time by **40 seconds** and time to delivery by **47 seconds** per drug.
- **Why seconds matter:** in adult cardiac arrest, each **1-minute delay** in giving a vasopressor (a drug like epinephrine that raises blood pressure) lowered the chance of restarting the heart by about **4%**.

**Honest framing for the pitch:** time savings are concentrated in high-acuity calls. On a routine chest-pain call, the device will not save much time.

---

## 3. Design Decision: Rotary Carousel vs. Gantry

**Decision: rotary carousel holding unit-dose packages.** It replaces both the loose-pill rotary dispenser and the bottle-grabbing gantry.

| Criterion | Rotary carousel | Gantry + gripper |
|---|---|---|
| Moving axes | 1 (indexed rotation) | 3 + gripper |
| Robustness to vehicle braking, potholes, vibration | Good. Can be mechanically locked in position. | Poor. Gripping varied containers in a moving vehicle is a hard reliability problem. |
| Fit with how EMS stocks drugs | Good. Unit-dose packages fit fixed slots. | Poor. EMS doesn't stock multi-dose bottles of liquids or powders to grab. |
| Access control | Easy. One access window behind a locked door. | Harder. Large open workspace. |
| Per-slot identification and counting | Easy. Fixed slot positions. | Harder. |
| Build complexity for a class | Moderate | High |

---

## 4. Product Concept

**Working name:** Smart Med Station

### 4.1 Core workflow

1. **Select:** the medic picks a protocol (e.g., "Pediatric seizure") and enters the patient's weight, or a length-tape color zone for children.
2. **Present:** the carousel rotates and unlocks **only** the correct slot.
3. **Guide:** the screen shows the dose in **mg and mL for that exact concentration**, plus the recommended syringe size. Where relevant, it gives step-by-step dilution instructions.
4. **Verify:** the medic scans the package barcode before drawing up. A mismatch in drug or concentration is blocked; for example, it catches the wrong epinephrine strength.
5. **Log:** the system automatically records drug, dose, concentration, time, user and incident number, then exports it for the patient care report.

### 4.2 Scene-side consideration

Much of the care happens away from the vehicle, from the jump bag. Two options to evaluate:
- The dose guidance also runs on a tablet or phone used at the scene.
- The station can "kit" drugs into the jump bag, with check-out and check-in tracking.

### 4.3 Stretch concept: automated draw-up

A mechanism that pulls the exact volume from a vial into a syringe would directly target the air and dilution errors found in the 2025 Michigan study.
- **Hard parts:** sterility, removing air bubbles, handling many vial and syringe sizes, and much heavier regulatory burden for a real product.
- **Class version:** demonstrate with water only.

---

## 5. Controlled Substances

### 5.1 Regulatory context

DEA final rule implementing the Protecting Patient Access to Emergency Medications Act (PPAEMA). Published Feb 5, 2026; **effective Mar 9, 2026**. Adds 21 CFR 1301.80. *(Summary for design purposes, not legal advice. Verify against the rule text.)*

- **Storage:** controlled drugs must be in a locked, substantially constructed cabinet or safe. In a vehicle, the safe must be **separately locked and permanently mounted**; a locked ambulance alone is not enough.
- **Dispensing machines:** the rule **explicitly allows storage in automated dispensing machines.**
- **Jump bags:** crews may carry controlled drugs on their person or in a jump bag while responding to an emergency. The drugs must go back to secure storage afterward, at shift end and during non-emergency stops such as meals.
- **Vehicle locking:** a vehicle storing controlled drugs must be locked when it is parked outside an enclosed registered or designated location, or left unattended during non-emergency stops.
- **Out of service:** drugs must be removed promptly if the vehicle is taken out of service.

### 5.2 Design features

- **Separate hardened compartment:** a steel compartment bolted to the vehicle frame, with its own lock and a door-open sensor.
- **Two-factor access:** badge plus PIN, or a fingerprint. It releases **one unit at a time**, tied to the user and incident number.
- **Witnessed waste:** when only part of a vial is used, a second crew member must log in to record the discarded amount. This is common agency practice.
- **Automatic counts:** per-slot weight sensors or RFID tags make the shift-change count instant and detect discrepancies.
- **Jump-bag check-out and check-in:** with a timer alert if drugs aren't returned after the call.
- **Tamper and audit logging:** door events, failed access attempts, power loss, and shocks detected by an accelerometer.
- **Mechanical key override:** a dead battery must never block patient care. Every override use is logged and flagged for review.
- **Out-of-service mode:** guides the crew through removing and transferring the drugs.

---

## 6. Draft Requirements

*Values marked TBD need research or user input.*

### Functional (FR)
- **FR-1:** Hold N unit-dose packages in indexed slots. N is TBD; the target is 15–25 for the product and 8–12 for the prototype.
- **FR-2:** Present the selected slot within TBD seconds of selection.
- **FR-3:** Calculate weight-based doses from a configurable formulary and protocol file, never from hard-coded values.
- **FR-4:** Display each dose in mg and mL for the stocked concentration.
- **FR-5:** Verify drug and concentration by barcode before release is logged. Confirm which barcode formats appear on the actual packages.
- **FR-6:** Log every access and administration with timestamp, user and incident number, and export the log (CSV for the prototype).
- **FR-7:** Track expiration dates and lot numbers per slot, and alert before expiry.

### Controlled substances (CS)
- **CS-1:** Separate locked compartment, permanently mounted.
- **CS-2:** Two-factor authentication for access.
- **CS-3:** Single-unit release per authenticated request.
- **CS-4:** Witnessed waste recording.
- **CS-5:** Automatic count reconciliation.
- **CS-6:** Jump-bag check-out and check-in with alerts.
- **CS-7:** Mechanical override with audit trail.

### Mechanical / environmental (ENV)
- **ENV-1:** Survive vehicle vibration and shock. Test spec TBD; find the relevant ambulance equipment standards.
- **ENV-2:** Carousel locks mechanically when idle, and slots retain packages under hard braking.
- **ENV-3:** Run from the vehicle's 12 V DC electrical system, with battery backup. Runtime TBD.
- **ENV-4:** Operating temperature range TBD. Consider internal temperature logging, since drugs stored in ambulances see temperature extremes.
- **ENV-5:** Fit the available mounting envelope in the patient compartment (TBD from vehicle measurements).

### Safety / usability (SAF)
- **SAF-1:** Weight entry must be hard to get wrong: confirm kg vs. lb, offer length-tape zones, and flag implausible weights. Wrong weight is a top cause of errors.
- **SAF-2:** The device must never block access to drugs. Failures default to a manual override.
- **SAF-3:** Usable with gloves, in low light, and in a moving vehicle.

---

## 7. Proposed Architecture

```
            +-------------------------------+
            |   Touchscreen UI + Scanner    |
            +---------------+---------------+
                            |
            +---------------v---------------+
            |  Main controller (SBC)        |
            |  - formulary/protocol engine  |
            |  - dose calculator            |
            |  - audit log / export         |
            +---+-----------+-----------+---+
                |           |           |
     +----------v--+  +-----v------+  +-v---------------+
     | Motion ctrl |  | Access ctrl|  | Sensing         |
     | stepper +   |  | solenoid   |  | slot weight /   |
     | home sensor |  | locks, RFID|  | RFID, door, IMU,|
     | + detent    |  | badge, PIN |  | temperature     |
     +-------------+  +------------+  +-----------------+
                            |
            +---------------v---------------+
            | Power: 12 V vehicle + backup  |
            +-------------------------------+
```

| Subsystem | Prototype suggestion |
|---|---|
| Carousel drive | Stepper motor with a home/index sensor and a mechanical detent or locking pin |
| Slot access | A single access window with a solenoid-latched door |
| Controlled compartment | Separate steel box with its own solenoid lock and RFID badge plus PIN |
| Counting | Load cell per controlled slot; RFID optional |
| Verification | USB 2D barcode scanner |
| Controller | Single-board computer for the UI and logic, plus a microcontroller for real-time motion and I/O |
| Data | Local log file (CSV/SQLite) with an export function |

---

## 8. Prototype Scope (Class)

**MVP**
- An 8–12 slot carousel with indexing and a mechanical lock.
- An access window with a solenoid lock.
- Touchscreen dose guidance for 3–5 demo protocols, using doses taken from a published agency protocol and double-checked.
- Barcode verification.
- A controlled compartment with two-factor access, single-unit release and a weight-sensor count.
- Audit log with CSV export.
- **Dummy vials only.** Handling real controlled substances requires a DEA registration.

**Stretch**
- Automated draw-up demonstrated with water.
- Jump-bag check-out and check-in with timer alerts.
- A basic vibration or shake test.
- Temperature logging.

**Out of scope**
- Real drugs.
- Clinical claims.
- Regulatory submission.
- Live integration with patient care report software.

---

## 9. Risks & Open Questions

- **Protocols vary by agency.** The formulary and protocols must be configurable, and a real product would need medical director approval.
- **Dose guidance counts as clinical decision support.** Accuracy and validation are critical, so never hand-code doses from memory.
- **Scene vs. vehicle:** how much does a vehicle-mounted unit help if most drugs are given from the jump bag? Validate this with users.
- **Space and mounting:** where would it physically fit in the patient compartment?
- **Reliability in a moving vehicle:** vibration, shock and temperature, with no test spec defined yet.
- **Restock workflow:** how agencies restock (hospital exchange, central supply) affects the loading and scanning design.
- **Competition:** electronic narcotics safes for ambulances already exist, and hospital dispensing-cabinet makers are watching the new DEA rule. We need a clear differentiator; dose guidance plus verification is the current candidate.

---

## 10. Next Steps

1. **Interview 2–3 working paramedics.** Suggested questions:
   - Walk me through your last pediatric medication call. Where did time go?
   - How do you handle controlled-substance counts at shift change, and how long do they take?
   - Where are drugs given more often: on scene from the bag, or in the truck?
   - What would make you *not* use a device like this?
   - Where in the patient compartment is there space?
2. **Get a real formulary and protocol set** from a local agency or the state. Pick the demo protocols from it.
3. **Lock the slot count and package size range.** Measure the actual vials, prefilled syringes and blister cards.
4. **CAD the carousel** and size the motor, including the detent and locking mechanism.
5. **Build the dose-calculation logic** from protocol data and have a paramedic or instructor check it.
6. **Build and test** the MVP, then the stretch items as time allows.

---

## 11. Sources

- NEMSIS Annual Public Data Report 2023: https://nemsis.org/wp-content/uploads/2025/09/NEMSIS-Annual-Public-Data-Report-2023___.pdf
- NEMSIS V3 Suggested List whitepaper (glucose vs. dextrose coding): https://git.nemsis.org/projects/NEP/repos/nemsis_public/raw/SuggestedLists/NEMSIS_V3_Suggested_List_dConfiguration.04.pdf?at=refs/tags/3.2.3.120418
- Summa Health EMS Protocol, Medication Administration: https://www.summahealth.org/-/media/project/summahealth/website/page-content/ems/ems-protocols/procedures/medication-administration.pdf
- Pennsylvania Bulletin, Approved Drugs for ALS Ambulance Services: https://www.pacodeandbulletin.gov/Display/pabull?file=/secure/pabulletin/data/vol42/42-27/1270.html
- Alabama Paramedic Medication Formulary: https://www.alabamapublichealth.gov/ems/assets/paramedic.formulary.pdf
- Pediatric Prehospital Medication Dosing Errors, National Survey of Paramedics: https://www.tandfonline.com/doi/full/10.1080/10903127.2016.1227001
- Medication Dosing Errors in Pediatric Patients Treated by EMS: https://www.tandfonline.com/doi/full/10.3109/10903127.2011.614043
- Statewide Dosing Reference Aid and Pediatric Dosing Errors (2025): https://pubmed.ncbi.nlm.nih.gov/41359816/
- JAMA Network Open, Mobile App and Prehospital Medication Errors (2021): https://jamanetwork.com/journals/jamanetworkopen/fullarticle/2783613
- PedAMINES continuous-infusion trial (vasopressor delay and outcomes): https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5311423/
- NASCSA, New DEA Rules on EMS: https://nascsa.org/new-dea-rules-on-emergency-medical-services/
- NCRETAC, DEA Rule for EMS Controlled Substances: https://ncretac.org/dea-rule-ems-controlled-substances-ppaema/
- DeKoninck Law, DEA Final Rule Key Takeaways: https://www.dekolaw.com/post/breaking-news-dea-just-published-its-final-rule-on-ems-agencies-here-are-the-key-takeaways-you-need
- NAEMSP, DEA Releases PPAEMA Rule: https://naemsp.org/news/dea-releases-rule-protecting-patient-access-to-emergency-medications-act-of-2017-ppaema-2/
- Federal Register, final rule: https://www.federalregister.gov/documents/2026/02/05/2026-02288/registering-emergency-medical-services-agencies-under-the-protecting-patient-access-to-emergency
- BD, When Seconds Matter (PPAEMA): https://news.bd.com/When-Seconds-Matter-Protecting-Patient-Access-to-Emergency-Medications-Act
