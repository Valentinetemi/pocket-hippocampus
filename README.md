This project was submitted to the ryze.ai hackathon by Temiloluwa Valentine.

### Pocket Hippocampus

**An on-device memory for the physical world.**

Pocket Hippocampus is an experimental visual-memory system that gives small devices a form of object permanence. It observes physical objects, distinguishes one object from another, records how they move through a task, and remembers what happened after they leave the camera’s view.

The first demonstration is a private workbench memory for technicians. During a repair session, Pocket Hippocampus follows tools and components across the workspace. A technician can ask what disappeared, what has not been returned, and where an item was last observed.

All AI inference runs locally. The workspace, object history, and user questions do not need to be sent to a cloud AI service.

### The problem

Computer-vision systems are usually good at describing what is visible in the current frame. Once an object is hidden, removed, or carried outside the camera’s view, it effectively stops existing to the system.

That is a problem during physical tasks. A technician repairing a phone may handle several tools, screws, cables, and components at once. A missing screw or forgotten component can interrupt the repair, waste time, or cause an incomplete reassembly.

Pocket Hippocampus explores a different kind of vision system. Instead of repeatedly asking only “What can I see?”, it also asks:

* Have I seen this object before?
* Where was it last observed?
* Did it move or disappear?
* Has it returned to the workspace?
* Which parts of the task remain unresolved?

### Core demonstration

1. Start a local work session.
2. Show the camera several tools and components.
3. Move, use, hide, and return some of the objects.
4. Leave one component outside the workspace.
5. Ask what is missing and where it was last seen.

Pocket Hippocampus should answer with evidence from its stored event history:

> The small black component disappeared from the right side of the workbench 18 seconds ago and has not returned.

The goal is not to recognize every possible tool. The hackathon prototype focuses on preserving object identity, recording meaningful state changes, and recalling a short physical history reliably.

## Why it runs on-device

A camera observing a workbench may capture private devices, customer information, proprietary equipment, and details of someone’s surroundings. Continuously uploading that stream to a remote model would create unnecessary privacy, latency, cost, and connectivity problems.

Pocket Hippocampus keeps its perception, identity matching, event memory, retrieval, reasoning, and optional voice processing on the user’s device.

This provides:

* Private processing
* Low-latency responses
* Offline operation
* No inference API costs
* Direct user control over stored memories

## How it works

Every camera observation passes through a small local pipeline.

### 1. Perception

YOLO11n detects objects currently visible in the scene.

### 2. Identity

A compact image-embedding model compares each detected object with previously observed objects. This helps the system determine whether an item is new or the same physical object returning to view.

### 3. Event memory

A rule-based event engine compares current observations with recent state. It creates events such as:

* Object appeared
* Object remained visible
* Object changed position
* Object disappeared
* Object reappeared
* Object was last observed at a location

The events are stored locally in SQLite.

### 4. Recall

The system retrieves the observations and events relevant to a question. A small local language model converts that structured evidence into a concise response.

The language model does not receive an entire video and guess what happened. Its answer is grounded in events already produced by the perception and memory layers.

## Architecture

| Layer | Responsibility | Planned implementation |
| --- | --- | --- |
| Perception | Detect visible objects | YOLO11n |
| Identity | Match objects with earlier observations | DINOv2 Small or another compact image encoder |
| Memory | Record appearances, movement, disappearance, and return | Rule-based event engine and SQLite |
| Retrieval | Find events relevant to a question | Structured queries and local vector similarity |
| Reasoning | Explain retrieved events in plain language | FunctionGemma or another local model with 500M parameters or fewer |
| Voice | Accept optional spoken questions | Whisper Tiny or another local speech model |

Every model included in the final submission will contain no more than 500 million parameters and will run locally. Pocket Hippocampus will not use OpenAI, Anthropic, Gemini, or any hosted AI inference API.

## Example questions

* Which component has not returned?
* Where was the screwdriver last seen?
* Did the blue tool move?
* What disappeared from the workbench?
* When did this component leave the scene?
* Is this the same object that appeared earlier?

When the stored evidence is incomplete or the identity match is uncertain, the system should clearly report that uncertainty instead of inventing an event.

## Hackathon scope

The Ryze AI Hack prototype focuses on one reliable loop:

**Observe an object, preserve its identity, record changes over time, and recall its history.**

The prototype will support a small number of objects in a controlled indoor workspace.

Continuous life logging, medical use, surveillance, large-scale multi-camera tracking, wearable hardware, and a complete repair assistant are outside the scope of this submission.

The workbench is the first practical demonstration of the underlying memory system, not the limit of what the architecture could eventually support.

## Current status

The existing prototype includes:

* Live camera capture
* YOLO11n perception
* Local SQLite observations
* Visibility episodes
* Depth-estimation experiments
* A C++ spatial visualizer
* An object-identity subsystem under integration

The Ryze AI Hack build will connect these parts into a complete on-device observation, memory, and recall loop.

## Setup

Setup instructions will be added when the on-device pipeline is finalized.

## Models and licences

The exact model variants, parameter counts, licences, files, and sources will be verified and recorded before submission.

| Model | Purpose | Parameters | Licence | Source |
| --- | --- | ---: | --- | --- |
| YOLO11n | Object detection | To verify | To verify | To add |
| Selected compact image encoder | Object identity | To verify | To verify | To add |
| Selected local language model | Grounded response generation | To verify | To verify | To add |
| Selected local speech model | Optional voice input | To verify | To verify | To add |

Only models that satisfy the Ryze AI Hack rules will be included in the final application.

## Responsible use

Pocket Hippocampus is a research prototype. It is not a medical device, surveillance product, or safety-critical system.

Object identity can be affected by lighting, occlusion, camera movement, and visually similar items. The interface will expose uncertainty, provide visible recording controls, and allow users to inspect and delete stored memories.

## Licence

The project code is released under the [MIT Licence](LICENSE). Individual model weights remain subject to their respective licences.

## Author

Built by [Temiloluwa Valentine](https://github.com/Valentinetemi).
