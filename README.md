# NavUS <img src="https://user-images.githubusercontent.com/35805635/120774197-a3257d80-c554-11eb-804c-579cd979efe1.png" width="80" height="80">


## Motivation
When you’re trying to look for a classroom and you entered the venue in Google maps, there are no suitable results returned. Currently, students have to manually look up the location of their classrooms/lecture theatres on NUSMods, then look up the individual routes of the NUS ISB to get to their intended destination (either through the NextBus app or static ISB map). This project aims to minimise the lookup time. Users just need to input their current location/via GPS and their destination location within campus (e.g. I3-Auditorium to FoS S12 Bldg) and NavUS should provide step-by-step instructions to get from their current location to their destination. NavUS uses live bus timing data to calculate the estimated travel time required and recommends a route with its bus travel time to the destination’s nearest bus stop.

## User Stories
1. As a student in NUS, I want to be able to find the location of my lessons quickly.
2. As a student in NUS, I want to be able to get the bus arrival timings for each bus stop.
3. As a visitor to NUS, I want to be able to locate nearby amenities even without prior knowledge of the NUS campus layout.
4. As an NUS teaching staff, I want to know how to get from one lecture location to my next tutorial location across campus using the ISB.

## Demonstration
Telegram           |  Android App
:-------------------------:|:-------------------------:
<img src="https://user-images.githubusercontent.com/35805635/119498649-6d380a80-bd98-11eb-9992-95b0dc00a5a2.png" width="350" height="600"> |  <img src="https://user-images.githubusercontent.com/35805635/119499100-f2bbba80-bd98-11eb-9b49-6a3a276345a0.png" width="350" height="600">

## Features
- **Android UI** for user to enter his source and destination, as well as detailed steps to his destination
- **Google Maps** interface to show the route
- **Estimated Time of Arrival (ETA)** taking into account bus arrival/transit times
- **Shortest path route** from source to destination (via ISB)
- **Telegram bot** which allows user to enter his source and destination for directions

## Implementation Overview
The **Telegram Bot** provides a chat-like interface for users to query the route to their destination.

A **Flask Server** will share a common database on `firebase` with the Telegram Bot as well as the Android app and provide the functionality of computing the route from the user’s location to destination.

The **Android app** provides a visual interface for the user to enter his destination and suggests a route based on his current location.

<img width="1094" alt="NavUS Graph_v1" src="https://user-images.githubusercontent.com/35778042/120532399-386c2900-c412-11eb-9cdd-efba99bf36f8.png">
This chart illustrates the graph model currently used for calculating the shortest time taken from a given source and destination. The source in the given diagram is "KR MRT". A "supernode" is constructed (with outgoing edges representing the bus arrival times leading to their respective services) that serves as a starting point for running the shortest path algorithm. 


_Note: Variables a, b, c, d, e, f, g, h, i, j represent live bus arrival timings. Edges without variables are weighted with static bus travel times._

## Comparisons with existing applications
**NUS NextBus** app provides us with the bus routes and arrival times of buses but assumes you know the closest bus stop to your destination. Also, there is no functionality to enter a bus stop and be guided from your location.

**NUSMODS** provides us with the location of your classroom but not the routing information to there.

## Technical Stack
- Firebase
- Flask
- NUS NextBus API
- Telegram API
- Android
- Google Maps API

## Project Timeline
Week           |  Task
:-------------------------:|:-------------------------:
4 (31/5 - 6/6)             | Graph Modelling
5 (7/6 - 13/6)             | Integrate real-time bus arrival timings to graph
6 (14/6 - 20/6)            | Implement Telegram bot
7 (21/6 - 27/6)            | Refine Telegram bot
8 (28/6 - 4/7)             | Design Android UI
9 (5/7 - 11/7)             | Refine and improve Android UI
10 (12/7 - 18/7)           | Testing and debugging
11 (19/7 - 25/7)           | Further testing and debugging, while polishing the application as a whole

## Setup Instructions
The `Data` directory contains 4 `JSON` and 4 `Python3` files used to populate Firebase, as well as 1 `Python3` file for the logic of the path calculation. Before running the scripts, ensure that `firebase.json` is replaced with yours downloaded from the `Firebase Console`. Also, edit the file `firebaseurl.txt` to your Firebase's URL from the `Firebase Console`.

- `Python3 Scripts`
  - `InsertBusOperatingHours.py` is used to populate Firebase at the `/BusOperatingHours` reference. It contains all the bus operating hours on *Weekdays*, *Saturdays*, and *Sundays and Public Holidays*.
  - `InsertBusRoutes.py` is used to populate Firebase at the `/BusRoutes` reference. It contains all the bus *routes* as well as the *time taken* to travel to the next busstop.
  - `InsertBusStops.py` is used to populate Firebase at the `/BusStops` reference. It contains all the bus stop's *Name*, *NextBusAlias* (used to query for bus arrival timings) and *Services* (used to determine the bus services at each bus stop).
  - `InsertVenue.py` is used to populate Firebase at the `/Venues` reference. It contains all the venue's *Name*, *Latitude*, *Longitude* and *IsBusStop* (used to determine if a venue is a bus stop).
  - `CalculatePath.py` constructs a graph with vertices representing a unique busstop and service combination and weighted edges representing the travel time between vertices. Dijkstra's Algorithm is used to calculate the shortest path from the given source to the given destination, and the `getpath` method returns this information in `JSON` format.

- `JSON Files`
  - `BusOperatingHours.json` contains the bus *operating hours*.
  - `BusRoutes.json` contains the bus *routing information* and *time taken* to each stop.
  - `BusStop.json` contains the bus stop's *Name*, *NextBusAlias* and *Services*.
  - `Venue List.json` contains the venue's *Name*, *Latitude*, *Longitude* and *IsBusStop*.

The `App` directory contains the code for the `Android` application. Update `App\src\main\java\com\example\navus\APIKeys.java` with your Firebase's URL and `App\src\debug\res\values\google_maps_api.xml` with your Google map API key.
