---
layout: post
title:  "Atomic Habit - Make it Difficult"
date:   2020-07-02 15:00:00
categories: Programming Swift Productivity
---

Book "Atomic Habits" talked about four laws of behavior changes:
1. Make it obvious / make it invisible
2. Make it attractive / make it unattractive
3. Make it easy / make it difficult
4. Make it satisfying / make it unsatisfying

All 4 laws have interesting applications. Today I want to focus on the 3rd law, make it easy / difficult.

Relying on self discpline and self control sometimes can be difficult especially with WFH. I kept wondering what I should order for lunch, what video / music to listen to next. All those distractions are detrimental to productivity.

I decided to turn to technology to make certain distraction impossible. Love games? Set up parental control so I can't play games during certain periods. Want to watch videos on youtube? Block websites through forest on Chrome!

On safari, there is no famous app to do website blocking so I decided to make one. By following several tutorials online, I created a simple safari [extension](https://github.com/wwei10/BlockSite) to one click block all websites on safari for 30 minutes. I also spent some time learning google cloud and created a redirect page showing countdown [timer](https://blocksite-gcloud-app.wl.r.appspot.com).

Quite interesting project. Several learnings from developing safari extension:
1. Initial setup of developing extension took long time. Code signing was a huge problem for me. `codesign -vvvv <filepath>` helped me rule out codesign related problems several times.
2. Sometimes running safari extension from xcode doesn't work. I had to clear build, rebuild, then run the app extension again to see the change reflected during testing.
3. Safari doesn't support beforeload trigger. Also spent quite sometime trying to figure that out.

Overall quite nice experience, glad I learned something new! :) 
