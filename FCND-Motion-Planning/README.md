## Project: 3D Motion Planning

### Sorry about my mistake, i have written section `Explain the Starter Code` in below. ###
![Quad Image](./misc/enroute.png)

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
   
   - In plan_path function, beginning at line 140. I load file from third line.

2. Discretize the environment into a grid or graph representation.

   - Line 143, i use create_grid function to Discretize the obstacle into a grid.

3. Define the start and goal locations.

   - Line 149. I use current local position like start point. It's equal offset + local_position.
   - Set goal at line 152  (grid goal) . If goal is global position, in line 154,155 is  convert from latitude/longtitude position to local position, then convert local to grid goal.

4. Perform a search using A* or other search algorithm.

   - Using a_star function in planning_utils.py. In this function, i used 8 direction instead of 4 direction to reduce oscillation.

5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.

   - In planning_utils.py file, start at line 5 and end at line 22.

6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].

   - motion_planning.py file, at line 168.

7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`

##### Motion planning #####

It's same with project 1. I just modify follow requirements of this project. almost changing are in function plan_path. I described detail it in section `Implementing Your Path Planning Algorithm`.

Get home position. It is in frist line of colliders file -> set home position -> get global position -> convert global position become local_position.

-> Load all obstacles from file -> creat grid map and extract north_min (north_offset in code) and east_min (east_offset in code) (origin of the coordinates axis of grid map) -> from local_position and (north_min,east_min) i compute and set grid_start (current grid of drone).

-> set grid_goal -> grid map, grid_start, grid_goal, use `a_start` function to find path from grid_start to grid_goal. -> you `remake_path` in `planning_utils.py` to and remove unecessary points.
-> convert waypoints in grid map to global position (adding north_min `(north_offset in code)` and east_offset_min `(east_offset in code)`).
##### Planning utils #####

From original file of clone github, i added 3 function `check_collinear`, `p`, `remake_path`.
- `check_collinear` i use it to check whether consecutive 3 points in a straight line. I computed det. If it is approximately 0, this function will return True and vice versa.
- `p` convert a point in 2D to vector 3D.
- `remake_path` use `p` and `check_collinear` to check straight lines, and then remove point in middle.

- `create_grid` function, creat grid map from colliders file. (posX,posY,posZ) columns of colliders file are central point of obstacle. Remain columns are size of obstacle around central point. I have an adding safety_distance, and then set origin coordinates (0,0) is minimum of north coordinates and east coordinates.

- `Action` and `valid_actions` I added more direction to help path become more smooth.
- `a_star` functtion, i just use basic algorithm, so i hadn't any changing.
- `heuristic`, the cost, use in a_star function, is distance between current position and goal position. (norm)


Here's | A | Snappy | Table
--- | --- | --- | ---
1 | `highlight` | **bold** | 7.41
2 | a | b | c
3 | *italic* | text | 403
4 | 2 | 3 | abcd

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Here students should read the first line of the csv file, extract lat0 and lon0 as floating point values and use the self.set_home_position() method to set global home. Explain briefly how you accomplished this in your code.

     - motion_planning.py file, at line 123. I used open file fuction of python, then readline function to read first line of colliders.csv.
     - Use split with sep = ', ' to split first line into 'lat0 xxx' and 'lon0 xxx', split once again with sep = ' ' to can take home position.
     - Then i took global home position, i used self.set_home_position.
And here is a lovely picture of our downtown San Francisco environment from above!
![Map of SF](./misc/map.png)

#### 2. Set your current local position
Here as long as you successfully determine your local position relative to global home you'll be all set. Explain briefly how you accomplished this in your code.

     - Line 132.
     - Drone would update global postion, i can get it by use self.global_positon and use global_to_local function to convert it to local position.

Meanwhile, here's a picture of me flying through the trees!
![Forest Flying](./misc/in_the_trees.png)

#### 3. Set grid start position from local position
This is another step in adding flexibility to the start location. As long as it works you're good to go!

     - First i get north_offset and east_offset.
     - Line 149. I use current local position like start point. It's equal (- offset + local_position).

#### 4. Set grid goal position from geodetic coords
This step is to add flexibility to the desired goal location. Should be able to choose any (lat, lon) within the map and have it rendered to a goal location on the grid.

    - Set goal at line 152  (grid goal) . If goal is global position, in line 154,155 is  convert from latitude/longtitude position to local position, then convert local to grid goal (like start position).

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
Minimal requirement here is to modify the code in planning_utils() to update the A* implementation to include diagonal motions on the grid that have a cost of sqrt(2), but more creative solutions are welcome. Explain the code you used to accomplish this step.

     - In planning_utils.py file. I used 8 direction instead of 4 direction. I set a cost of sqrt(2).

#### 6. Cull waypoints 
For this step you can use a collinearity test or ray tracing method like Bresenham. The idea is simply to prune your path of unnecessary waypoints. Explain the code you used to accomplish this step.

    - In planning_utils.py file, at line 5.
    - I find consecutive 3 points in a straight line, and then remove point in the middle.

### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.


