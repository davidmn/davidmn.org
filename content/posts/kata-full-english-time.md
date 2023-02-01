---
title: "KATA: Full English Time"
date: 2023-02-01T00:00:00.00Z
draft: false
---

_At work, we have a weekly Code Gym. Someone will bring a kata/problem along and we'll split up to tackle it. Usually pairing across normal team boundaries. Pretty normal stuff. We have been doing this for two years now and feel like we've run out of kata. So we've been trying to invent new ones. I am going to post ones I've created which should hopefully be useful to someone._

## Full English Time

You are given an int representing the number of seconds elapsed since midnight (12:00am, 00:00, 23:59+1min). You must return a string that contains a text based English representation of the time.

### Examples

```
int   | string
------|--------------------------------------------
0     | Midnight
60    | One minute past midnight
53700 | Five minutes to three
53708 | Four minutes and fifty two seconds to three
```

## Extension 1

Can you do it without using a library to help you with the time parsing? (i.e. System.DateTime and its friends are banned)

## Extension 2

Is "Five minutes to three" the morning or afternoon? Or maybe the night? Take the following definitions and extend your solutions.

```
Time Range           | Name
---------------------|------------
00:00:00 -> 05:59:59 | Night
06:00:00 -> 11:59:59 | Morning
12:00:00 -> 16:59:59 | Afternoon
18:00:00 -> 23:59:59 | Evening
```

So now you might output "Four minutes and fifty two seconds to three in the afternoon"

## Extension 3

We're not normally so precise, can you round your times to the nearest 5 minute?
