# Reflection Questions

## 1. For every function you wrote (TODO 1 to 19), explain in your own words what it does and give its time complexity in terms of n, the number of scenes. One short paragraph each.

### TODO 1 — Scene()
Scene() is a constructor that creates a scene and initializes numChoices_ choices in an array to nullptr. It also checks if the scene has more than 0 choices. Since each scene only has at most 3 choices, Big O is O(1).

### TODO 2 — ~Scene()
~Scene() is the destructor for a Scene. It deletes every child scene stored in the array, triggering their destructors and deleting the scene's descendants. It finishes by deleting the choices_ array and data to prevent a memory leak. Big O is O(n) because it visits every descendant scene to delete it.

### TODO 3 — isEnding()
isEnding() checks whether a scene is an ending by checking whether numChoices_ equals 0. Big O is O(1) because it's a single comparison.

### TODO 4 — getChoice()
getChoice() returns the pointer to the scene at a certain index and checks for invalid index inputs. Big O is O(1).

### TODO 5 — setChoice()
setChoice() puts a pointer to the next scene in the choices_ array and checks if the index is valid. Big O is O(1).

### TODO 6 — countScenes()
countScenes() recursively counts all scenes at and below a selected scene. So if the selected scene is the root, it counts the total number of scenes in the story. It first checks if the current scene isn't nullptr. Since it traverses to each scene, its Big O is O(n).

### TODO 7 — countEndings()
countEndings() recursively counts how many ending scenes are in the tree below the selected scene. It checks if the current scene is nullptr, and when it reaches an ending, it returns 1 to increment/count endings. Each scene is visited, so Big O is O(n).

### TODO 8 — height()
height() finds the maximum number of choices you can make from a scene before hitting an ending (the height of the tree). It recursively finds the child with the largest height and increments up from its ending to find the current scene's height. It visits each scene once, so Big O is O(n).

### TODO 9 — clear()
clear() deletes the root and then sets it to nullptr. This triggers the destructor, which will recursively delete the story tree. Each scene is visited, so Big O is O(n).

### TODO 10 — load()
load() opens a story text file or returns false if it fails. It then reads the START_HP value and returns false if it fails. It then calls readScene() and setRoot() to put the root in tree_ (again, return false if it fails). Since readScene is called, which reads and builds every scene, Big O is O(n).

### TODO 11 — readScene()
readScene() recursively reads and builds a scene and every other below it. It reads the scene's data, creates a Moment and Scene, then recursively moves to every child to do the same. If it fails, it deletes the current scene, which also deletes all its descendants. Since it builds every scene, Big O is O(n).

### TODO 12 — askChoice()
askChoice() asks the player to enter a choice between 1 and the number of choices in the scene and checks for valid inputs. Big O is O(1), assuming the user inputs a correct option.

### TODO 13 — play()
play() actually plays the game. It starts the player at the root scene and changes the HP, loot, and items every time a new scene is visited. It also checks for death (HP is 0 or less) or an ending. It also handles printing scenes and their choices. Big O is O(n²) because if a story tree has one path, play() visits n scenes and, in the worst case, searching through the bag linked list is another O(n). Together, Big O becomes O(n²) at worst.

### TODO 14 — printStats()
printStats() displays the total number of scenes, number of endings, and height of the story. It calls countScenes(), countEndings(), and height(), which each take O(n). Three O(n) traversals are still O(n) overall.

### TODO 15 — printSpoilers()
printSpoilers() calls printEndings() on the root to print every path to an ending. Because printEndings() visits every scene, Big O is O(n).

### TODO 16 — printEndings()
printEndings() recursively finds every ending and prints the sequence of choices needed to get to it. It travels down the tree and adds the choices of every scene to paths, which are printed when an ending is reached. Every scene is visited, so Big O is O(n).

### TODO 17 — greedyRun()
greedyRun() plays a playthrough like play(), except it checks the choices of the current scene and automatically chooses the one that gains the most loot (default to lower choice number if there is a tie). Big O is O(n) since a story tree could be a single branch, meaning greedyRun() would visit every scene.

### TODO 18 — bestLoot()
bestLoot() recursively checks every possible ending and its path to find the best surviving path that ends with the most loot. If a path ends in death, it returns -1. When it reaches an ending, it returns the loot to compare to the previous highest loot path. It eventually returns the highest loot amount and saves its path in bestPath (later used in optimalRun()).  Every scene is visited, so Big O is O(n).

### TODO 19 — optimalRun()
optimalRun() calls bestLoot() on the root to get the highest loot and its path. If bestLoot() returns -1, it says that no path survives. It then prints the path and loot. Since it calls bestLoot, which has a big O of O(n), and only prints, which is O(1), the big O of optimalRun() is O(n).

## 2. Why does each scene allocate exactly numChoices slots instead of declaring Scene<T>* choices[3]? What does an ending hold in choices_, and why?

Each scene allocates exactly numChoices_ slots to save memory. There is no reason to allocate slots for choices_ that won't be used because the scene does not have 3 choices; they'd only be taking up memory unnecessarily. An ending holds a nullptr since it has 0 choices and therefore does not pass the if check in the constructor for choices_ to be allocated an array.

## 3. Walk through your Scene destructor. What gets deleted, and in what order? What would go wrong if choices_ were deleted first?

The Scene destructor first loops through the choices_ array to delete every child scene. This also triggers each child's destructor, which will recursively delete their children. The result is deleting all the descendants of the scene the destructor is called on. It then deletes the choices_ array storing the pointers and lastly deletes data_ to delete the current scene's data. The order matters because we need the pointers to access the children. For example, if we had deleted the choices _ array first, we would lose access to the child scene since we don't have their pointers. This would result in a memory leak.

## 4. Show the path greedy took and the path optimal took on your story, with the loot for each. Explain why greedy lost. If greedy did not lose, change your story until it does, and explain what you changed.

In my story, the greedy's path was 3-2-1, which results in 12 loot, but the player dies. Optimal took path 3-1-1, which collects 10 loot and survives. Greedy lost because it only considers immediate loot gain in the current choices, which can lead it to a game over. It doesn't recursively check future choices to find a surviving ending like optimal does before choosing the highest loot choice.

## 5. bestLoot tries every path, yet it runs in O(n) time. Explain why it is not exponential, using what you know about paths and leaves in a tree.

Because bestLoot() works recursively to travel every path to its leaves and makes another path at parents of multiple children. It visits each scene only once. Each scene requires constant work, so Big O becomes O(n) because we visit "n" scenes. Big O could be exponential if bestLoot restarted at the root and traversed down every path, visiting every parent more than once.

## 6. Which List methods does your game use, and why? What does one search cost as the bag grows?

It uses the search(), addFront(), size(), and print() methods from List.h. Search() is used in play() to make sure we don't add a duplicate item to the bag. AddFront() is then used if a duplicate item is not found to add the Item to the bag list. Size() is used at an ending or game over to print the item count, and print() is used after size() to print the bag items. The Big O cost of Search() is O(n) because the worst-case scenario is that the item we are looking for is at the end or not even in the bag, so it iterates through every item. The actual cost of search() grows as more items are added because there are now more items to iterate through. Though there is still the possibility that the first item was the one we were searching for, the cost becomes 1.
