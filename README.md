# SearchAndPaint
A short project for my AI class. Implements random chance for creating "art," and a problem solver for a variation of Russel and Norvig's exercise.

Parking
1. State space size of parking garage
	With n defined as the dimensions of the square grid and the number of cars, and b defined as b <= n obstacles, we can define the state space size for a parking garage. Obstacles can not be placed in the top or bottom row, and they may not occupy an entire row, or the garage will be unsolvable.
	If we were to simply be finding the possible locations of each car, we would only need n2 choose n, as this represents every possible position for n cars on an n*n grid. However, if we consider barriers, knowing that barriers are both immobile and defined in the start state of the problem, we only need to subtract b from the number of potential squares for cars to be positioned in. Therefore, the number of states becomes (n2 - b) choose n.

2. Branching factor of the parking garage
	Branching factor depends on the number of available squares for each car, as well as the number of attendants, m. For every attendant, they may enter a car and move it in one of four directions. They must pick the car from a list – which one they pick is irrelevant. We can define this as n choose m, or the number of potential combinations for occupied cars. For each of these combinations, there are 4m possible moves that can be made.
	Taking this into consideration, multiply 4m by n choose m to get the final value for the branching factor.
	While certain obstacles, such as the edge of the map, other cars, and barriers, decrease this for individual states, the branching factor should only take into account the maximum potential new nodes.

3. Heuristic_dist function
	Heuristic_dist, for the purposes of this project, is a simple hill-climbing algorithm. It returns the distance from each car to their destination, summed. For example, on a 2x2 grid, if the cars are located, in order, (0,0), (0,1), then both cars have to move 2 tiles to reach their destination, resulting in a score of 4. Technically, this would be a “valley-dropping” (I made that up) equation because it’s aiming for the smallest score rather than the highest.
	This heuristic function is definitely an improvement over the other functions, but much of the improvement comes from state trimming – something I had to implement to prevent DFS from eternally iterating. It removes moves where cars bump, where a car hits another car, goes off the edge, where no move is made, and where the problem has already seen a state. This trimmed, for the 2x2 model above with 1 attendant, the largest branching factor from 8 to 3, and had even more trimming for larger models.

4. Driving Summary
	For each algorithm, I decided to test them with two different states – 4 cars, 2 attendants, and 1 barriers, and 4 cars with 1 attendant. This isn’t a perfect test – different algorithms undoubtedly perform better in certain scenarios, and they are designed to have different outcomes – BFS finds a solution as fast as possible (without scoring), while DFS finds the shortest search.
	The results are cataloged below.

4 C, 2 A, 1 B
4 C, 1 A, 0 B
depth_first_tree_search
15.43
42.59
depth_first_graph_search
(.11)
(.92)
breadth_first_graph_search
(.27)
(.99)
best_first_graph_search
.27
.38
astar_search
2.46
.84
	Note that certain variables aren’t cataloged – for example, best_first_graph_search takes roughly 100x as long with 2 barriers as 1. It doesn’t account for these barriers, and so, if it would be better to lose a point by going around, it tries that as the very last thing.
	Additionally, due to the magnitude of the graph being searched, some functions could not find a solution in reasonable time. For breadth_first_graph_search, this is because it’s scanning every single node to find the shortest path. For depth_first_graph_search, I don’t quite understand why it takes so long – theoretically, it should be better than depth_first_tree_search. The times listed in parentheses are given the same parameters but only 3 cars.
	For every function except astar_search, having a barrier was actually better for time complexity. This is due to trimming in my all_moves function – removing an entire tile from play decreases the state space significantly.
	Astar_search is unique in that it catalogs values and depth – it prioritizes short paths and advancing, and is hindered significantly by unexpected barriers. This is seen to a lesser degree in best_first_graph_search – the three other searches have <.5x time taken with one barrier, while best_first_graph_search is ~.7x
	Please note that different position and count of barriers have an major impact on performance. With a second barrier, best_first_graph_search ran once at .32 seconds, and once at 3.86 seconds. Barriers implement randomness, and so they wildly vary performance each time.

Painter
	Painter is a bit of a mess, but it does generate interesting images. It mutates with a mutation% chance, then recombines the first (recombine) elements in (pools) different combinations, with random priority and positioning.
	My fitness function rewards having a high number of colors, as well as a having colors be of similar sizes – if, for example, on a 100x100 grid, there are 2 colors, the best-scoring painting will have 5000 pixels of each color.
	I attempted to have it generate something similar to real images, but unfortunately the random mutations aren’t specific enough to generate good images in reasonable times. I typically wound up with large swaths of dark gray due to the darkness in the image I was testing.
