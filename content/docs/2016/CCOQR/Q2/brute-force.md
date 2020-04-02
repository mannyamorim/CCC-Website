---
title: "2016-CCOQR-Q2 Through A Maze Darkly - Brute Force"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-CCOQR-Q2 Through A Maze Darkly - Brute Force Solution

## Algorithm

The algorithm is the most basic approach - start in each room and walk through each door and keeping track of the number of hallways travled following the path until you return to the room. Then compute the maximum path for that room.

## Optimization

The one optimization that can be applied to the brute force approach is precomputing the next door. The results of that optimization are presented in the results.

## Testing Results

| Task# | Test# | Rooms   | Questions | Total Distance | Simple Execution Time | Optimized Execution Time |
|-------|-------|--------:|----------:|---------------:|-----------------------|--------------------------|
| 1     | 1     | 100     | 100       | 19,800         | 0.063                 | 0.042                    |
| 1     | 2     | 100     | 100       | 19,800         | 0.053                 | 0.039                    |
| 1     | 3     | 100     | 100       | 19,800         | 0.038                 | 0.04                     |
| 1     | 4     | 2       | 2         | 4              | 0.037                 | 0.037                    |
| 1     | 5     | 65      | 43        | 5,850          | 0.038                 | 0.039                    |
| 1     | 6     | 100     | 100       | 620            | 0.037                 | 0.038                    |
| 1     | 7     | 100     | 100       | 2,008          | 0.036                 | 0.04                     |
| 1     | 8     | 100     | 100       | 7,776          | 0.038                 | 0.04                     |
| 1     | 9     | 100     | 100       | 13,652         | 0.039                 | 0.038                    |
| 1     | 10    | 15      | 15        | 2,964          | 0.039                 | 0.037                    |
| 2     | 11    | 100,000 | 1         | 199,998        | 0.222                 | 0.235                    |
| 2     | 12    | 100,000 | 1         | 199,998        | 2.477                 | 0.37                     |
| 2     | 13    | 100,000 | 1         | 199,998        | 0.223                 | 0.228                    |
| 2     | 14    | 100,000 | 1         | 2              | 0.182                 | 0.18                     |
| 2     | 15    | 100,000 | 1         | 2              | 0.191                 | 0.195                    |
| 2     | 16    | 100,000 | 1         | 179,898        | 0.253                 | 0.261                    |
| 2     | 17    | 100,000 | 1         | 294,035        | 0.392                 | 0.372                    |
| 2     | 18    | 635     | 1         | 399,350        | 0.353                 | 0.321                    |
| 2     | 19    | 10,000  | 1         | 396198         | 0.295                 | 0.306                    |
| 3     | 20    | 100,000 | 100,000   | 19,999,800,000 | 9 Minutes             | 9 Minutes                |
| 3     | 21    | 100,000 | 100,000   | 19,999,800,000 | 62 Hours (est)        | 5 Minutes                |
| 3     | 22    | 100,000 | 100,000   | 19,999,800,000 | 6 Minutes             | 6 Minutes                |
| 3     | 23    | 100,000 | 100,000   | 19,999,800,000 | 6 Minutes             | 6 Minutes                |
| 3     | 24    | 100,000 | 100,000   | 597,452        | 0.982                 | 0.548                    |
| 3     | 25    | 100,000 | 100,000   | 26,746,872,867 | 15 Minutes            | 18 Minutes               |
| 3     | 26    | 635     | 635       | 253,642,605    | 62                    | 8.375                    |
| 3     | 27    | 4,500   | 4,500     | 1,791,483,017  | 85.5                  | 67.189                   |
| 3     | 28    | 100,000 | 100,000   | 32,236,609,882 | 18 Minutes            | 21 Minutes               |
| 3     | 29    | 100,000 | 100,000   | 11,256,017,057 | 4 Minutes             | 4 Minutes                |

## Simple Implementation

{{< highlight cpp >}}
#include <iostream>
#include <string>
#include <algorithm>
#include <vector>
 
int   totalRooms;
int*  roomDoorCounts;
int** roomDoors;
 
//---------------------------------------------------------------------------
// Input
//---------------------------------------------------------------------------
 
void read_input()
{
    std::cin >> totalRooms;
    roomDoorCounts = new int[totalRooms];
    roomDoors = new int*[totalRooms];
 
    for (int room = 0; room < totalRooms; room++)
    {
        std::cin >> roomDoorCounts[room];
        roomDoors[room] = new int[roomDoorCounts[room]];
 
        for (int door = 0; door < roomDoorCounts[room]; door++)
        {
            int next_room;
            std::cin >> next_room;
            roomDoors[room][door] = next_room - 1; // 0-based index
        }
    }
}
 
 
 
//---------------------------------------------------------------------------
// Next Door Precomputation
//---------------------------------------------------------------------------
 
int get_next_door(int currentDoor, int roomDoorCount)
{
    int nextDoor;
    if (currentDoor != 0)
        nextDoor = currentDoor - 1;
    else
        nextDoor = roomDoorCount - 1;
    return nextDoor;
}
 
int find_door_in_room(int currentRoom, int roomDoorCount, int nextRoom)
{
    for (int door = 0; door < roomDoorCount; door++)
    {
        if (roomDoors[currentRoom][door] == nextRoom)
            return door;
    }
    abort();
}
  
 
//---------------------------------------------------------------------------
// Solver
//---------------------------------------------------------------------------
 
int compute_door_cost(int startingRoom, int startingDoor)
{
    int currentRoom = startingRoom;
    int currentDoor = startingDoor;
    int cost = 0;
    do
    {
        // Get the next Room & index
        int nextRoom = roomDoors[currentRoom][currentDoor];
        int nextRoomDoorCount = roomDoorCounts[nextRoom];
        int index = find_door_in_room(nextRoom, nextRoomDoorCount, currentRoom);
        int nextDoor = get_next_door(index, nextRoomDoorCount);
     
        // Increase Cost
        cost++;
 
        // Go to next room
        currentRoom = nextRoom;
        currentDoor = nextDoor;
    } while (currentRoom != startingRoom);
    return cost;
}
 
int compute_room_max(int room)
{
    int maxCost = 0;
    int roomDoorCount = roomDoorCounts[room];
    for (int door = 0; door < roomDoorCount; door++)
        maxCost = std::max(maxCost, compute_door_cost(room, door));
    return maxCost;
}
 
 
//---------------------------------------------------------------------------
// Output
//---------------------------------------------------------------------------
 
void answer_questions()
{
    int totalQuestions;
    std::cin >> totalQuestions;
    for (int q = 0; q < totalQuestions; q++)
    {
        int room;
        std::cin >> room;
        std::cout << compute_room_max(room - 1) << std::endl;
    }
}
 
int main()
{
    read_input();
    answer_questions();
    return 0;
}
{{< /highlight >}}

## Optimized Implementation

The only optimization involved is to pre-compute the next door so the navigation along the paths is fast.

{{< highlight cpp >}}
#include <iostream>
#include <string>
#include <algorithm>
#include <unordered_map>
 
//---------------------------------------------------------------------------
// Int Reader
//---------------------------------------------------------------------------
 
int get_next_int()
{
    int n;
    scanf("%d", &n);
    return n;
}
  
 
//---------------------------------------------------------------------------
// Room Storage
//---------------------------------------------------------------------------
 
typedef struct
{
    int nextRoom;
    int nextDoor;
    bool valid;
} RoomDoor;
 
typedef struct
{
    int doorCount;
    RoomDoor* doors;
    std::unordered_map<int, int> doorMap;
} Room;
 
//---------------------------------------------------------------------------
// Input
//---------------------------------------------------------------------------
int   totalRooms;
Room*  rooms;
void read_input()
{
    totalRooms = get_next_int();
    rooms = new Room[totalRooms];
    for (int room = 0; room < totalRooms; room++)
    {
        int doorCount = get_next_int();
        rooms[room].doorCount = doorCount;
        rooms[room].doors = new RoomDoor[doorCount];
        for (int door = 0; door < doorCount; door++)
        {
            int nextRoom = get_next_int() - 1; // 0-Based Index
            rooms[room].doors[door].nextRoom = nextRoom;
            rooms[room].doors[door].valid = true;
            rooms[nextRoom].doorMap[room] = door; // Store the reverse Lookup to the door
        }
    }
}
 
 
//---------------------------------------------------------------------------
// Next Door Precomputation
//---------------------------------------------------------------------------
void precompute_next_door()
{
    for (int room = 0; room < totalRooms; room++)
    {
        int roomDoorCount = rooms[room].doorCount;
        for (int door = 0; door < roomDoorCount; door++)
        {
            int nextRoom = rooms[room].doors[door].nextRoom;
            int nextDoor = rooms[room].doorMap[nextRoom] - 1;
            if (nextDoor == -1)
                nextDoor = rooms[nextRoom].doorCount - 1;
            rooms[room].doors[door].nextDoor = nextDoor;
        }
    }
}
 
 
//---------------------------------------------------------------------------
// Solver
//---------------------------------------------------------------------------
 
int compute_door_cost(int startingRoom, int startingDoor)
{
    int currentRoom = startingRoom;
    int currentDoor = startingDoor;
    int cost = 0;
    do
    {
        // Get the next Room & index
        int nextRoom = rooms[currentRoom].doors[currentDoor].nextRoom;
        int nextDoor = rooms[currentRoom].doors[currentDoor].nextDoor;
 
        // Increase Cost
        cost++;
 
        // Go to next room
        currentRoom = nextRoom;
        currentDoor = nextDoor;
 
    } while (currentRoom != startingRoom);
 
    return cost;
}
 
int compute_room_max(int room)
{
    int maxCost = 0;
    int roomDoorCount = rooms[room].doorCount;
    for (int door = 0; door < roomDoorCount; door++)
        maxCost = std::max(maxCost, compute_door_cost(room, door));
    return maxCost;
}
  
 
//---------------------------------------------------------------------------
// Output
//---------------------------------------------------------------------------
 
void answer_questions()
{
    int totalQuestions = get_next_int();
    for (int q = 0; q < totalQuestions; q++)
    {
        int question = get_next_int() - 1; // 0 Based Index
        int cost = compute_room_max(question);
        printf("%d\n", cost);
    }
}
 
int main()
{
    read_input();
    precompute_next_door();
    answer_questions();
    return 0;
}
{{< /highlight >}}
