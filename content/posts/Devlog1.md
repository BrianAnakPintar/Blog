---
title: "Making an Online Party Game"
date: 2023-12-24
# weight: 1
aliases: ["/devlog1"]
tags: ["Party Game", "Unity"]
hideMeta: false
draft: false
hidemeta: true
searchHidden: false
ShowBreadCrumbs: false
ShowWordCount: true
ShowRssButtonInSectionTermList: false
ShowCodeCopyButtons: true
math: true

fuseOpts:
        isCaseSensitive: false
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 0.4
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary", "content"]
---

> Surely making an online party game isn't that hard. \
> \- Me, 2023.

Welcome to the first entry of the devlog for our online multiplayer party game! The game is heavily inspired by other popular party games such as Super Mario Party and Headbangers. This specific devlog will be more technical than the others so please bear with me!

I believe this devlog will serve a great way to see the progress on the game as well as a good way to express my thoughts and design decisions. Moreover, I have been wanting to nerd out about some technical stuff so I think this is a great opportunity for that! In this part, we will be using Unity as our game engine and Netcode for GameObjects as our networking framework.

## Designing a Turn-Based Board Game System. {#}
We start with one of the core mechanic of our game, the board game! First, we must decide on how we wanted to structure our board. There are many different ways we can choose to structure our board, for example we can create a board with different paths or just a simple one like monopoly. 

It would make sense to represent the board using [Graphs](https://en.wikipedia.org/wiki/Graph_(abstract_data_type)) especially if we wanted to scale this game, as it would allow for interesting maps and customizable routes in the game. However, for the sake of simplicity and since this is just a small project, we will go with the easy route. A monopoly board! And since this type of board is very simple, rather than using things such as LinkedLists, we can simply use an Array to represent this data type. To be more precise, we will use a *Circular Array*.

To start, we will store our board as an array. Before that since we want to store tile informations in the board, it would make sense for us to make a Tile Class.
```c#
public class BoardTile
{
    ...
    public TileType tileType;
    public Vector3 tilePosition;
    private int points;
    ...
}

public class BoardGameSystem : NetworkBehaviour
{
    ...
    public const int MAX_BOARD_TILES;
    private BoardTile[] board = new BoardTile[MAX_BOARD_TILES];
    ...
}
```

Now that we have a way to represent our board, we proceed with creating the turn-based system for each player. There are definitely a lot of better ways to design this, but the way I decided to proceed is to create a counter which decides which player gets to go next. This is a simple task which we can accomplish using Network Variables. Moreover, we will also use Network Variables for representing the dice roll.

Now at this point of the project although I had tested the code by checking the variables, my horrible attention span was not able to make sure that it is actually working properly without those pretty visuals. Therefore, in this part I decided to actually render the board. Here, I generate a board using some math! This is good because by using this method, I am able to create a board with any arbitrary length, thus reducing the time for play testing when in the future 😎.

[![Board.png](https://i.postimg.cc/Tw4YGZXQ/Board.png)](https://postimg.cc/1nDZr7XN)

Now that we have a board, I also decided to create the player. However, currently there is no animations implemented. Animations are vital if we want this game to look and feel good. Therefore, I will do exactly that in my spare time.