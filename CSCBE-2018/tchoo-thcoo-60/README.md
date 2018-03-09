# Cyber Security Challenge Belgium: Tchoo-Tchoo-60

**Category:** Web Security
**Points:** 60
**Solves:** 29
**Description:**

> (missing description)

## Write-up

I do not have the description anymore but it said something along the lines of *Thomas has lost data about his train, can you help him get it back?*

the full site can be seen [here](https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/tchoo-tchoo-60/full_site.png "https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/tchoo-tchoo/full_site.png")

![index](https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/tchoo-tchoo-60/index.jpg "index")

When visiting the website we are greeted by a swagger, I never would have thought to see a swagger in a CTF!
The challenge is pretty straightforward, get Thomas' train information.
First we create a user to get an apikey
`respone body`
```{
  "apikey": "0a39db4c-ea0a-4115-81e0-6369a6400077",
  "name": "cscbe"
}```

The next step is ask info about the top trains, see what's going on.
`respone body`
```[
  {
    "name": "Thomas",
    "position": 1
  },
  {
    "name": "pompbaksmoel",
    "position": 2
  },
  {
    "name": "Robin",
    "position": 3
  },
  {
    "name": "TrainBiz",
    "position": 4
  },
  {
    "name": "Hogwarts Express",
    "position": 5
  }
]```

Creating a train gives us 
`response body`
```
{
  "id": "0981be8db35498a5acc8c5cbfc26970842905d18",
  "trainnumber": 4626,
  "name": "supertrain"
}```

Now we need Thomas' train data, but what is his id? Can we figure out how an id is made?
After some tinkering it is clear that the id is SHA1(trainnumber+trainName). Now we just nead the right trainnumber and we should be able to get Thomas' information.

Luckily his trainnumber was three so we didn't have to try alot

`response body`
```
[
  {
    "id": 74,
    "sensordata": "CSCBE{Choo_choo_MotherBlobber}"
  },
  {
    "id": 73,
    "sensordata": "CSCBE{Choo_choo_MotherBlobber}"
  },
  {
    "id": 72,
    "sensordata": "CSCBE{Choo_choo_MotherBlobber}"
  }, ......
  ```