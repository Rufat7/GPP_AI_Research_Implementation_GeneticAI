# Genetic algorithm

### Demonstration

![Level1Reached](https://user-images.githubusercontent.com/76409612/103140693-0da2d980-46ea-11eb-86e1-c0248a9e5338.gif)


# What is it?
**Genetic algorithm** is a problem-solving solution inspired by the process of **natural selection**. It is commonly used to generate high quality solutions and to search problems by using methods such as **selection** , **crossover** and **mutation**. There is a number of steps to take to ensure correct implementation. 
First of all you need a starter population. The population depends on the nature of the problem, but usually it contains hundreds or thousands of individuals. 
Each individual has a set of properties **(genetics)** which will be **mutated** and slightly altered based on the results of the previous generations. Usually the algorithm starts from a population of **randomly generated individuals** and is an iterative process , where each iteration is called a **generation**. 
Every generation the performance of each individual of that generation is evaluated. This performance is usually the value of the objective function. The better the performance the more chances of succeeding there is for the next generation. The individuals that performed better than others are selected for breeding of the **next generations**. Their genetics are slightly altered to form the new generation. 
The new generation is then used in the next iteration of the algorithm. Traditionally , the algorithm stops when either a max number of generations has been reached or the problem has been solved. 
A typical genetic algorithm requires a genetic representation of the solution domain and a performance function that evaluates how well the individuals did in this iteration.
This generational process is repeated until a termination condition has been reached. Common terminating conditions are:

*	A solution is found that satisfies minimum criteria
*	Fixed number of generations reached
*	Allocated budget (computation time/money) reached
*	The highest ranking solution's fitness is reaching or has reached a plateau such that successive iterations no longer produce better results
*	Manual inspection
*	Combinations of the above



## Implementation
The project is written in **c++ using SDL framework**.
Classes made for the implementation : 

	-AIGenetics class
	-AIPlayer class 
	-AIPopulation class 
	-Obstalce class
	-Game class 
	-Main class 
	-Some helper classes 

## How does it work :

#### **1st part** :
**Basics :**

First of  all the **AIPlayer** class was made to have a moving player. Afterwards I added **AIGenetics** class that holds information about the (randomized) directions (list) and amount of moves that the AIPlayer took. Then I made **AIPopulation** class that is kind of a manager of AIPlayer class and that could only spawn a given amount of AIPlayers and update and draw them. So right now the AIPlayers randomly move without taking the goal into consideration. 

![Basics](https://user-images.githubusercontent.com/76409612/103140830-d8978680-46eb-11eb-87a1-4d7886141a8b.gif)

#### **2nd part** : 
**Starter functionality :**

So right now I have randomly moving AIPlayers. I added death state to the AIPopulation, that kills AIPlayer which has taken more amount of moves (that is cached in their AIGenetic) than the direction list size. And made it so the game ‘restarts’ if all AIPlayers are dead. For the next step I added **CalculatePerformance()** to AIPlayers. This function is pretty simple as it just takes the distance between AIPlayer's position and the goal's position. I also made it so the players activate a Boolean when they reach the goal. So right now I have everything that I need to move onto the next step.

#### **3rd part** : 
**Working AI :**

After the first generation we have to make the **next generation**. First thing I did is to add a **Breed()** function to the AIPlayer class which copies the data from its AIGenetics and returns a new AIPlayer copy with that information. 
Now we need 2 more functions that will make this generation based genetic algorithm work. First one is the **Selection()** function. In this function the first step is to determine which **parent** from the last generation did the best, so had the best performance score. Afterwards I Breed that best parent and add its child to the first element of the AIPlayer list and set it as the best player to indicate it when I draw it for more visual feedback. After that I Breed that same parent and add them all to the list of AIPlayers. So right now we have a list of AIPlayers that have exactly the same information as the best player of the previous generation. This is not good because this way all of the AIPlayers will move in the same direction with the same amount of moves. So we have to **mutate** them now. This is the last function we need. In AIGenetics we add this function. Here we take a random number inside of a for loop and if this number is smaller than the mutationRate we **“mutate”** that specific direction and change it (randomly). My default mutationRate is 1%. After adding this function we add a **MutateNextGen()** function to the AIPopulation that mutates the whole list of AIPlayers. Afterwards in the Game **Update()** function we call these functions if the AIPlayers are dead. 
So now we have a working AI using the genetic algorithm. The AI basically learns by the information it gets from it's parent and also mutates a bit to explore new paths and reach the goal.

![WorkingAI](https://user-images.githubusercontent.com/76409612/103140813-a71ebb00-46eb-11eb-854d-effea5e8a184.gif)

#### **4th part** : 
**Encountered Issues :**

Now that we have a working AI, I added an **Obstacles** class that can spawn obstacles which can damage you or change your direction when you overlap with it (you specify this with a bool). They can also move in the given range if you specify it. I added this for design and testing purposes. To see how the AI would react in different environments and situations. First I started with simple levels and gradually built it up and made it harder to complete. While testing the AI in differenct levels I encountered some issues or behavior I didn't like.
* **The first thing** that bothered me is that when the goal is in a corner right behind an obstacle the AIPlayers keep going there in a straight line and can’t reach it because the performance of a player right behind the obstacle is shorter than any other.
* **The second** thing that I disliked is that right now they just mutate with random (angle) directions instead of taking into account the parents angle and slightly changing it. 
* **The last thing** I didn’t like was the fact that when the AI finds a path it doesn’t shorten it out and makes it faster to reach. It just took the same path over and over again.

#### **5th part** : 
**Solution to the issues :**

* The first thing I wanted to fix is that the AI should explore more directions (mutate more) if it doesn’t reach the end goal. So I alternated the MutateNextGen() function in my AIPopulation where I call AIPlayers.MutateNextGen() ,which mutates them. But in AIPopulation I pass the mutationRate to that. So what I did was **increase** the mutationRate by 0.5% each time the AI can’t reach the endGoal. When I tried to run this it was working fine at the start but after a while it reached 70-80% , and it looked like the AI was always randomly moving (which he was). So I limited this increase in mutationRate. Now it can’t exceed 15% and once it finally found the goal I turn the mutationRate back to default value which is 1% because at this point we don’t need more mutations than usual. 

**BEFORE FIX** Generation 40 (mutation always 1%).

![Problem1](https://user-images.githubusercontent.com/76409612/103141361-9bcf8d80-46f3-11eb-9973-b8d9bf4e1e38.jpg)

![Problem1](https://user-images.githubusercontent.com/76409612/103140982-2ca36a80-46ee-11eb-8937-e4f6efa937a8.gif)

**AFTER FIX** Generation 40 (mutation increased to 15% that's why it finally found the goal, after finding the goal, mutation changed to 1% again).

![problem1Solved](https://user-images.githubusercontent.com/76409612/103141363-9eca7e00-46f3-11eb-97cd-c4abe6d90d6d.jpg)

![ProblemSolved1](https://user-images.githubusercontent.com/76409612/103140941-7e97c080-46ed-11eb-8ade-392d493dd421.gif)

* The next problem was that when mutating it took a random direction. So with **acos()** I get back my oldAngle from my current Direction.x which is a copy of direction of the parent from previous gen and added an offset to it between -90 and 90 degress. By doing this the new angle of mutated direction of the AI was not completely random but was actually altered by using the (angle) direction of the parent.

**BEFORE FIX** Generation 10 (mutated directions are randomized and sometimes it gives bad mutation and the mutation is wasted).

![problem2](https://user-images.githubusercontent.com/76409612/103141392-fbc63400-46f3-11eb-9c2b-15250b8d2b83.jpg)

![Problem2](https://user-images.githubusercontent.com/76409612/103141090-9112f980-46ef-11eb-8c3e-3efa72e1f4f3.gif)

**AFTER FIX** Generation 10 (mutated directions are altered by taking into account "parent's" direction and it is more precise)

![problem2Solved](https://user-images.githubusercontent.com/76409612/103141393-fe288e00-46f3-11eb-9d19-7f2c6735f200.jpg)

![Problem2Solved](https://user-images.githubusercontent.com/76409612/103141092-9708da80-46ef-11eb-97e4-53e177f091bc.gif)

* And the last optimization I did was for **shortening** the path after the AI reached the goal at least once. For this I changed the CalculatePerformance() of the AIPlayer by making a new calculation when that specific AIPlayer has reached the goal. This calculation is based on the amount of moves it took to reach the goal, so the less movement it took the better the performance will be. And also in the AIPopulation ,in the SelectBestParent() function which previously returned the parent with the max performance I check if that parent has reached the goal. If yes, then I change the **minimumAmountOfMoves** that a AIPlayer can take before it dies. By doing this I “kill off” the AIPlayers that took more steps to reach the goal than the previous best parent. And gradually this will shorten the path that the AIPlayers are taking to reach the goal.

**BEFORE FIX** Generation 20 (when the AI finds the goal ,it will never try other paths and will always take the same one)

![problem3](https://user-images.githubusercontent.com/76409612/103141318-0c29df00-46f3-11eb-8cac-b8dc4df3afdd.jpg)

![problem3 1](https://user-images.githubusercontent.com/76409612/103141275-8efe6a00-46f2-11eb-8279-38318117a0c3.jpg)

![Problem3](https://user-images.githubusercontent.com/76409612/103141238-14354f00-46f2-11eb-8348-87bf99de189c.gif)

**AFTER FIX** Generation 20 (when the AI finds the goal ,it still looks for other shorter paths)

![problem3Solved](https://user-images.githubusercontent.com/76409612/103141277-9160c400-46f2-11eb-9db3-327a2c81c9a3.jpg)

![problem3Solved 1](https://user-images.githubusercontent.com/76409612/103141319-0e8c3900-46f3-11eb-8e07-740e38e68000.jpg)

![problem3Solved 2](https://user-images.githubusercontent.com/76409612/103141279-958ce180-46f2-11eb-9212-0e73c12d6bc6.jpg)

![Problem3Solved](https://user-images.githubusercontent.com/76409612/103141239-17303f80-46f2-11eb-8f4b-956256a3d1fe.gif)

#### **6th part** : 
**Making fun level :**

Now that the whole genetic algorithm AI is working in a way I wanted it to work, I added a level and challenged the AI. I have always been a fan of a game called ‘The hardest game ever’ , so I made a classic level with moving obstacles inspired by this game for fun. You can see the level at the start of this ReadME.

## RESULT

After implementing the above mentioned functions and behavior, the **AI** does what I wanted it to do and uses **a genetic algorithm** to do it. The main goal was that the AI reaches the given position. On average (depending on difficulty of the level) it reaches the given goal in 15-30 generations. Without any obstacles it finds the goal in 2-5 generations. On some cases where the level is built too difficult it might take too long or get stuck but that is of course because I didn’t implement further AI learning. 

As a conclusion I really enjoyed working on this project and this topic. I always wanted to test this out and see how the AI actually ‘learns’ and becomes smarter each iteration. I am definitely planning on doing more research and working on more projects like this in the future that involves AI learning. 

## References

https://en.wikipedia.org/wiki/Genetic_algorithm#Methodology

https://www.youtube.com/watch?v=1i8muvzZkPw&ab_channel=MATLAB

https://www.sciencedirect.com/topics/engineering/genetic-algorithm

https://towardsdatascience.com/genetic-algorithm-explained-step-by-step-65358abe2bf

