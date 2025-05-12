Hey, sorry, come in, I'm still checking my notes, Oh I see we had just captured our first traffic on mobile, it all looks pretty normal from here aside from their odd choice of domain name `fsniwaikato.kiwi`, I'm guessing it breaks down like so `Food Stuffs North Island Waikato`, not sure why they care so much about
waikato in particular, but hey, who am I to judge.

# Part One - [Embracing the Marble](https://www.youtube.com/watch?v=z8e1XElIOwM)
I guess when we're trying from a fresh install of the app the calls are different, as the last post was captured with me signed in, so we can see a much more minmal feed of calls, I'm interested most in `/prod/mobile/user/login/guest` because I suspect if they're calling something to give me specific information being logged in (MIght be foreshadowing), I take it they're 
also going too when I am not. 
![Screenshot 2025-05-12 192459](https://github.com/user-attachments/assets/f325daa9-8837-4bb8-ad98-4be72eb5c312)

Well, would you look at that, it's a [JsonWebToken](https://en.wikipedia.org/wiki/JSON_Web_Token) and associated information, I guess we should decode that and see what we come up with?
Oh, some little bits of information there, it's a pretty minmal token only really including what's required and not that much to go with, but hey, it's still pretty cool to see them use something proper for a change... Yeah, I'm still holding a grudge over the JSON in a Cookie from the last post.
![image](https://github.com/user-attachments/assets/59330f93-e40c-42ec-bc24-6fdd45915e60) ![image](https://github.com/user-attachments/assets/d7490062-6969-44d4-9bf6-f58b98b0ddf3)

I guess, we should note that we have a token and keep an eye out for requests that need it again in the future, but for now, there are some other notable endpoints, like `/prod/mobile/v1/upgrade` that gives you a nag if you set the version of the app to low
![image](https://github.com/user-attachments/assets/dc58c5f8-a6fe-486d-9c10-41bd67a4075b)
![image](https://github.com/user-attachments/assets/08c05644-fd08-4f4c-8823-264ee3e09660)

One of the more interesting ones is `/prod/mobile/store/physical`, it has the Token we got before, but it has it in the `access_token` header, not the `Authorization` header with the `Bearer` format so commonly used, okay, I don't know about you, but I want to have a coffee with the backend guy, and ask, you good mate? Are they making you drink Gregg's Cofffe or something?
![image](https://github.com/user-attachments/assets/5fa3c70c-5359-42ff-9ba3-a52f4ad69496)
Keep an eye on this list, at the time of writing this it hasn't got the Rolleston store, but that will be an intereting time to see if they have any fields not used, or if there are hidden endpoints for stores not ready to be open yet?


# Part Two - Visualizing the Concept

We have only really been on the home page of the app so far, so let's take a little detour to [Easter Eggs](https://www.androidauthority.com/android-easter-eggs-818694/), did you know that Pak 'n Save *and* New World both support the ol' triple tap the version number trick? Well, now you do! It opens a nifty little Debug Menu that must be used for internal testing, I take 
it some of this is niche Android knowledge that don't really have as I've only ever made stuff for Desktop, but hey, I still think it's pretty cool to see stuff like this in mainstream applications
![ezgif-4b6a0ae7892595](https://github.com/user-attachments/assets/3dcd9a78-f90f-4ba1-b0eb-0a030b11e5a9)


We should also mention that you can change your API build you're hitting? I wouldn't, it's like Pak 'n Save is the gift that keeps giving with weird app features you wouldn't expect. Though thankfully if you do change your environment it does alter the API's URL in some way, so they are at least branching their code, though from my testing it appears all endpoints behave the same,
so my guess would be that they have merged all branches and don't expect to be changing code in the short term. Lucky them.
![Screenshot 2025-05-12 194434](https://github.com/user-attachments/assets/067ca332-9c6e-494a-905e-76bd5d67f6ce)
