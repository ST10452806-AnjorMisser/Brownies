> # Instruction: Copy this template to your own repo and edit together with your group.

# Activity 1: App Concept and API Proposal

**PROG7314 | Group Worksheet | Maximum 4 students**

## 1. Group and concept

**Group members and student numbers:**  

Anjor Misser - ST10452806\
Kiara Naidoo - ST10252256\
Sayali Sookdeo - ST10458649


**Working app name:**  
FleetPulse — Multi-Depot Delivery & Fleet Coordination App

**Problem to be solved:**

Small and medium logistics and courier operators in South Africa largely coordinate deliveries manually, using telephonic dispatch, WhatsApp groups, and paper waybills. This causes missed SLAs, disputed proof of delivery, duplicated or inefficient routes that waste fuel, poor real-time visibility for both dispatchers and clients, and reconciliation delays when drivers operate in areas with unreliable network coverage. There is no affordable, purpose-built tool that unifies dispatching, live fleet visibility, offline-capable proof of delivery and client tracking for smaller operators who cannot afford enterprise fleet-management platforms.  

**Target users:**

●	Depot dispatchers/fleet managers who assign and monitor delivery jobs.
●	Delivery drivers who execute multi-stop routes, often in low-connectivity areas.
●	Business clients (retailers and manufacturers) who outsource deliveries and need shipment visibility.

## 2. Proposed mobile solution

**Core solution:** What will the app do, and what is the main user journey?

FleetPulse is a single Android app with role-based views. A dispatcher creates a delivery job containing multiple stops; the system suggests an optimised stop sequence across the depot's available drivers and vehicles. The assigned driver receives the job as a push notification, opens the route in-app, and works through each stop, navigating, capturing proof of delivery (photo, signature and geotagged timestamp), and logging exceptions such as a failed delivery. All stop-level actions are queued locally and sync automatically once connectivity returns. Dispatchers see fleet position and job status update in real time on a live map, and clients linked to a job receive live tracking and delivery confirmation without needing to phone the depot.

**Why Android?** Which mobile capabilities make a native app appropriate?

●	Background location and geofencing for live driver tracking and automatic stop arrival/departure detection.\
●	Native camera and BiometricPrompt APIs for proof-of-delivery capture and secure driver authentication.\
●	Local persistence (Room database) to queue stop completions, photos and signatures while offline, common on rural or depot-yard routes with poor signal.\
●	Firebase Cloud Messaging for low-latency push notifications to drivers, dispatchers and clients.\
●	Compatibility with low-cost and ruggedised Android devices, which dominate the SA courier and field-driver market, keeping the solution affordable for SMEs.  

**Positive impact:** How will the app benefit users, a community, an organisation, or society?

●	Reduces delivery disputes through an auditable, timestamped proof-of-delivery trail.\
●	Improves SLA compliance and customer trust through real-time tracking and exception alerts.\
●	Cuts fuel and time waste through route optimisation, lowering operating costs for small operators.\
●	Formalises and digitises informal SME logistics operations, supporting record-keeping for tax, insurance and client reporting.\
●	Makes enterprise-grade dispatch tooling accessible and affordable to small South African logistics businesses, supporting local job creation and business growth.

## 3. Custom REST API

Explain what the API stores or processes and why it is central to the app.

The API stores and manages depots, vehicles, drivers, clients, delivery jobs and stops, proof-of-delivery records, and offline sync logs. It is the single source of truth that reconciles actions captured offline by drivers with the real-time views seen by dispatchers and clients, and it enforces the app's core business logic.

**API responsibilities and business logic:**  

●	Authenticating and authorising users via SSO and issuing role-scoped tokens (driver, dispatcher, client).\
●	Sequencing multi-stop routes for route-optimisation suggestions.\
●	Detecting and flagging SLA breaches (e.g. a stop overdue against its delivery window) and triggering notifications.\
●	Resolving conflicts when a driver's offline-queued updates are synced (e.g. a stop marked complete while dispatcher had already reassigned it).\
●	Aggregating fleet and driver performance data for dispatcher reporting dashboards.


List at least five proposed endpoints:

|**#**|**Method and route**|**Purpose**|
|---|---|---|
|1|POST /api/auth/login|Authentcate a user (driver, dispatcher or client) via SSO and<br>return a JWT and refresh token.|
|2|GET /api/drivers/{id}/jobs|Retrieve the delivery jobs and stop sequence currently<br>assigned to a driver.|
|3|POST<br>/api/jobs/{jobId}/stops/{stopId}/pod|Submit proof of delivery (photo, signature, GPS coordinates,<br>tmestamp) for a completed stop, including ofine-queued<br>submissions.|
|4|POST /api/jobs|Create a new delivery job with multple stops and trigger<br>route-optmisaton logic.|
|5|GET /api/depots/{id}/feet-status|Return live vehicle positons and job statuses for all drivers at<br>a depot, for the dispatcher map view.|
|6|GET<br>/api/clients/{id}/shipments/{jobId}/tr<br>acking|Provide client-facing live tracking data and delivery<br>confrmaton for a shipment.|
|7|POST /api/sync/batch|Accept a batch of ofine-queued driver actons and resolve<br>conficts against server state.|


## 4. POE feature fit

|**Requirement**|**How it will ft the proposed app**|
|---|---|
|Single Sign-On|Drivers and dispatchers log in with a company Google or Microsof (Azure<br>AD) account via OAuth2/OpenID Connect, giving one identty across the<br>dispatcher web console and the Android driver app without separate<br>passwords per depot.|
|Setngs — identfy at least three|(1) Language selecton (English / isiZulu); (2) notfcaton preferences, e.g.<br>SLA-breach alert thresholds; (3) ofine sync frequency / data-saver mode<br>for low-connectvity areas; (4) preferred map/navigaton provider (Google<br>Maps or Waze).|
|Biometric authentcaton|Fingerprint or face unlock (Android BiometricPrompt) is required to open<br>the driver app each shif and to confrm submission of proof of delivery,<br>preventng a driver's device from being used by someone else and<br>reducing delivery fraud.|
|Ofine acton and synchronisaton|The driver app uses a local Room database to queue stop completons,<br>POD photos/signatures and GPS breadcrumbs when there is no signal,<br>typical of rural depots or basement loading bays. Queued actons are<br>uploaded through the batch sync endpoint and reconciled by server-side<br>confict-resoluton logic once connectvity returns.|
|Real-tme notfcaton|Firebase Cloud Messaging pushes: new job assignments to drivers, SLA-<br>breach and excepton alerts to dispatchers, and 'out for delivery' /<br>'delivered' updates to clients.|
|Two South African languages|The interface is localised in English and isiZulu, covering navigaton menus,<br>job instructons and notfcaton text, selectable in Setngs or defaultng to<br>the device language.|


## 5. Five user-defined features


|**#**|**Feature**|**Purpose and user value**|
|---|---|---|
|1|Route optmisaton<br>suggeston|Automatcally re-sequences a driver's mult-stop route to minimise<br>distance and tme, reducing fuel cost and enabling more deliveries per<br>shif.|
|2|Excepton/incident reportng|Lets a driver log a failed or problem delivery (client absent, damaged<br>goods, access denied) with a photo and note, giving dispatchers<br>evidence to resolve client disputes quickly.|
|3|In-app driver–dispatcher|Provides quick, logged text communicaton between driver and|||chat|dispatcher for real-tme issue resoluton, avoiding costly or missed<br>phone calls while driving.|
|4|Client self-service tracking|Gives business clients a live map and ETA for their shipment plus<br>delivery confrmaton, cutng 'where is my order' calls to the depot.|
|5|Depot performance<br>dashboard|Shows dispatchers KPIs such as on-tme delivery rate, per-driver<br>performance and total distance/fuel used, supportng data-driven<br>operatonal decisions.|



## 6. Comparable Android apps

This is not the formal research report. Identify three suitable apps that could later be compared with your concept.

| App | Google Play Store link | Why it is comparable |
|---|---|---|
| 1. |  |  |
| 2. |  |  |
| 3. |  |  |

## 7. Feasibility and approval

**Proposed technology:** Android stack, API technology, database, hosting, authentication and key SDKs.  

**Three main risks and how they will be reduced:**  

1.  
2.  
3.  

**Scope check:** What will be removed first if the project becomes too large?  

### Lecturer decision

- [ ] Approved
- [ ] Approved with changes
- [ ] Revise and resubmit

**Conditions or comments:**  
