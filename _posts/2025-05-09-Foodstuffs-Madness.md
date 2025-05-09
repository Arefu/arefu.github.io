Well, it's one of those moments again where I can't figure out how to explain the thing I'm trying to do. What a crazy world. I originally set out to make an app that let me keep track of my kitchen, and instead, it has blown out into a full-blown inspection of Foodstuffs' API for both Pak'nSave and New World.


# Step One 
Like any good story, we begin at the start—or is it the beginning? Oh well, who cares.

I started our by bringing up the [Pak'nSave](https://www.paknsave.co.nz/) website, thinking that would be a decent place to start doing what I want. After all, it's half of the service I'm wanting from my app—the grocery side.
Everything seemed normal to start with, I saw some [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)'s go out which looked hopeful, oh how naive I was. 

![Screenshot 2025-05-09](https://github.com/user-attachments/assets/dcccb288-a09a-467d-8017-29201489a75c)

Let’s break that down a little more. We’ll start with the key one I was most interested in: AutoComplete. This fired when I searched for an item in the shop’s search bar. Well, what do you know about that?

[Recording 2025-05-09 221115.webm](https://github.com/user-attachments/assets/faeff742-1b3d-49de-bf8a-c8b4396f51dc)

Simple, right? Well, not quite—you surely noticed the pessimistic tone. Upon looking at the Request Headers, I can see some pretty funky looking cookies that don't look exactly nice to try recreate, not exactly hard, it's just more what sort of sick individual puts JSON in a cookie?

![Screenshot 2025-05-09 221822](https://github.com/user-attachments/assets/5977c843-1953-474e-b947-bff7a55940e7)

Screenshot 2025-05-09 221822

C’mon Foodstuffs, really? What’s with this? You’ve got the data inside `browser_nearest_store` duplicated across three other keys. Oh, and now that you’ve got me started, there’s also `Region` what’s that about? Can’t you get the region from the StoreID in any number of the cookies already storing that?
