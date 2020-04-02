---
title: "2016-CCOQR-Q2 Through A Maze Darkly - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-CCOQR-Q2 Through A Maze Darkly - Solution

## IO

{{< highlight cpp >}}
int get_next_int()
{
    int n;
    scanf("%d", &n);
    return n;
}
{{< /highlight >}}

## Data Storage

Note the use of a structure to keep the data together. Since this problem is very dependent of accessing memory quickly keeping the required data together is critical. Also note that we are using an unordered_map container. This is one of the keys to making this code execute quickly.

{{< highlight cpp >}}
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
{{< /highlight >}}

## Read Input

{{< highlight cpp >}}
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
{{< /highlight >}}

## Next Door Pre-computation

{{< highlight cpp >}}
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
{{< /highlight >}}

## Solver

{{< highlight cpp >}}
int* roomMaxPathDistance;
int* roomStartDistance;

void init_solver()
{
    roomMaxPathDistance = new int[totalRooms];
    roomStartDistance = new int[totalRooms];
    for (int i = 0; i < totalRooms; i++)
        roomMaxPathDistance[i] = 0;
}
 
void init_distances()
{
    for (int room = 0; room < totalRooms; room++)
        roomStartDistance[room] = -1;
}
 
void process_door(int room, int distance)
{
    if (roomStartDistance[room] == -1)
    {
        // Start Measuring
        roomStartDistance[room] = distance;
    }
    else
    {
        // Compute Distance from starting point
        int path_distance = distance - roomStartDistance[room];
 
        // Only keep the maximum
        if (path_distance > roomMaxPathDistance[room])
            roomMaxPathDistance[room] = path_distance;
 
        // Start measuring again from here
        roomStartDistance[room] = distance;
    }
}
 
void solve_path(int startRoom, int startDoor)
{
    int currentRoom = startRoom;
    int currentDoor = startDoor;
    int distance = 0;
    int pass = 0;
    void init_distances();
    do
    {
        // Process this room
        process_door(currentRoom, distance);
 
        // Remove this door from the system
        rooms[currentRoom].doors[currentDoor].valid = false;
 
        // What room is next?
        int nextRoom = rooms[currentRoom].doors[currentDoor].nextRoom;
        int nextRoomDoor = rooms[currentRoom].doors[currentDoor].nextDoor;
 
        // Increase distance
        distance++;
 
        // Switch rooms
        currentRoom = nextRoom;
        currentDoor = nextRoomDoor;
 
        // Did we hit the starting room/door?
        if (currentRoom == startRoom && currentDoor == startDoor)
            pass++;
    } while (pass < 2);
}
 
void solve_rooms()
{
    init_distances();
    for (int room = 0; room < totalRooms; room++)
    {
        int roomDoorCount = rooms[room].doorCount;
        for (int door = 0; door < roomDoorCount; door++)
        {
            // Only solve room/doors that have not been explored
            if (rooms[room].doors[door].valid)
                solve_path(room, door);
        }
    }
}
{{< /highlight >}}

## Data Output

{{< highlight cpp >}}
void answer_questions()
{
    int totalQuestions = get_next_int();
    for (int q = 0; q < totalQuestions; q++)
    {
        int question = get_next_int() - 1; // 0 Based Index
        printf("%d\n", roomMaxPathDistance[question]);
    }
}
{{< /highlight >}}

## Main

{{< highlight cpp >}}
int main()
{
    read_input();
    precompute_next_door();
    init_solver();
    solve_rooms();
    answer_questions();
    return 0;
}
{{< /highlight >}}
