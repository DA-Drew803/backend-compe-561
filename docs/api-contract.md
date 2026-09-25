# Vacation Together Backend API Contract Draft

## Scope

This document proposes the API needed by the four web pages in the [frontend repository](https://github.com/DA-Drew803/frontend-compe-561): Landing, Planning, Destination Results, and Itinerary. It is a planning contract, not a description of implemented endpoints. The backend owns validation, trip persistence, matching, estimated costs, and itinerary changes. The frontend owns page layout, navigation, form interactions, and English website copy.

The agreed implementation stack is FastAPI, Pydantic, SQL, and PostgreSQL. Keep the backend in this repository and the Next.js frontend in its separate repository.

## Proposed endpoints

| Method and path | Purpose | Main response |
| --- | --- | --- |
| `POST /trips` | Validate and save the group's planning choices. | A trip ID and the saved plan. |
| `GET /trips/{tripId}` | Restore a plan when Planning is reopened or refreshed. | The saved plan. |
| `GET /trips/{tripId}/matches` | Rank available destination options for a plan. | Destination IDs, match values, reasons, and per-person cost estimates. |
| `POST /trips/{tripId}/destination` | Save the selected destination. | The selected destination ID. |
| `GET /trips/{tripId}/itinerary` | Restore the current itinerary. | Destination, trip dates, day entries, activities, and estimated cost. |
| `POST /trips/{tripId}/itinerary/refinements` | Request a cheaper plan, less walking, or an activity swap. | The updated itinerary and estimate, or a clear reason no alternative is available. |

## Planning request

`POST /trips` should accept these fields after the team agrees on exact names and values:

| Field | Meaning |
| --- | --- |
| `travelers` | At least one traveler name or ID. The five names in Figma are examples, not a permanent limit. |
| `dateChoice` | One of the seasonal presets, flexible dates, or custom dates. |
| `customStartDate`, `customEndDate` | Required together when custom dates are chosen; the end date cannot precede the start date. |
| `budgetTier` | Exactly one budget choice. The tier-to-amount mapping and currency must be agreed on. |
| `interests` | One or more selected interests. |

The Figma form currently collects one **shared** set of choices for the group. It does not collect preferences from each traveler. The backend must not claim to calculate individual compatibility unless a per-traveler input flow is added.

## Results and itinerary responses

Each result needs a stable destination ID, name, match percentage, short explanation, estimated cost per person, currency, and a data status such as `sample` or `calculated`. The frontend can then label sample values honestly.

The itinerary response needs the selected destination ID, trip dates, numbered days, activities, estimated cost, currency, and applied refinements. **Make It Cheaper** must update both the activities and estimate. **Reduce Walking** must change relevant activities or transport assumptions. **Swap Activity** needs a target activity and a valid replacement; the selection interaction is still to be designed.

Use clear English error responses for invalid input, unknown trip or destination IDs, unavailable matches, and impossible refinements. The frontend should preserve user selections after an error.

## Decisions to agree on before implementation

1. Are preferences shared by the group or submitted separately by every traveler?
2. What dates and year do the seasonal presets represent?
3. How is the match percentage calculated and explained?
4. Where do price estimates come from, and which costs do they include?
5. What alternatives support itinerary refinements?

Keep `AGENTS.md` local and ignored by Git; never commit or push it.
