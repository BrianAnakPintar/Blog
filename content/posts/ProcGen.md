---
title: Creating Procedurally Generated Rooms!
date: 2024-01-06
draft: false
hideMeta: false
tags: ["Party Game", "Unity"]
ShowCodeCopyButtons: true
ShowWordCount: true
math: true

---

In the process of making a party game, I notice that most scenes and minigames that I make typically requires the creation of a room. Sometimes, each scenes and minigames require different room sizes depending on their gameplay, therefore rather than creating a room prefab, how about I create a tool that automatically generates a room for me. It sounds pretty cool in my opinion, so let's do it 😎.

# The Task
Create a general internal tool in Unity which will procedurally generate a room along with various features based on specified parameters. This tool should be able to work in the Scene View using `Custom Editors` along with the Game View during Runtime.

# Implementation
Since I have used a modular 3D model kit to build most of my scenes, I will reuse them here. To keep things simple, I will use a grid system which means the map will be in the form of an $n \times m$ matrix, in which $n,m \in \mathbb{N}$.

First, consider the following $n \times m$ matrix,
$$\begin{bmatrix}
    a_{1,1} & a_{1,2} & \dots & a{1, m}\\\\
    a_{2,1} & a_{2,2} & \dots & a{2, m}\\\\
    \vdots & \vdots & \ddots & \vdots \\\\
    a_{n,1} & a_{n,2} & \dots & a_{n,m}
\end{bmatrix} $$
we will use this matrix as our Grid representation. Do note that while we are representing this as a Matrix, in our implementation we will not be storing this data anywhere at all, this is just an abstraction which we use to ease our understanding.

Now, we will go through each elements of our matrix and then proceed to fill each elements with our desired objects. First notice that we would like to place a floor. This is a simple task which we can do using the following code.

```c#
public void GenerateEmptyRoom()
{
    ...
    tmp = new GameObject();
    for (int x = 0; x < n; x++)
    {
        for (int z = 0; z < m; z++)
        {
            Vector3 pos = new Vector3(x * OFFSET, 0, z * OFFSET);
            // Create Floors.
            Instantiate(floor, pos, quaternion.identity, tmp.transform);
            
            ...
        }
    }
}
```
Next, we want to create our walls. For the sake of generality, we typically only need the edges and corners to have walls, notice that in our matrix this is when one of the element index is at 0, or at $n$ or $m$.
```C#
public void GenerateEmptyRoom()
{
    ...
    // Check if we should produce wall.
    if (x == 0 || z == 0 || x == n - 1 || z == m - 1)
    {
        ...
    }
}
```

Next, since we want to adjust the rotation of the wall based on the current element position, thus to keep our code clean we will use a helper function,

```C#
public void GenerateEmptyRoom()
{
    ...
    // Check if we should produce wall.
    if (x == 0 || z == 0 || x == n - 1 || z == m - 1)
    {
        ...
    }
}

private void WallChecker(Vector3 pos, int[] loc)
{
    if (loc[0] == 0) 
        GenerateWall(pos, 0);

    if (loc[1] == 0) 
        GenerateWall(pos, 1);

    if (loc[0] == n - 1) 
        GenerateWall(pos, 2);

    if (loc[1] == m - 1)
        GenerateWall(pos, 3);
}

```
Notice that the `WallChecker` function uses `if` statements rather than `else if`, this is because we will *abuse* the fact that it will continue checking to deal with corner walls. Now for our Generate Wall function, here we want to generate random walls with different probabilities,

``` C#
private void GenerateWall(Vector3 pos, int rot)
{
    /* Choose one of 3 wall types. [Normal, Shelves, Window]
        
        WALL PMF. Let X be an R.V with values 1, 2, 3 where each correspond to the 3 wall types respectively.
        I will hard code these values in. Of course we can generalize them and set to variables but I'm too lazy rn.
        
        P(X = 1) = 0.8
        P(X = 2) = 0.15
        P(X = 3) = 0.05
    
    Since no probability is less than 0 and these probabilities all add up to 1 then it's a valid PMF.
    
    */

    int wallType;

    float randomNum = Random.value;

    if (randomNum < 0.05f)
    {
        // Window wall
        wallType = 2;
    } else if (randomNum < 0.2f)
    {
        // Shelve wall
        wallType = 1;
    } else
    {
        // Normal wall
        wallType = 0;
    }

    Quaternion rotationStyle = Quaternion.identity;
    if (rot == 0)
        rotationStyle = Quaternion.Euler(0f, 90f,0f);
    else if (rot == 1)
        rotationStyle = Quaternion.Euler(0f, 0f,0f);
    else if (rot == 2)
        rotationStyle = Quaternion.Euler(0f, 270f,0f);
    else if (rot == 3)
        rotationStyle = Quaternion.Euler(0f, 180f,0f);
    Instantiate(walls[wallType], pos, rotationStyle, tmp.transform);
}
```
# Result
To ease debugging and the showcase I will create a CustomEditor to run these functions! Now, let's see the result so far,
[![roomgen.gif](https://i.postimg.cc/FzkFKDrv/roomgen.gif)](https://postimg.cc/sGyC6PGH)
It seems that everything is working perfectly fine. Next, we will proceed with creating decorations to the room and implementing the generated board tiles to these things.

However, this post is a bit too long therefore I shall continue some other time.
